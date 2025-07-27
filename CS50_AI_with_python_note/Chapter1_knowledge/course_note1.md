## 序 (Preface)

在学习这个课程系列的过程中，我希望不仅仅是跟随课程完成作业，而是能够逐步理解人工智能背后的基本思想和逻辑。本节 *Knowledge* 课主要围绕知识表示（Knowledge Representation）与逻辑推理（Logical Inference），这是人工智能的重要基石。通过形式化知识并基于规则进行推理，我们能够让计算机“像人一样”利用已知信息去发现未知结论。
 这份笔记将系统整理课程的核心内容，并为后续学习打下坚实基础

## 📖知识

> 人类基于现有知识进行推理并得出结论。
>
> 表达知识并从中得出结论的概念也应用于人工智能领域。

我们如何把知识(对世界的已知)用形式化的语言表达出来，并在此基础上进行可靠的逻辑推理，得出新的结论？这是我们这一课需要探讨的问题。

---

### 基于知识的代理

> 基于知识的代理可以通过对知识的内部表征进行操作来进行推理。

> **句子**是用知识表示的语言对世界做出的断言。句子是人工智能存储知识并利用知识推断新信息的方式。

流程：

1. **表达**：把事实/规则写成逻辑句子。
2. **推理**：用规则从旧知识中推导出新的知识(逻辑句子)
3. **行动**：根据知识库中的知识得出结论。

> 举个课程中的例子，我们有如下句子：
>
> 1. 如果今天没有下雨，哈利今天就会去拜访海格。
> 2. 哈利今天拜访了海格或邓布利多，但没有同时拜访他两人。
> 3. 哈利今天拜访了邓布利多。
>
> 基于这3个句子，我们可以回答一个问题，“今天下雨了吗？”。
>
> 尽管每一个句子都没有告诉我们今天是否下雨了。
>
> 通过句子3个2，我们可以推导出新的句子
>
> 4. 哈利今天没有去拜访海格
>
> 然后，通过句子4和句子1，我们可以推导出结论：
>
> 5. 今天下雨了
>
> 为了得出这个结论，我们使用了人类特有的**逻辑**进行推导，如何让计算也能像人类一样进行逻辑推导？



### 命题逻辑

> 命题逻辑是基于命题，即对世界的陈述，要么真要么假。

#### 命题符号

我们常用一些简单的符号来表示命题，比如 P，Q，R。

#### 逻辑连接词

> 逻辑连接词是连接命题符号的逻辑符号，以便以更复杂的方式推理世界。

- **Not**(**¬**)：使命题的真值取反

  > P表示“正在下雨”，`¬P`则表示“没有下雨”

- **And**(∧)：连接两个不同的命题P，Q，只有当两个命题都为真时，整个连接的命题才为真

  > 表示P和Q。只有 P和Q都为真时，`P ∧ Q`才为真。

- **Or**(**∨**)：也叫“包含或”，连接两个不同的命题P，Q，任何一个命题为真，整个连接的命题就为真

  > 表示P或者Q。P和Q任何一个为真，`P ∨ Q`就为真，当然也包括了两个同时为真

- **XOR**(⊕)：也叫“排他或”或者“异或”，有且仅有一个命题为真时，连接命题才为真

  > `P ⊕ Q`，只有当P或者Q为真时，整个命题才为真。P和Q都为真，命题为假

- **Implication**(**⇒**)：“蕴涵”表示“如果P，则Q”的结构，P是前提，Q是结果

  > Implication稍微有点不好理解，我们举一些例子可以帮助思考
  >
  > P表示“我中了彩票”，Q表示“我请你吃饭”，那么`P ⇒ Q`就表示“我如果中了彩票，我就请你吃饭”，请你吃饭的前提是我中了彩票。
  >
  > 所以我们来看看P和Q分别为真和假时，`P ⇒ Q`是真还是假
  >
  > | P     |   Q   | P ⇒ Q |                             解释                             |
  > | :---- | :---: | :---: | :----------------------------------------------------------: |
  > | True  | True  | True  |               条件满足，没有违背承诺，蕴涵成立               |
  > | True  | False | False | 我中了彩票，前提满足了，但是没有请你吃饭，违背承诺，蕴涵不成立 |
  > | False | True  | True  |   前提没有满足但是还是请你吃饭了，并没有违背承诺，蕴涵成立   |
  > | False | False | True  |     我没有中彩票，没有请你吃饭也就没有违背承诺，蕴涵成立     |

- **Biconditional**(**⇔**)：双条件是一种双向的蕴涵，可以理解为“当且仅当”

  > `P ⇔ Q`，等同于` (P ⇒ Q) ∧ (Q ⇒ P)`。P可以推导出Q，Q也可以推导出P。
  >
  > 举个例子P表示“我口渴了”，Q表示“我要喝水”，那么`P ⇔ Q`就表示“如果我口渴了，我就要喝水”和“如果我要喝水，就说明我口渴了”。这个比较好理解，因为P可以得出结论Q，Q也可以得出结论P，所以为真的前提推导出为真的结论，那么连接命题就成立。如果命题为假，自然得不到为真的结论。
  >
  > | P     |   Q   | P ⇔ Q |
  > | :---- | :---: | :---: |
  > | True  | True  | True  |
  > | True  | False | False |
  > | False | True  | False |
  > | False | False | True  |



#### 模型

> 模型是给每个命题一个真/假赋值，构成一个“可能的世界“。如果有n个命题，可构成的模型数为n^2^

例如，如果 P：“正在下雨。” 和 Q：“今天是星期二。”，则模型可以是以下真值分配：{P = True, Q = False}。这个模型意味着今天正在下雨，但今天不是星期二。然而，在这种情况下，存在更多可能的模型（例如，{P = True, Q = True}，表示今天既是星期二又是下雨）。



#### 知识库

> 知识库是知识型代理已知的一组被认为是真的句子。这些句子以命题逻辑句子的形式提供给AI关于世界的知识，可以用来对世界进行额外的推理。



#### 蕴含(**⊨**)

> 蕴含(Entailment)是一种关系表示，如果 `α ⊨ β`（α 蕴涵 β），那么在任何 α 为真的世界中，β 也为真。
>
> 蕴含(Entailment)不同于蕴涵(Implication)

例如，如果 α：“今天是一月的星期二”，β：“今天是一月”，那么我们知道 `α ⊨ β`。也就是如果今天是一月的星期二为真，我们也知道今天是一月。



### 模型检验和推理

> 推理是从旧句子中推导出新句子的过程。例如前面哈利的例子，我们从句子1，2，3推导出了句子4，5.

基于现有知识推理出新知识的方法有很多种，我们首先来考虑模型检验算法。

#### 模型检验算法

为了确定是否`KB ⊨ a`，换句话说，我们能否根据我们的知识库KB得出a为真的结论？我们需要这样做：

- 列举出所有可能的模型
- 过滤出KB为真的模型
- 看目标a是否在这些模型种都为真，如果是，那么KB必然蕴含a，即`KB ⊨ a`

考虑下面的例子：

P：今天是星期二。Q：下雨了。R：哈利要去跑步。KB：`(P ∧ ¬Q) → R`（换句话说，P 且非 Q 蕴涵 R）P（P 为真）¬Q（Q 为假）查询：R（我们想知道 R 是真还是假；`KB ⊨ R` 吗？）

我们列举出所有可能的模型(P, Q, R分别为真和假的组合)：

| P     | Q     | R        | KB        |
| ----- | ----- | -------- | --------- |
| False | False | False    | ~~False~~ |
| False | False | True     | ~~False~~ |
| False | True  | False    | ~~False~~ |
| False | True  | True     | ~~False~~ |
| True  | False | False    | False     |
| True  | False | **True** | **True**  |
| True  | True  | False    | ~~False~~ |
| True  | True  | True     | ~~False~~ |

然后我们检查每个模型，并根据我们的知识库检查它是否正确。

首先，在我们的知识库种，我们知道P为真，Q为假。因此，我们可以说，在所有P为假或者Q为真的模型中，KB都是假的。

最后，剩下两个模型。在这两个模型中，P为真，Q为假。在一个模型中，R 为真，在另一个模型中，R 为假。由于 (P ∧ ¬Q) → R 在我们的知识库中，我们知道，在 P 为真而 Q 为假的情况下，R 必定为真。因此，我们说，对于 R 为假的模型，我们的知识库为假；对于 R 为真的模型，我们的知识库为真。

从该表中可以看出，只有一个模型中我们的KB为真。在这个模型中，我们发现R也为真。根据我们对蕴含的定义，如果R在所有知识库为真的模型中都为真，则`KB ⊨ R`

---

#### 用代码表示模型检验算法

##### 逻辑和知识的表示

```python
from logic import *

# Create new classes, each having a name, or a symbol, representing each proposition.
rain = Symbol("rain")  # It is raining.
hagrid = Symbol("hagrid")  # Harry visited Hagrid
dumbledore = Symbol("dumbledore")  # Harry visited Dumbledore

# Save sentences into the KB
knowledge = And(  # Starting from the "And" logical connective, becasue each proposition represents knowledge that we know to be true.

    Implication(Not(rain), hagrid),  # ¬(It is raining) → (Harry visited Hagrid)

    Or(hagrid, dumbledore),  # (Harry visited Hagrid) ∨ (Harry visited Dumbledore).

    Not(And(hagrid, dumbledore)),  # ¬(Harry visited Hagrid ∧ Harry visited Dumbledore) i.e. Harry did not visit both Hagrid and Dumbledore.

    dumbledore  # Harry visited Dumbledore. Note that while previous propositions contained multiple symbols with connectors, this is a proposition consisting of one symbol. This means that we take as a fact that, in this KB, Harry visited Dumbledore.
    )
```

打印出knowledge应该是`(¬rain ⇒ hagrid) ∧ (hagrid ∨ dumbledore) ∧ (¬(hagrid ∧ dumbledore)) ∧ dumbledore`。这个就是我们知识库中储存的内容。



##### 模型检验算法

要运行模型检验算法，我们需要如下的信息：

- KB。用于进行推理
- 我们感兴趣的查询或命题是否由知识库所蕴含
- 符号。所有使用的符号(或者叫命题，比如上面例子中的rain，hagrid，dumbledore)列表
- 模型。也就是对符号的真值假值分配后，组成的世界

```python
def check_all(knowledge, query, symbols, model):

    # If model has an assignment for each symbol
    # (The logic below might be a little confusing: we start with a list of symbols. The function is recursive, and every time it calls itself it pops one symbol from the symbols list and generates models from it. Thus, when the symbols list is empty, we know that we finished generating models with every possible truth assignment of symbols.)
    if not symbols:

        # If knowledge base is true in model, then query must also be true
        if knowledge.evaluate(model):
            return query.evaluate(model)
        return True
    else:

        # Choose one of the remaining unused symbols
        remaining = symbols.copy()
        p = remaining.pop()

        # Create a model where the symbol is true
        model_true = model.copy()
        model_true[p] = True

        # Create a model where the symbol is false
        model_false = model.copy()
        model_false[p] = False

        # Ensure entailment holds in both models
        return(check_all(knowledge, query, remaining, model_true) and check_all(knowledge, query, remaining, model_false))
```

该函数是递归的调用。也就是说，它选择一个符号，创建两个模型，其中一个模型中的符号为真，另一个模型中的符号为假，然后再次调用自身，此时两个模型的区别在于该符号的真值赋值。函数将持续执行此操作，直到所有符号在模型中都被赋值真值，列表为`symbols`空。一旦列表为空（由行 标识`if not symbols`），在函数的每个实例中（每个实例包含不同的模型），函数都会在给定模型的情况下检查知识库是否为真。如果知识库在此模型中为真，则函数将检查查询是否为真，如前所述。



#### 推理规则

> 模型检查并非一种高效的算法，因为它必须考虑所有可能的模型才能给出答案（如果在所有知识库为真的模型（真值分配）下，查询 R 也为真，则 R 为真）。推理规则使我们能够**基于现有知识生成新信息**，而无需考虑所有可能的模型。

考虑这个例子：

我们知道这些信息：

- 如果正在下雨(P)，那么Harry在屋内(Q)，`P ⇒ Q`
- 正在下雨(P为真)

那么我们可以推理出Harry在屋内(Q为真)。也就是我们根据`{P ⇒ Q, P}`我们得到了Q这个结论



同样的别的一些逻辑命题也可以推理出新的命题，我们来列举一下：

|     逻辑命题     |    推理出的新命题     |                             解释                             |
| :--------------: | :-------------------: | :----------------------------------------------------------: |
|  `(P ⇒ Q) ^ P`   |          `Q`          |                                                              |
|     `P ^ Q`      |        `P, Q`         |                                                              |
|     `¬(¬P)`      |          `P`          |                        双重否定表肯定                        |
|     `P ⇒ Q`      |       `¬P ∨ Q`        | 命题“如果下雨，哈利就在屋内”，那么只能有两种情况，要么哈利在屋内(Q)，因为下雨了, 要么就没下雨¬P。 |
|     `P ⇔ Q`      | (`P ⇒ Q`) ^ (`Q ⇒ P`) |                                                              |
|    `¬(P ^ Q)`    |       `¬P ∨ ¬Q`       |  "A和B都通过了考试，这是假的"，意味着A没有通过或者B没有通过  |
|    `¬(P ∨ Q)`    |      ``¬P ^ ¬Q``      |  "A或者B没有通过考试，这不是真的"，意味着A和B都没有通过考试  |
| `(P ^ (Q ∨ R) )` |  `(P ^ Q) ∨ (P ^ R)`  |                                                              |
| `(P ∨ (Q ^ R))`  |  `(P ∨ Q) ^ (P ∨ R)`  |                                                              |

---

推理可以看作具有以下属性的搜索问题：

- 初始状态：初始化知识库KB
- 动作：推理规则
- 过度模型：推理后的新知识库
- 目标测试：检查我们试图证明的陈述是否在知识库中
- 路径成本函数：证明中的步数



**反证法**也是计算机科学中常用的工具。如果我们的知识库为真，且与¬α相矛盾，则意味着¬α为假，因此α必定为真。更确切地说，该算法将执行以下操作：

- 确定 KB 是否⊨α：

  - 将 (KB ∧ ¬α) 转换为合取范式。

  - 继续检查，看看我们是否可以使用决议来产生新的条款。

  - 如果我们得出空子句（相当于False），那么恭喜！我们得出了矛盾，从而证明了KB⊨α。

  - 然而，如果没有产生矛盾，也无法推断出更多的条款，那么就不存在蕴涵。

> 以下示例说明了该算法如何工作：
>
> - (A ∨ B) ∧ (¬B ∨ C) ∧ (¬C) 是否蕴涵 A？
> - 首先，为了用反证法证明，我们假设 A 为假。由此，我们得到 (A ∨ B) ∧ (¬B ∨ C) ∧ (¬C) ∧ (¬A)。
> - 现在，我们可以开始生成新的信息了。既然我们知道 C 为假 (¬C)，那么 (¬B ∨ C) 为真的唯一条件就是 B 也为假。因此，我们可以将 (¬B) 添加到知识库中。
> - 接下来，因为我们知道 (¬B)，所以 (A ∨ B) 为真的唯一方式是 A 为真。因此，我们可以将 (A) 添加到我们的知识库 (KB)。
> - 现在我们的知识库有两个互补的文字，(A) 和 (¬A)。我们对它们进行解析，得到空集 ()。空集根据定义是假的，因此我们得到了矛盾。



## 课后思考 (After-class Reflection)

通过本课的学习，我最大的收获是：**知识表示 + 推理规则 = 新知识**。逻辑推理看似抽象，但它的力量在于只要我们定义了规则，系统就能自动地从有限事实推出无限可能。
同时我也意识到两个挑战：

1. **规则依赖性强** —— 如果知识库不完整或规则设计不严谨，就会导致推理受限甚至得出错误结论。
2. **规模化问题** —— 在现实世界中，知识和规则数量庞大，如何让推理既高效又准确，是人工智能需要解决的重要难题。

带着这两个问题，我期待在后续课程中逐步理解 AI 是如何在实际场景（如搜索、游戏、自然语言处理）中解决这些挑战的。