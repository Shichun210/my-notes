# CS50 AI 学习笔记：Shopping 作业总结

这一课第一个作业是 **Shopping**，目标是写一个 AI，预测用户在网上购物时是否会完成购买。

------

## 📖 题目理解

题目背景：
 在电商网站上，大部分用户浏览时并不会下单，但如果我们能预测用户是否有购买意图，就能采取不同策略，比如：如果预测用户可能不会买，就给点优惠券。

于是任务就变成了：

- 输入：用户的浏览行为（访问了多少页面、停留时间、是否周末、用什么浏览器等等）
- 输出：用户是否会下单（1 = 买，0 = 不买）

训练数据有大约 12,000 个用户会话。我们要做的就是训练一个**最近邻分类器（kNN, k=1）**，然后用它来预测。

------

## 🛠️ 解题思路

这个作业相对比较简单，主要就是对scikit-learn库的初步熟悉。scikit-learn库的简单应用，在课程里面已经给出了具体的例子，本次作业相当于就是照葫芦画瓢。

作业要求实现三个函数：

### 1. `load_data(filename)`

- 从 CSV 文件中读入数据。
- `evidence`：每个用户的特征（17 个值）。
- `labels`：对应的购买结果（0 或 1）。
- 返回一个tuple`（evidence, labels）`
- 需要特别注意**数据类型转换**：哪些是 `int`，哪些是 `float`，还有 `Month` 要转成 0~11，`VisitorType` 和 `Weekend` 要转成 0/1。

### 2. `train_model(evidence, labels)`

- 使用 scikit-learn 的 `KNeighborsClassifier`，参数 `n_neighbors=1`。
- 调用 `.fit(evidence, labels)` 来训练。

### 3. `evaluate(labels, predictions)`

- 计算 **敏感度 (sensitivity)** = 真正例率 = 正样本里预测正确的比例。
- 计算 **特异度 (specificity)** = 真负例率 = 负样本里预测正确的比例。
- 返回 `(sensitivity, specificity)`。

最终运行时，输出结果类似：

```
Correct: 4088
Incorrect: 844
True Positive Rate: 41.02%
True Negative Rate: 90.55%
```

------

## 📝代码实现

> 我觉得没太多说的，代码比较简单。课程里面另外介绍了几种模型，可以直接尝试切换模型看看预测结果的变化。

```python
import csv
import sys

from sklearn.model_selection import train_test_split
from sklearn.neighbors import KNeighborsClassifier

TEST_SIZE = 0.4

def main():

    # Check command-line arguments
    if len(sys.argv) != 2:
        sys.exit("Usage: python shopping.py data")

    # Load data from spreadsheet and split into train and test sets
    evidence, labels = load_data(sys.argv[1])
    X_train, X_test, y_train, y_test = train_test_split(
        evidence, labels, test_size=TEST_SIZE
    )

    # Train model and make predictions
    model = train_model(X_train, y_train)
    predictions = model.predict(X_test)
    sensitivity, specificity = evaluate(y_test, predictions)

    # Print results
    print(f"Correct: {(y_test == predictions).sum()}")
    print(f"Incorrect: {(y_test != predictions).sum()}")
    print(f"True Positive Rate: {100 * sensitivity:.2f}%")
    print(f"True Negative Rate: {100 * specificity:.2f}%")


def load_data(filename):
    """
    Load shopping data from a CSV file `filename` and convert into a list of
    evidence lists and a list of labels. Return a tuple (evidence, labels).

    evidence should be a list of lists, where each list contains the
    following values, in order:
        - Administrative, an integer
        - Administrative_Duration, a floating point number
        - Informational, an integer
        - Informational_Duration, a floating point number
        - ProductRelated, an integer
        - ProductRelated_Duration, a floating point number
        - BounceRates, a floating point number
        - ExitRates, a floating point number
        - PageValues, a floating point number
        - SpecialDay, a floating point number
        - Month, an index from 0 (January) to 11 (December)
        - OperatingSystems, an integer
        - Browser, an integer
        - Region, an integer
        - TrafficType, an integer
        - VisitorType, an integer 0 (not returning) or 1 (returning)
        - Weekend, an integer 0 (if false) or 1 (if true)

    labels should be the corresponding list of labels, where each label
    is 1 if Revenue is true, and 0 otherwise.
    """
    evidence = []
    labels = []
    month = {"Jan": 0, "Feb": 1, "Mar": 2, "Apr": 3, "May": 4,"June": 5,
             "Jul": 6, "Aug": 7, "Sep": 8, "Oct": 9, "Nov": 10, "Dec": 11}

    with open(filename) as f:
        reader = csv.reader(f)
        next(reader)

        for row in reader:
            evidence.append([
                int(row[0]),
                float(row[1]),
                int(row[2]),
                float(row[3]),
                int(row[4]),
                float(row[5]),
                float(row[6]),
                float(row[7]),
                float(row[8]),
                float(row[9]),
                int(month[row[10]]),
                int(row[11]),
                int(row[12]),
                int(row[13]),
                int(row[14]),
                1 if row[15] == "Returning_Visitor" else 0,
                1 if row[16] == "TRUE" else 0
            ])
            labels.append(1 if row[-1] == "TRUE" else 0)
    
    return (evidence, labels)


def train_model(evidence, labels):
    """
    Given a list of evidence lists and a list of labels, return a
    fitted k-nearest neighbor model (k=1) trained on the data.
    """
    # model = Perceptron()
	# model = svm.SVC()
	# model = GaussianNB()
    model = KNeighborsClassifier(n_neighbors=1)
    model.fit(evidence, labels)
    return model


def evaluate(labels, predictions):
    """
    Given a list of actual labels and a list of predicted labels,
    return a tuple (sensitivity, specificity).

    Assume each label is either a 1 (positive) or 0 (negative).

    `sensitivity` should be a floating-point value from 0 to 1
    representing the "true positive rate": the proportion of
    actual positive labels that were accurately identified.

    `specificity` should be a floating-point value from 0 to 1
    representing the "true negative rate": the proportion of
    actual negative labels that were accurately identified.
    """
    true_pos_count = 0
    true_neg_count = 0

    for v_label, v_pre in zip(labels, predictions):
        if v_label == v_pre:
            if v_label == 1:
                true_pos_count += 1
            elif v_label == 0:
                true_neg_count += 1

    sensitivity = true_pos_count/labels.count(1)
    specificity = true_neg_count/labels.count(0)

    return sensitivity, specificity


if __name__ == "__main__":
    main()

```



## ✨ 收获与体会

第一次实践 scikit-learn，感受到 scikit-learn 的方便：只要用 `.fit()` 训练数据和 `.predict()`对数据做预测，核心部分一行就能解决。切换模型也很方便。scikit-learn库里面还有众多模型可以深入探索。

这让我体会到，机器学习真正难的部分往往不是“调库”，而是数据准备和结果分析。