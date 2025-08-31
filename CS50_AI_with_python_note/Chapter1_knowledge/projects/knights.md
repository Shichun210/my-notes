## 序

在视频课程以及整理笔记的过程中学习了一些逻辑表达，以及逻辑推理。这些对程序员来说不难理解，需要理解的是符号，句子，知识库，模型之间的关系，以及模型检验算法的原理。人类有逻辑思维，但是计算机没有，聪明的人类想到办法让计算机可以像人一样进行推理，不断地从知识库的旧知识中推导出新知识以扩充知识库，也不断地在知识库中寻找结论。

本文将对我的解题思路、代码实现以及遇到的问题做一个技术总结。



## 题目要求

我们需要用逻辑推理表示一些谜题，把谜题的线索翻译成逻辑表达式，输入到知识库中，最终能够通过模型检验算法，识别出谜题中的角色是骑士还是恶棍。

背景规则：

- 每个角色要么是骑士，要么是恶棍
- 骑士只会说真话，恶棍只会说假话



将知识添加到知识库knowledge0, knowledge1, knowledge2以及knowledge3中，看是否能够通过model_check，推导出每个角色分别是骑士还是恶棍。

- 谜题 0 是来自“背景”的谜题。它包含一个角色A。
  - A说“我既是骑士，又是恶棍。”
- 谜题 1 有两个角色：A 和 B。
  - A说“我们都是无赖。”
  - B 什么也没说。
- 谜题 2 有两个角色：A 和 B。
  - A说“我们是同一类人。”
  - B说“我们是不同类型的。”
- 谜题 3 有三个角色：A、B 和 C。
  - A 会说“我是骑士。”或者“我是恶棍。”，但你不知道是哪一个。
  - B 说：“A 说‘我是个无赖。’”
  - B 接着说“C 是个无赖。”
  - C 说“A 是骑士。”



## 题目分析

其实就是把文字翻译为逻辑命题的句子，使用一些逻辑连接词，并把它们输入到知识库中。

这里需要注意的是，谜题里面每个人说的每一句话，得分两种情况考虑，因为角色不清楚是骑士还是恶棍，所以他们说的话有可能是真话，也有可能是假话。

对于真话，骑士角色就蕴涵他说的话，恶棍角色则蕴涵他说的话的相反意思。

题目中出现的“我们是同一类人”这种表述，是明显的双向蕴涵(⇔)。

谜题3会有些难度，需要一些逻辑思考才能写出正确的逻辑命题句子。

详见代码，注释写得比较清晰。



## 代码实现

logic.py已经实现，symbol也已经定义好，我们只需要填充4个KB

```python
from logic import *

AKnight = Symbol("A is a Knight")
AKnave = Symbol("A is a Knave")

BKnight = Symbol("B is a Knight")
BKnave = Symbol("B is a Knave")

CKnight = Symbol("C is a Knight")
CKnave = Symbol("C is a Knave")

# Puzzle 0
# A says "I am both a knight and a knave."
knowledge0 = And(
    Or(AKnight, AKnave),
    Not(And(AKnight, AKnave))
)
# If AKnight, (AKnight && AKnave) is true
knowledge0.add(Implication(AKnight, And(AKnight, AKnave)))
# If AKnave, (AKnight && AKnave) is not true
knowledge0.add(Implication(AKnave, Not(And(AKnight, AKnave))))

# Puzzle 1
# A says "We are both knaves."
# B says nothing.
knowledge1 = And(
    Or(AKnight, AKnave),
    Not(And(AKnight, AKnave)),
    Or(BKnight, BKnave),
    Not(And(BKnight, BKnave))
)

# A says "We are both knaves.", if AKnight, (AKnave && BKnave) is true.
knowledge1.add(Implication(AKnight, And(AKnave, BKnave)))
# if AKnave, we just know (AKnave && BKnave) is Not true.
knowledge1.add(Implication(AKnave, Not(And(AKnave, BKnave))))

# Puzzle 2
# A says "We are the same kind."
# B says "We are of different kinds."
knowledge2 = And(
    Or(AKnight, AKnave),
    Not(And(AKnight, AKnave)),
    Or(BKnight, BKnave),
    Not(And(BKnight, BKnave))
)

# A says "We are the same kind.", if AKnight, A,B are the same kind, means A->B && B->A
knowledge2.add(Implication(AKnight, Biconditional(AKnight, BKnight)))
# if AKnave, not the same kind, (A->B && B->A) is Not true
knowledge2.add(Implication(AKnave, Not(Biconditional(AKnave, BKnave))))

# B says "We are of different kinds.", if BKnight, A,B are not the same kind. (A->B && B->A) is Not true
knowledge2.add(Implication(BKnight, Not(Biconditional(AKnight, BKnight))))
# if BKnave, A,B are the same kind, means A->B && B->A
knowledge2.add(Implication(BKnave, Biconditional(AKnight, BKnight)))

# Puzzle 3
# A says either "I am a knight." or "I am a knave.", but you don't know which.
# B says "A said 'I am a knave'."
# B says "C is a knave."
# C says "A is a knight."
knowledge3 = And(
    Or(AKnight, AKnave),
    Not(And(AKnight, AKnave)),
    Or(BKnight, BKnave),
    Not(And(BKnight, BKnave)),
    Or(CKnight, CKnave),
    Not(And(CKnight, CKnave))
)

# if AKnight, (AKnight || AKnave) is true, otherwise (AKnight || AKnave) is Not true
knowledge3.add(Implication(AKnight, Or(AKnight, AKnave)))
knowledge3.add(Implication(AKnave, Not(Or(AKnight, AKnave))))

# B says "A said 'I am a knave'.", so if BKnight, then A said that he is a Knave.
# "A said 'I am a knave'." can't be true. Because if AKnight, he lied, if AKnave, he not lied. it's contradictory.
# so B must lied, B is not Knight
knowledge3.add(Not(BKnight))

# B says "C is a knave.", If Bknight, then CKnave. Else BKnave, CKnight.
knowledge3.add(Implication(BKnight, CKnave))
knowledge3.add(Implication(BKnave, CKnight))

# C says "A is a knight.", If Cknight, then AKnight. Else CKnave, AKnave.
knowledge3.add(Implication(CKnight, AKnight))
knowledge3.add(Implication(CKnave, AKnave))

def main():
    symbols = [AKnight, AKnave, BKnight, BKnave, CKnight, CKnave]
    puzzles = [
        ("Puzzle 0", knowledge0),
        ("Puzzle 1", knowledge1),
        ("Puzzle 2", knowledge2),
        ("Puzzle 3", knowledge3)
    ]
    for puzzle, knowledge in puzzles:
        print(puzzle)
        if len(knowledge.conjuncts) == 0:
            print("    Not yet implemented.")
        else:
            for symbol in symbols:
                if model_check(knowledge, symbol):
                    print(f"    {symbol}")

if __name__ == "__main__":
    main()

```



## 运行结果

Puzzle 0
    A is a Knave
Puzzle 1
    A is a Knave
    B is a Knight
Puzzle 2
    A is a Knave
    B is a Knight
Puzzle 3
    A is a Knight
    B is a Knave
    C is a Knight



## 总结和反思

本课的难点不在于逻辑命题句子，而是在于理解模型检验算法(在这课的笔记里面我有做详细的讲解)和看懂算法实现代码model_check函数。