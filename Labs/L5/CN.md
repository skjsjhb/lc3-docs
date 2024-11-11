# 实验 5：探秘之光

*注：中文版为阅读理解方便而编写，与原文有所不同，请**务必同时参考英文原文**。*

## 概述

> 若有秘密等待探寻，就由我来一一发现！

最后的实验已能望其项背，构想逐渐转换为现实的痕迹。向音乐的世界（或者说存款问题，如果你对这个故事还一无所知的话）与数学计算（即所谓的**处理**）道别，我们现在转向**交互**。没有输入的程序功能单一，没有输出的程序毫无意义。准备好下一个挑战了？我们将从内存移动到**控制台**，探寻 LC-3 的另一神秘之处。

## 背景

密码、宝藏、埋藏的历史。探索的魅力构筑着我们庞大的知识库。正所谓“神秘的事物一直在我们身旁”。这里是 0 和 1 的世界，而你是 LC-3 探险家小队的队长，准备出发**寻找潜藏在字符串中的宝藏符文** `1010`。好吧，或许和童话故事不那么一样，拿到它不会让你变成超级英雄，也不会解锁特殊副本。这不过是探索途中的小小纪念品，用以纪念勇敢者的足迹。闲话到此为止，你的小队已经准备万全，是时候开始你的指挥了。

## 任务目标

从控制台读入一个字符串，并清点其中 `1010` 符文出现的次数。

- 每个字符最多能使用一次，即 `10101010` 算作 2 次（`1010/1010`）。

- 输入以小写字母 `y` 标志结束。

- 输入末尾**没有换行符**，即不按下 `Enter` 键。

- 输入只有 `0` 和 `1`。

- 输入可能是空白的。

- 输出数据须传递到控制台。

- 符文的个数**可能超过 10**，此时按十进制输出。

## 输入输出样例

?> 输入以方括号 `[]` 标注。

输入 `1100101011010` 的情况：

```
Enter the source: [1100101011010y]
Key count: 2
```

输入 `11111111` 的情况：

```
Enter the source: [11111111y]
Key count: 0
```

## 详细要求

- LC3XT 采用一种**基于缓冲区的输入方法**向你的程序传递输入，这种输入只能通过特殊设计的 `GETC` 和 `IN` 来读取，**不支持中断**。如果要使用中断，请改用 LC3Tools。

- 输入提示符 `Enter the source: ` 也可以任意变更，在比较结果时会被忽略。

- 输出数据除了统计次数外**不要包含其它数字**，否则 LC3XT 将无法正确解析结果。

## 指南

### 读取输入

尽管从硬件上来看，读取输入是件很复杂的事情，但 LC-3 已经内置了读取输入的指令 `IN`。与 `getc` 类似，系统调用 `IN`（即 `TRAP x23`）读取一个输入，并将其 ASCII 编码存入 `R0`。如果输入当中还没有字符，则它会等待下一个的出现。

```
IN ; R0 = getc()
```

### 识别字符串

用于解决字符串识别问题的万能灵药是所谓的**确定型自动机（Deterministic Finite Automaton，DFA）**。它的工作方式如下：

1. 确定识别字符串过程中的各个**状态**。一个状态包含它已经识别的字符以及期望看到的字符。本例包含 4 个状态：
   
   - 初始：无字符
   
   - `1`：已经读入 `1`，期望下一个字符是 `0`
   
   - `10`：已经读入 `10`，期望下一个字符是 `1`
   
   - `101`：已经读入 `101`，期望下一个字符是 `0`

2. 分析状态之间的**转换关系**。每当获取一个新的输入时，状态需要相应转换。在本例中，一个状态要么是**移进**（如果输入和它的期望匹配，例如 `10` 在接受了 `1` 后转到 `101`），要么是**重置**（如果输入和期望不匹配，例如 `10` 在接受了 `0` 后转到初始状态）。

3. 当识别到目标字符串的状态时，执行所要的操作。按照上面的分析，当状态处于 `101` 并且输入是 `1` 时，意味着我们已经读到了一个 `1010`。

实现 DFA 时，通常使用一个变量存储当前的状态。输入处理程序要根据输入的内容将当前状态转换到合适的下一个状态。

### 输出数字

将数字转换成一个个字符（例如 `10` 变成字符串 `"10"`）不能称得上简单。将数字转换为字符串，通常采用所谓的**短除法**：

1. 将该数除以 10，计算商 `Q` 和余数 `R`。

2. 把余数 `R` 增添到输出的最前方。

3. 如果 `Q` 不比 10 小，则重复这个过程。

用 Python 代码能更清晰地表达：

```python
n = 0 # 输入数字
s = ""
while (n >= 10) {
    r = n % 10
    s = str(r) + s
    n = n // 10
}
```

“好吧，但是我缺的除以 10 这块谁给我补啊？”你可能要问。除以 10 比除以 2 难得多，因为它不能简单通过移位来完成。一种相对折中的算法是从被除数中不断减去 10 直到不能再减，并数数减去了多少个 10。我们（守序邪恶地）邀请读者将这个算法的具体实现作为一个练习。

### 输出到终端

和 `IN` 很类似，我们可以使用 `OUT` 来输出一个字符，不过，对于更长的字符串，其实不必这么麻烦。我们可以使用 `PUTS` 系统调用，它输出位于 `R0` 的字符串，以 `0` 作为其末尾。使用 `OUT` 还是 `PUTS` 完全取决于你，只是不要忘了在字符串的最后添上 `0`。

## 提示与技巧

- 注意区分数字 `1` 和字符 `'1'`（后者的 ASCII 编码是 `49`）。

- `y` 的编码是 `121`，`0` 的编码是 `48`。

- 在 LC3Tools 中运行时，当你按下按键的时刻，字符就已经被读取，不需要按下 `Enter`。

## 实验报告

实验报告就和……无所谓啦，你知道报告该长什么样的。不过你要是在用 LC3Tools，这次请不要忘记输出的内容在控制台而不是内存。

## 提交

提交要求与实验 2 相同。

## Mythic Light Quest（原曲使用）

（下方的嵌入式页面包含自动播放代码，点按即可播放）

<style>

    #player {
        width: 100%;
        aspect-ratio: 16 / 9;
    }

</style>

<iframe id="player" src="https://monster-siren.hypergryph.com/music/880310" referrerpolicy="no-referrer"/>