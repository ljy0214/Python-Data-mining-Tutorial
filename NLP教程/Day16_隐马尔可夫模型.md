# Day16 : 隐马尔可夫模型

> **作者**：长行
>
> **时间**：2020.05.08

隐马尔可夫模型(Hidden Markov Model,HMM)是描述两个时许序列联合分布p(x,y)的概率模型，是一种著名的有向图模型，也是解决自然语言处理中标注问题的统计机器学模型。

其中x为外界观测可见的观测序列，也称为显状态；y是外界不可见的状态序列，也称为隐状态。

下面，我们借用骰子的例子来描述：

假设有3个不同的骰子，分别为D6、D4和D8。其中D6有6个面，D4有4个面，D8有8个面。

下面我们进行如下过程：从三个骰子中随机选择一个，投掷它并得到一个数值，并将此操作执行6次。

我们会获得一串数值[1,6,3,5,2,7]，这就是观测序列；而在每次选择的骰子[D6,D8,D8,D6,D4,D8]，则是状态序列；黑色的箭头是从状态序列中的转换；红色的箭头是从状态序列到观测序列的输出。

其中，状态序列的状态转移构成了隐马尔可夫链；而任意时刻的观测值都只取决于对应时刻状态，与其他时刻的状态或观测独立无关。

> 马尔可夫链：每个事件的发生概率只取决于前一个事件，将满足该假设的多个事件串联在一起，就构成了马尔可夫链。

在使用{B,M,E,S}标注集做中文分词时，可以将字视作观测值，将{B,M,E,S}的状态视作状态值。

>>BMES标注集：B = 词语开头，M = 词语中间，E = 词语结尾，S = 单字成词

隐马尔可夫模型是由初始状态概率向量、状态转移概率矩阵和观测概率矩阵共同决定的，下面我们分开了解。

## 初始状态概率向量

系统启动时进入的第一个状态称为初始状态，是一个离散型随机变量，其概率分布的参数向量，称为初始状态概率向量。

在三个骰子的例子中，三个骰子被选中的概率都是一样的，因此其初始状态概率向量如下：

```
D4 = 1/3
D6 = 1/3
D8 = 1/3
```

而在中文分词的例子中，在句子开头不可能是词语中间或词语结尾，只能是词语开头和单字成词，因此其初始状态概率向量可能如下：

```
P(B) = 0.7
P(M) = 0
P(E) = 0
P(S) = 0.3
```

## 状态转移概率矩阵

根据马尔可夫假设，t+1时的状态仅仅取决于t时的状态。因为一共有N中状态，因此从状态si到状态sj的概率就构成了一个N×N的矩阵，称为状态转移概率矩阵。

在三个骰子的例子中，三个骰子被选中的概率实际上不受前一次的影响，因此状态转移概率矩阵中所有的值均为1/3。

而在中文分词的例子中，单字成词后不可能是词语结尾，词语结尾后也不可能是词语结尾，因此可以用P(E|S)=0和P(E|E)=0来表示。

这些概率分布的参数在自然语言领域都是通过对语料库的统计自动学习。

## 观测概率矩阵
因为观测值仅取决于当前的状态值，因此观测值和状态值都是独立的离散型随机变量。假设观测值一共有M种可能的取值，状态值一共有N中可能的矩阵的，那么这些参数向量就构成了N×M的矩阵，称为观测概率矩阵，也称发射概率矩阵。

在三个骰子的例子中，观测值共有8个可能的取值{1,2,3,4,5,6,7,8}，状态值共有3个可能的取值(D4,D6,D8)；此时的观测概率矩阵为3×8的矩阵。其中D4不可能得到观测值5，因此p(x=8,y=D4)=0。

在中文分词的例子中，“忑”通常作为“忐忑”的词语结尾(E)出现，因此p(x=忑,y=E)的概率较高，而p(x=忑,y=B)的概率较小。

>学习参考文献：
>*  详解隐马尔可夫模型(HMM) 小修 《自然语言处理技术》
>* 《自然语言处理入门》(何晗)：4.2