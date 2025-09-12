# 🎮 学习笔记：Nim 作业（强化学习）

这一课第二个作业是 **Nim** —— 一个看似简单，但背后策略很微妙的小游戏。题目的要求是写一个 AI，它不会预先知道怎么赢，而是靠 **强化学习 (Q-learning)** 通过和自己反复对战，慢慢学会必胜的方法。

------

## 📜 游戏规则回顾

- 桌子上有几堆石子。
- 玩家轮流操作，每次只能从一堆里拿走一个或多个石子。
- 谁拿到最后一个石子，谁就输。

比如只剩 3 个石子，如果是你的回合，最优解是拿 2 个，让对手只能拿最后那个。
 但如果有好几堆，局面就一下子变得复杂了。**这就是 AI 要学会的地方。**

------

## 🧩 Q-learning 在 Nim 里的应用

### 🔹 状态和动作

- **状态 (state)**：当前所有堆的情况，比如 `[1, 3, 5, 7]`。
  - 在代码里要转成 `tuple`，因为 Python 的字典 key 不能用 list。
- **动作 (action)**：一个 `(i, j)`，意思是“从第 i 堆拿走 j 个石子”。
  - 例如 `(2, 3)` 表示从第 2 堆拿 3 个。

### 🔹 Q 值的意义

Q 值就是一张“经验表”，形式是：

```
Q[(state, action)] = 价值
```

它的含义是：在某个局面下走某一步，到底值不值得。

- 如果直接输 → -1
- 如果直接赢 → +1
- 如果游戏继续 → 当前奖励是 0，但会结合未来可能获得的奖励。

> [!note]
>
> **Q 值表就是 AI 的“经验记忆”**：它会记下“在某个局面里做某个动作”到底好不好，数值越大越好，越小越差。经过大量训练，这个表会越来越准确，AI 也就越来越强。

### 🔹 更新公式（核心）

$$
Q(s, a) \leftarrow Q(s, a) + \alpha \cdot \big( (r + \text{best future reward}) - Q(s, a) \big)
$$

理解成一句人话：
 👉 “这一步的价值 = 旧的经验 + 一点点新经验的修正。”

------

## 🤖 AI 是怎么学会的？

一开始 AI 就像个新手小白：它完全随机走，几乎局局都输。
 但是：

- 每盘结束后，它会根据输赢给相关的动作打分。
- 刚开始只有最后一步决定胜负的动作可以被打分，但是后面的游戏可以从未来状态和动作的q值计算当前状态各个动作的q值，玩的次数多了，到最后每个状态和动作都被赋q值。
- 输的动作分数越来越低，赢的动作分数越来越高。
- 经过成千上万盘自我对战，这些分数（Q 值）会逐渐收敛，AI 就能越来越聪明，因为它是根据Q值来做出下一步动作的决策。

到最后，你再跟它对战，就会发现它几乎不会犯错。

------

## 🔀 探索 vs 利用

这里有个挺有趣的问题：**AI 是不是应该永远走“最优解”？**
 答案是：不能。

- **利用 (exploit)**：走当前分数最高的动作。
- **探索 (explore)**：偶尔尝试一下别的动作，说不定能发现更好的。

如果只利用，AI 可能会被困在一个“看似不错但其实不完美”的策略里。
 如果只探索，它就永远不稳定。

解决方法是 **ε-贪心算法**：

- 以概率 ε（比如 10%）随机探索，
- 以概率 1-ε（90%）选择最优动作。

这样就能平衡“尝试新路”和“坚持老路”。

------

## 📝代码实现

> 函数`get_q_value`, `update_q_value`, `best_future_reward`和`choose_action`为本次作业要求实现的核心代码。

```python
import math
import random
import time


class Nim():

    def __init__(self, initial=[1, 3, 5, 7]):
        """
        Initialize game board.
        Each game board has
            - `piles`: a list of how many elements remain in each pile
            - `player`: 0 or 1 to indicate which player's turn
            - `winner`: None, 0, or 1 to indicate who the winner is
        """
        self.piles = initial.copy()
        self.player = 0
        self.winner = None

    @classmethod
    def available_actions(cls, piles):
        """
        Nim.available_actions(piles) takes a `piles` list as input
        and returns all of the available actions `(i, j)` in that state.

        Action `(i, j)` represents the action of removing `j` items
        from pile `i` (where piles are 0-indexed).
        """
        actions = set()
        for i, pile in enumerate(piles):
            for j in range(1, pile + 1):
                actions.add((i, j))
        return actions

    @classmethod
    def other_player(cls, player):
        """
        Nim.other_player(player) returns the player that is not
        `player`. Assumes `player` is either 0 or 1.
        """
        return 0 if player == 1 else 1

    def switch_player(self):
        """
        Switch the current player to the other player.
        """
        self.player = Nim.other_player(self.player)

    def move(self, action):
        """
        Make the move `action` for the current player.
        `action` must be a tuple `(i, j)`.
        """
        pile, count = action

        # Check for errors
        if self.winner is not None:
            raise Exception("Game already won")
        elif pile < 0 or pile >= len(self.piles):
            raise Exception("Invalid pile")
        elif count < 1 or count > self.piles[pile]:
            raise Exception("Invalid number of objects")

        # Update pile
        self.piles[pile] -= count
        self.switch_player()

        # Check for a winner
        if all(pile == 0 for pile in self.piles):
            self.winner = self.player


class NimAI():

    def __init__(self, alpha=0.5, epsilon=0.1):
        """
        Initialize AI with an empty Q-learning dictionary,
        an alpha (learning) rate, and an epsilon rate.

        The Q-learning dictionary maps `(state, action)`
        pairs to a Q-value (a number).
         - `state` is a tuple of remaining piles, e.g. (1, 1, 4, 4)
         - `action` is a tuple `(i, j)` for an action
        """
        self.q = dict()
        self.alpha = alpha
        self.epsilon = epsilon

    def update(self, old_state, action, new_state, reward):
        """
        Update Q-learning model, given an old state, an action taken
        in that state, a new resulting state, and the reward received
        from taking that action.
        """
        old = self.get_q_value(old_state, action)
        best_future = self.best_future_reward(new_state)
        self.update_q_value(old_state, action, old, reward, best_future)

    def get_q_value(self, state, action):
        """
        Return the Q-value for the state `state` and the action `action`.
        If no Q-value exists yet in `self.q`, return 0.
        """
        # self.q is a dict, key is (state, action), value is reward, means in this
        # "state", if take this "action", you will get how much reward
        state = tuple(state)
        if (state, action) in self.q:
            return self.q[(state, action)]
        else:
            return 0

    def update_q_value(self, state, action, old_q, reward, future_rewards):
        """
        Update the Q-value for the state `state` and the action `action`
        given the previous Q-value `old_q`, a current reward `reward`,
        and an estiamte of future rewards `future_rewards`.

        Use the formula:

        Q(s, a) <- old value estimate
                   + alpha * (new value estimate - old value estimate)

        where `old value estimate` is the previous Q-value,
        `alpha` is the learning rate, and `new value estimate`
        is the sum of the current reward and estimated future rewards.
        """
        state = tuple(state)
        self.q[(state, action)] = (old_q + self.alpha * 
                                   ((reward + future_rewards) - old_q))

    def best_future_reward(self, state):
        """
        Given a state `state`, consider all possible `(state, action)`
        pairs available in that state and return the maximum of all
        of their Q-values.

        Use 0 as the Q-value if a `(state, action)` pair has no
        Q-value in `self.q`. If there are no available actions in
        `state`, return 0.
        """
        actions = Nim.available_actions(state)
        # If no available actions, return 0 reward
        if not actions:
            return 0

        best_reward = -math.inf
        # Loop all available actions and return the best action's reward
        for action in actions:
            action_reward = self.get_q_value(state, action)
            if action_reward > best_reward:
                best_reward = action_reward
        return best_reward

    def choose_action(self, state, epsilon=True):
        """
        Given a state `state`, return an action `(i, j)` to take.

        If `epsilon` is `False`, then return the best action
        available in the state (the one with the highest Q-value,
        using 0 for pairs that have no Q-values).

        If `epsilon` is `True`, then with probability
        `self.epsilon` choose a random available action,
        otherwise choose the best action available.

        If multiple actions have the same Q-value, any of those
        options is an acceptable return value.
        """
        actions = Nim.available_actions(state)
        # Return None if no available action
        if not actions:
            return None

        action_q = []
        # In current state, bind available actions with its q value
        for action in actions:
            action_q.append((action, self.get_q_value(state, action)))

        # Pick out the action with best q value
        best_action = max(action_q, key=lambda x: x[1])[0]

        if epsilon:
            # random.random() returns a float value between 0.0 to 1.0
            if random.random() <= self.epsilon:
                # convert set to a list in order to call random.choice
                # epsilon is the probability that we can chose a action randomly,
                # not based on the q value
                return random.choice(list(actions))
            else:
                return best_action
        else:
            return best_action

def train(n):
    """
    Train an AI by playing `n` games against itself.
    """

    player = NimAI()

    # Play n games
    for i in range(n):
        print(f"Playing training game {i + 1}")
        game = Nim()

        # Keep track of last move made by either player
        last = {
            0: {"state": None, "action": None},
            1: {"state": None, "action": None}
        }

        # Game loop
        while True:

            # Keep track of current state and action
            state = game.piles.copy()
            action = player.choose_action(game.piles)

            # Keep track of last state and action
            last[game.player]["state"] = state
            last[game.player]["action"] = action

            # Make move
            game.move(action)
            new_state = game.piles.copy()

            # When game is over, update Q values with rewards
            if game.winner is not None:
                player.update(state, action, new_state, -1)
                player.update(
                    last[game.player]["state"],
                    last[game.player]["action"],
                    new_state,
                    1
                )
                break

            # If game is continuing, no rewards yet
            elif last[game.player]["state"] is not None:
                player.update(
                    last[game.player]["state"],
                    last[game.player]["action"],
                    new_state,
                    0
                )

    print("Done training")

    # Return the trained AI
    return player


def play(ai, human_player=None):
    """
    Play human game against the AI.
    `human_player` can be set to 0 or 1 to specify whether
    human player moves first or second.
    """

    # If no player order set, choose human's order randomly
    if human_player is None:
        human_player = random.randint(0, 1)

    # Create new game
    game = Nim()

    # Game loop
    while True:

        # Print contents of piles
        print()
        print("Piles:")
        for i, pile in enumerate(game.piles):
            print(f"Pile {i}: {pile}")
        print()

        # Compute available actions
        available_actions = Nim.available_actions(game.piles)
        time.sleep(1)

        # Let human make a move
        if game.player == human_player:
            print("Your Turn")
            while True:
                pile = int(input("Choose Pile: "))
                count = int(input("Choose Count: "))
                if (pile, count) in available_actions:
                    break
                print("Invalid move, try again.")

        # Have AI make a move
        else:
            print("AI's Turn")
            pile, count = ai.choose_action(game.piles, epsilon=False)
            print(f"AI chose to take {count} from pile {pile}.")

        # Make move
        game.move((pile, count))

        # Check for winner
        if game.winner is not None:
            print()
            print("GAME OVER")
            winner = "Human" if game.winner == human_player else "AI"
            print(f"Winner is {winner}")
            return

```



## 🛠️ 实现里的小坑

写代码的时候遇到了一些细节问题：

- **状态要转 tuple**：不然 Python 字典会报错 “unhashable type: list”。
- **random.choice 不能直接用在 set 上**：要先 `list(actions)`。
- **Q 值的更新要谨慎**：特别是 `best_future_reward` 的计算，不能写错。

这些小坑搞清楚之后，程序就能顺利跑通了。

------

## ✨ 我的收获

做完这个作业，我对强化学习有了一个非常直观的认识：

- Q-learning 的核心其实很朴素：
   **“旧经验” + 一点点“新经验” → 逐渐积累出智慧。**
- 通过不停试错，奖励会一点点往前传，前面的动作也能学会哪些是好哪些是坏。
- 最神奇的是，AI 从一开始的“乱走”，到最后真的能打得几乎无敌。

和上一个 Shopping 作业相比，Nim 的重点不在“数据处理”，而是在 **算法逻辑**。这里我第一次真正感受到 AI 在“自学”的过程，这比分类问题更震撼。

------

## 📝 附：简化版 Q-learning 流程（伪代码）

```
Q = {}  # Q[(state, action)] = value

for game in range(10000):
    state = initial_state()
    while not game_over(state):
        action = choose_action(state, epsilon=True)
        new_state, reward = make_move(state, action)

        # Q-learning 更新
        old_q = Q.get((state, action), 0)
        future = max(Q.get((new_state, a), 0) for a in actions(new_state))
        Q[(state, action)] = old_q + alpha * ((reward + future) - old_q)

        state = new_state
```
