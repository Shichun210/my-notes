## 序

顺利完成degrees，树立了信心，趁热打铁继续

**第二个作业Tic-Tac-Toe**



## 需求分析

用python实现一个会下井字棋(Tic-tac-toe)的AI玩家，要求如下：

- 游戏状态用`X`，`O`，`EMPTY`表示
- 玩家轮流落子
- AI玩家需要使用Minimax算法，确保选择最优策略
- 需要处理游戏结束，胜利或者平局的判断
- 理论上你永远不能赢AI玩家，如果它赢了，检查你的算法

游戏框架pygame部分已经提供，只需要实现游戏逻辑部分



## 问题抽象

这是一个典型的对抗性搜索问题，我们可以这样建模：

- 状态：井子棋是一个3x3的棋盘，每个格子是`X`，`O`或`EMPTY`，用一个二维列表就很容易表示，如：

  ```python
  # Board as a 3x3 list
  board = [
      ["X", "O", "EMPTY"],
      ["EMPTY", "X","EMPTY"],
      ["EMPTY", "EMPTY", "O"]
  ]
  ```

- 动作：玩家选择落子的动作可以抽象为改变棋盘格局(每次把二维列表里的一个`EMPTY`变为`X`或者`O`)

- 状态变化：根据当前玩家选择的落子位置生成新的棋盘

- 目标：根据当前玩家选择的落子位置生成新棋盘

- 效用值：
  - `X`获胜，返回+1
  - `O`获胜，返回-1
  - 平局，返回0



## 核心算法设计

> Minimax极小化极大算法是对抗搜索中的一种算法，它将获胜条件表示为一方得分为(-1), 另一方得分为(+1)。后续行动将由这些条件驱动，Min玩家试图获得最低分数，而Max玩家则试图获得最高分数。

![Minimax in tic-tac-toe](https://cs50.harvard.edu/ai/notes/0/minimax_tictactoe.png)

**Minimax基本思路：**

1. 终止条件
   - 当前棋局已结束，直接返回效用值
2. 轮到Max玩家(X)
   - 遍历所有可落子的位置
   - 对每个位置递归调用Minimax
   - 选择返回值最大的动作
3. 轮到Min玩家
   - 遍历所有可落子的位置
   - 对每个位置递归调用Minimax
   - 选择返回值最小的动作



## 代码实现

### tictactoe.py

```python
"""
Tic Tac Toe Player
"""

import math
import copy

X = "X"
O = "O"
EMPTY = None

def initial_state():
    """
    Returns starting state of the board.
    """
    return [[EMPTY, EMPTY, EMPTY],
            [EMPTY, EMPTY, EMPTY],
            [EMPTY, EMPTY, EMPTY]]

def player(board):
    """
    Returns player who has the next turn on a board.
    """
    num_x = 0
    num_o = 0
    for row in board:
        for element in row:
            if element == X:
                num_x += 1
            elif element == O:
                num_o += 1
    # X always play first, so if X is more than O, then O's turn
    return O if num_x > num_o else X

def actions(board):
    """
    Returns set of all possible actions (i, j) available on the board.
    """
    possible_actions = set()
    for i in range(len(board)):
        for j in range(len(board[i])):
            if board[i][j] == EMPTY:
                possible_actions.add((i, j))
    return possible_actions

def result(board, action):
    """
    Returns the board that results from making move (i, j) on the board.
    """
    # check if action is valid
    if len(action) != 2:
        raise ValueError("Invalid action length")
    
    i, j = action
    if not (0 <= i < len(board) and 0 <= j < len(board[i])):
        raise ValueError("Action out of range")

    if board[i][j] != EMPTY:
        raise ValueError("Cell is not empty")

    new_board = copy.deepcopy(board)
    new_board[i][j] = player(board)
    return new_board 

def winner(board):
    """
    Returns the winner of the game, if there is one.
    """
    # check rows
    for row in board:
        if row[0] == row[1] == row[2] != EMPTY:
            return row[0]
    #check columns
    for col in range(3):
        if board[0][col] == board[1][col] == board[2][col] != EMPTY:
            return board[0][col]
    # check two diagonals
    if board[0][0] == board[1][1] == board[2][2] != EMPTY:
        return board[0][0]
    if board[0][2] == board[1][1] == board[2][0] != EMPTY:
        return board[0][2]
    
    # No winner
    return None

def terminal(board):
    """
    Returns True if game is over, False otherwise.
    """
    # Winner exist, game over 
    if winner(board) is not None:
        return True
    # No empty cells, game over
    elif all(cell != EMPTY for row in board for cell in row):
        return True
    else:
        return False

def utility(board):
    """
    Returns 1 if X has won the game, -1 if O has won, 0 otherwise.
    """
    w = winner(board)
    if w == X:
        return 1
    elif w == O:
        return -1
    else:
        return 0

def minimax(board):
    """
    Returns the optimal action for the current player on the board.
    """
    # Game is over, no more actions
    if terminal(board):
        return None
    
    current_player = player(board)

    best_action = None
    # Max player, try to get the highest score
    if current_player == X:
        best_score = -math.inf
        for action in actions(board):
            # O player will take action, and he/she will minimize score
            score = min_value(result(board, action))
            if score > best_score:
                best_score = score
                best_action = action
    # Min player, try to get the lowest score
    else:
        best_score = math.inf
        for action in actions(board):
            # X player will take action, and he/she will maximize score
            score = max_value(result(board, action))
            if score < best_score:
                best_score = score
                best_action = action

    return best_action

def max_value(board):
    """
    return max value of new board result if you take the action, 
    Min player need to get the max value, because Min player know
    than Max player will give him/her the max value
    """
    if terminal(board):
        return utility(board)

    best_score = -math.inf
    for action in actions(board):
        # For each action that min player will take, 
        # he/she will choose the minimal score
        score = min_value(result(board, action))
        # All those values min player give me, I will pick up
        # a maximal one
        best_score = max(best_score, score)
    return best_score


def min_value(board):
    """
    return min value of new board result if you take the action, 
    Max player need to get the min value, because Max player know
    than Min player will give him/her the min value
    """
    if terminal(board):
        return utility(board)

    best_score = math.inf
    for action in actions(board):
        # For each action that max player will take, 
        # he/she will choose the maximal score
        score = max_value(result(board, action))
        # All those values max player give me, I will pick up
        # a minimal one
        best_score = min(best_score, score)
    return best_score
```



## 实现效果

AI玩家永远不会输，它会优先选择获胜的落子，因为可以获得+1分，如果没有获胜的机会，会阻止对方获胜，得分为0.



## 课后思考

Tic-Tac-Toe项目中最大的难点是**递归实现**。Minimax算法需要考虑每一步所有可能落子，并假设对手也会做出最优回应，每层递归都要在Max玩家和Min玩家间切换，并准确判断终局，同时回溯棋盘状态。

虽然状态空间不大，递归可行，但这个练习让我直观理解了对抗性搜索的核心思路，体会到算法处理多层决策的精妙，也为更复杂游戏的优化算法打下基础。通过这个项目，我真正感受到AI在策略决策上的力量。

Tic-tac-toe只有9个格子，棋局最多也只有9！种情况，用Minimax可行。但是如果碰到更多状态的棋局，比如国际象棋，就没办法去遍历所有的可能性，因为实在太多，达到了10²⁹⁰⁰⁰ 种可能性。于是，必须增加一些优化算法，比如Alpha-Beta剪枝，会大大优化搜索效率。本次作业只是对抗性搜索的入门练习，但是为更复杂的对抗性搜索算法提供了理论基础。

通过观看课程视频，总结课程笔记，然后认真完成课后项目，较好地理解了搜索算法的核心思路，敲开了AI算法的大门。

继续前进，开启第二课的学习。

