# 第八章：神经网络算法

各种因素的结合使得人工神经网络（ANNs）成为当今最重要的机器学习技术之一。这些因素包括解决日益复杂的问题的需求，数据的爆炸以及诸如可用的廉价集群等技术的出现，这些技术提供了设计非常复杂算法所需的计算能力。

事实上，这是一个迅速发展的研究领域，负责实现机器人技术、自然语言处理和自动驾驶汽车等领先技术领域所宣称的大部分重大进展。

观察 ANN 的结构，其基本单元是神经元。ANN 的真正优势在于其能够通过将它们组织成分层架构来利用多个神经元的力量。ANN 通过在不同层中将神经元链接在一起来创建分层架构。信号通过这些层传递，并在每个层中以不同的方式进行处理，直到生成最终所需的输出。正如我们将在本章中看到的，ANN 使用的隐藏层充当抽象层，实现了深度学习，这在实现强大的应用程序中被广泛使用，如亚马逊的 Alexa，谷歌的图像搜索和谷歌相册。

本章首先介绍了典型神经网络的主要概念和组件。然后，它介绍了各种类型的神经网络，并解释了这些神经网络中使用的不同类型的激活函数。接下来，详细讨论了反向传播算法，这是训练神经网络最广泛使用的算法。然后，解释了迁移学习技术，可以用来大大简化和部分自动化模型的训练。最后，通过一个真实世界的应用程序案例，探讨了如何使用深度学习来标记欺诈文件。

本章讨论的主要概念如下：

+   理解人工神经网络

+   ANN 的演变

+   训练神经网络

+   工具和框架

+   迁移学习

+   案例研究：使用深度学习进行欺诈检测

让我们从人工神经网络的基础知识开始。

# 理解人工神经网络

受人脑神经元工作的启发，神经网络的概念是由 Frank Rosenblatt 在 1957 年提出的。要充分理解其架构，有助于简要了解人脑中神经元的分层结构。（参考以下图表，了解人脑中的神经元是如何连接在一起的。）

在人脑中，树突充当检测信号的传感器。然后，信号传递给轴突，轴突是神经细胞的一种长而细的突出部分。轴突的功能是将这个信号传递给肌肉、腺体和其他神经元。如下图所示，信号通过称为突触的相互连接的组织传递，然后传递给其他神经元。请注意，通过这种有机管道，信号一直传递，直到它到达目标肌肉或腺体，引起所需的动作。信号通常需要七到八毫秒才能通过神经元链传递并到达目标位置：

![](img/0c34e87b-05f8-4248-a1d3-bfecc9d58c0b.png)

受到这种自然的信号处理的建筑杰作的启发，Frank Rosenblatt 设计了一种意味着数字信息可以在层中处理以解决复杂数学问题的技术。他最初设计的神经网络非常简单，看起来类似于线性回归模型。这种简单的神经网络没有任何隐藏层，被命名为感知器。以下图表对其进行了说明：

![](img/88d873d7-e3fb-4de6-a442-85e1cd5539c9.png)

让我们尝试开发这个感知器的数学表示。在前面的图表中，输入信号显示在左侧。这是输入的加权和，因为每个输入*(x[1]，x[2]..x[n])*都会乘以相应的权重*(w[1]，w[2]… w[n])*，然后求和：

![](img/3f4de7d2-6950-4b64-a0c5-3f96e3b1f24e.png)

请注意，这是一个二元分类器，因为这个感知器的最终输出取决于聚合器的输出是真还是假（在图表中显示为**∑**）。如果聚合器能够从至少一个输入中检测到有效信号，它将产生一个真实的信号。

现在让我们来看看神经网络是如何随着时间的推移而发展的。

# 人工神经网络的演变

在前面的部分中，我们研究了一个没有任何层的简单神经网络，称为感知器。发现感知器有严重的局限性，并且在 1969 年，马文·明斯基和西摩·帕帕特进行了研究，得出结论感知器无法学习任何复杂的逻辑。

事实上，他们表明即使学习像异或这样简单的逻辑函数也是一种挑战。这导致了对机器学习和神经网络的兴趣下降，开始了一个现在被称为**AI 寒冬**的时代。世界各地的研究人员不会认真对待人工智能，认为它无法解决任何复杂的问题。

所谓的 AI 寒冬的主要原因之一是当时可用的硬件能力的限制。要么是必要的计算能力不可用，要么是价格昂贵。到了 20 世纪 90 年代末，分布式计算的进步提供了易于获得和负担得起的基础设施，这导致了 AI 寒冬的融化。这种融化重新激发了对人工智能的研究。最终导致了将当前时代变成一个可以称为**AI 春天**的时代，人们对人工智能和神经网络特别感兴趣。

对于更复杂的问题，研究人员开发了一个称为**多层感知器**的多层神经网络。多层神经网络有几个不同的层，如下图所示。这些层包括：

+   输入层

+   隐藏层

+   输出层：

深度神经网络是一个具有一个或多个隐藏层的神经网络。深度学习是训练人工神经网络的过程。![](img/4ec6b21a-e4bb-4c00-b243-d7b438d0f3d9.png)

一个重要的事情要注意的是神经元是这个网络的基本单元，每个层的神经元都连接到下一层的所有神经元。对于复杂的网络，这些互连的数量激增，我们将探索不牺牲太多质量的不同减少这些互连的方法。

首先，让我们尝试阐明我们要解决的问题。

输入是一个特征向量*x*，维度为*n*。

我们希望神经网络能够预测值。预测值由*ý*表示。

从数学上讲，我们想要确定在给定特定输入的情况下，交易是欺诈的概率。换句话说，给定特定的*x*值，*y*=1 的概率是多少？从数学上讲，我们可以表示如下：

![](img/a68e1e70-b03b-423e-8eb2-b6e57787c0d5.png)

注意，*x*是一个*n[x]*维向量，其中*n[x]*是输入变量的数量。

这个神经网络有四层。输入和输出之间的层是隐藏层。第一个隐藏层中的神经元数量用![](img/11fb6e2b-3c01-4cb3-b314-0d3f70d33ea8.png)表示。各个节点之间的连接由称为*权重*的参数相乘。训练神经网络就是找到权重的正确值。

让我们看看如何训练神经网络。

# 训练神经网络

使用给定数据集构建神经网络的过程称为训练神经网络。让我们来看一下典型神经网络的解剖结构。当我们谈论训练神经网络时，我们谈论计算权重的最佳值。训练是通过使用一组示例（训练数据的形式）进行迭代完成的。训练数据中的示例具有不同输入值组合的输出的预期值。神经网络的训练过程与传统模型的训练方式不同（这些在第七章中讨论过，*传统监督学习算法*）。

# 理解神经网络的解剖结构

让我们看看神经网络包括什么：

+   **层：** 层是神经网络的核心构建模块。每一层都是一个数据处理模块，充当过滤器。它接受一个或多个输入，以某种方式处理它，然后产生一个或多个输出。每当数据通过一层时，它都会经过一个处理阶段，并显示与我们试图回答的业务问题相关的模式。

+   **损失函数：** 损失函数提供了在学习过程的各个迭代中使用的反馈信号。损失函数提供了单个示例的偏差。

+   **成本函数：** 成本函数是完整示例集上的损失函数。

+   **优化器：** 优化器确定损失函数提供的反馈信号将如何被解释。

+   **输入数据：** 输入数据是用于训练神经网络的数据。它指定目标变量。

+   **权重：** 权重是通过训练网络计算的。权重大致对应于每个输入的重要性。例如，如果特定输入比其他输入更重要，在训练后，它将被赋予更大的权重值，充当乘数。即使对于该重要输入的弱信号也将从大的权重值（充当乘数）中获得力量。因此，权重最终会根据它们的重要性转换每个输入。

+   **激活函数：** 值将由不同的权重相乘然后聚合。它们将如何被聚合以及它们的值将如何被解释将由所选择的激活函数的类型确定。

现在让我们来看看神经网络训练的一个非常重要的方面。

在训练神经网络时，我们逐个示例地进行。对于每个示例，我们使用正在训练的模型生成输出。我们计算期望输出和预测输出之间的差异。对于每个单独的示例，这种差异称为**损失**。在整个训练数据集中，损失被称为**成本**。随着我们不断训练模型，我们的目标是找到导致最小损失值的权重值。在整个训练过程中，我们不断调整权重的值，直到找到导致最小可能总成本的权重值集。一旦达到最小成本，我们标记模型已经训练完成。

# 定义梯度下降

训练神经网络模型的目的是找到权重的正确值。我们开始训练神经网络时，权重使用随机或默认值。然后，我们迭代使用优化算法，例如梯度下降，以改变权重，使我们的预测改善。

梯度下降算法的起点是需要通过算法迭代优化的随机权重值。在随后的每次迭代中，算法通过以最小化成本的方式改变权重值来进行。

以下图解释了梯度下降算法的逻辑：

![](img/47b9db15-d8f5-4fa7-b88f-044a27d2cb3c.png)

在前面的图中，输入是特征向量**X**。目标变量的实际值是**Y**，目标变量的预测值是**Y'**。我们确定实际值与预测值的偏差。我们更新权重并重复这些步骤，直到成本最小化。

在算法的每次迭代中如何改变权重将取决于以下两个因素：

+   **方向：** 要走的方向以获得损失函数的最小值

+   **学习率：** 我们选择的方向中变化应该有多大

下面的图表显示了一个简单的迭代过程：

![](img/116bf2a0-a40d-45f4-aa58-cd0c7ddec8ba.png)

该图显示了通过改变权重，梯度下降试图找到最小成本。学习率和选择的方向将决定要探索的图表上的下一个点。

选择正确的学习率很重要。如果学习率太小，问题可能需要很长时间才能收敛。如果学习率太高，问题将无法收敛。在前面的图中，代表当前解决方案的点将在图表的两条相反线之间不断振荡。

现在，让我们看看如何最小化梯度。只考虑两个变量，*x*和*y*。*x*和*y*的梯度计算如下：

![](img/9c045c39-cac5-4e9d-976d-7e77da01c018.png)

为了最小化梯度，可以使用以下方法：

```py
while(gradient!=0):
 if (gradient < 0); move right
 if (gradient > 0); move left
```

该算法也可以用于找到神经网络的最优或接近最优的权重值。

请注意，梯度下降的计算是在整个网络中向后进行的。我们首先计算最后一层的梯度，然后是倒数第二层，然后是倒数第二层之前的层，直到达到第一层。这被称为反向传播，由 Hinton、Williams 和 Rumelhart 于 1985 年引入。

接下来，让我们看看激活函数。

# 激活函数

激活函数规定了特定神经元的输入如何被处理以生成输出。

如下图所示，神经网络中的每个神经元都有一个激活函数，确定输入将如何被处理：

![](img/536fd811-bb85-40d2-9ac6-28e8cf18bc20.png)

在前面的图中，我们可以看到激活函数生成的结果传递到输出。激活函数设置了如何解释输入值以生成输出的标准。

对于完全相同的输入值，不同的激活函数将产生不同的输出。在使用神经网络解决问题时，了解如何选择正确的激活函数是很重要的。

现在让我们逐个查看这些激活函数。

# 阈值函数

最简单的激活函数是阈值函数。阈值函数的输出是二进制的：0 或 1。如果任何输入大于 1，它将生成 1 作为输出。这可以在下图中解释：

![](img/3b653d1d-c86d-45e7-8d19-366b8fb9dd19.png)

请注意，一旦检测到输入加权和中有任何生命迹象，输出（*y*）就变为 1。这使得阈值激活函数非常敏感。它很容易被输入中的最轻微的信号或噪音错误触发。

# Sigmoid

Sigmoid 函数可以被认为是阈值函数的改进。在这里，我们可以控制激活函数的灵敏度。

![](img/581c93c0-8b11-4abf-8e29-2923349c1a40.png)

Sigmoid 函数*y*定义如下：

![](img/cf23e5ef-72a7-42de-a05e-71c0595f48bb.png)

可以在 Python 中实现如下：

```py
def sigmoidFunction(z): 
      return 1/ (1+np.exp(-z))
```

请注意，通过降低激活函数的灵敏度，我们可以减少输入中的故障的影响。请注意，Sigmoid 激活函数的输出仍然是二进制的，即 0 或 1。

# 修正线性单元（ReLU）

本章介绍的前两个激活函数的输出是二进制的。这意味着它们将取一组输入变量并将它们转换为二进制输出。ReLU 是一个激活函数，它将一组输入变量作为输入，并将它们转换为单一的连续输出。在神经网络中，ReLU 是最流行的激活函数，通常用于隐藏层，我们不希望将连续变量转换为类别变量。

下面的图表总结了 ReLU 激活函数：

![](img/f99737ce-4ece-44ae-afc6-35e51f95a0e6.png)

请注意，当*x≤ 0*时，*y = 0*。这意味着输入中的任何信号为零或小于零都被转换为零输出：

![](img/c36cfe68-1975-4d9b-98f9-1e404b75399e.png) for ![](img/9ab31cd7-0b6a-4412-b32f-72ec6a44227f.png)![](img/ecea8529-3f94-4351-84bb-3841d873a580.png) for ![](img/0b063a5e-03ee-44f1-8a19-664c6536e069.png)

一旦*x*大于零，它就是*x*。

ReLU 函数是神经网络中最常用的激活函数之一。它可以在 Python 中实现如下：

```py
def ReLU(x): 
if x<0: 
    return 0 
else: 
    return x
```

现在让我们来看看 Leaky ReLU，它是基于 ReLU 的。

# Leaky ReLU

在 ReLU 中，*x*的负值导致*y*的值为零。这意味着在这个过程中丢失了一些信息，这使得训练周期变得更长，特别是在训练开始时。Leaky ReLU 激活函数解决了这个问题。对于 Leaky ReLu，以下内容适用：

![](img/10f3d67b-45d7-44b6-8455-576d8bae3079.png) ; for ![](img/2fd2b88a-3be6-49d4-ae6b-fbb781c0c34e.png)![](img/6aab91fb-9ecf-489a-a7d1-f74144c4dcb0.png) for![](img/01238af1-5c97-438f-9c4d-6eeb770fc177.png)

下面的图表显示了这一点：

![](img/75ed9e52-7fbb-4b34-9e63-3bd0fff166ef.png)

这里，ß是一个小于一的参数。

它可以在 Python 中实现如下：

```py
def leakyReLU(x,beta=0.01):
    if x<0:
        return (beta*x)    
    else:        
        return x
```

有三种指定ß值的方法：

+   我们可以指定一个默认值为ß。

+   我们可以在我们的神经网络中将ß设为一个参数，并让神经网络决定该值（这称为**参数化 ReLU**）。

+   我们可以将ß设为一个随机值（这称为**随机化 ReLU**）。

# 双曲正切（tanh）

tanh 函数类似于 Sigmoid 函数，但它也能给出负信号。下图说明了这一点：

![](img/f43bf042-f802-41dd-8990-e192757ad7c5.png)

*y*函数如下：

![](img/88736b33-6ea8-4e3e-ab82-887e5f487fe8.png)

可以通过以下 Python 代码实现：

```py
def tanh(x): 
    numerator = 1-np.exp(-2*x) 
    denominator = 1+np.exp(-2*x) 
    return numerator/denominator
```

现在让我们来看看 softmax 函数。

# Softmax

有时，我们需要激活函数的输出不止两个级别。Softmax 是一个可以为输出提供不止两个级别的激活函数。它最适用于多类分类问题。假设我们有*n*个类。我们有输入值。输入值将类映射如下：

*x = {x^((1)),x^((2)),....x^((n))}*

Softmax 是基于概率理论的。Softmax 的第*e*类的输出概率计算如下：

![](img/bb2a0fea-2795-46ca-8749-aafbd462c707.png)对于二元分类器，最终层的激活函数将是 Sigmoid，对于多类分类器，它将是 Softmax。

# 工具和框架

在本节中，我们将详细了解用于实现神经网络的框架和工具。

随着时间的推移，许多不同的框架已经被开发出来来实现神经网络。不同的框架有各自的优势和劣势。在本节中，我们将重点关注具有 TensorFlow 后端的 Keras。

# Keras

Keras 是最受欢迎和易于使用的神经网络库之一，用 Python 编写。它考虑到易用性，并提供了实现深度学习的最快方式。Keras 只提供高级模块，并被认为是在模型级别上。

# Keras 的后端引擎

Keras 需要一个低级别的深度学习库来执行张量级别的操作。这个低级别的深度学习库称为*后端引擎*。Keras 的可能后端引擎包括以下内容：

+   **TensorFlow** ([www.tensorflow.org](https://www.tensorflow.org/))：这是同类框架中最受欢迎的框架，由谷歌开源。

+   **Theona** ([deeplearning.net/software/theano](http://deeplearning.net/software/theano))：这是在蒙特利尔大学 MILA 实验室开发的。

+   **Microsoft Cognitive Toolkit**（**CNTK**）：这是由微软开发的。

这种模块化深度学习技术堆栈的格式如下图所示：

![](img/d57a079b-0263-474a-9c6f-a419ed2ecb88.png)

这种模块化的深度学习架构的优势在于，Keras 的后端可以在不重写任何代码的情况下进行更改。例如，如果我们发现对于特定任务，TensorFlow 比 Theona 更好，我们可以简单地将后端更改为 TensorFlow，而无需重写任何代码。

# 深度学习堆栈的低级层

我们刚刚提到的三种后端引擎都可以在 CPU 和 GPU 上运行，使用堆栈的低级层。对于 CPU，使用了一个名为**Eigen**的张量操作低级库。对于 GPU，TensorFlow 使用了 NVIDIA 的**CUDA 深度神经网络**（**cuDNN**）库。

# 定义超参数

如第六章中所讨论的*无监督机器学习算法*，超参数是在学习过程开始之前选择其值的参数。我们从常识值开始，然后尝试稍后优化它们。对于神经网络，重要的超参数包括：

+   激活函数

+   学习率

+   隐藏层的数量

+   每个隐藏层中的神经元数量

让我们看看如何使用 Keras 定义模型。

# 定义 Keras 模型

定义完整的 Keras 模型涉及三个步骤：

1.  **定义层**。

我们可以用两种可能的方式使用 Keras 构建模型：

+   **Sequential API：**这允许我们为一系列层构建模型。它用于相对简单的模型，并且通常是构建模型的常规选择：

![](img/d836d917-46b6-4525-b030-bd434e2ee4cd.png)

请注意，在这里，我们创建了三层 - 前两层具有 ReLU 激活函数，第三层具有 softmax 作为激活函数。

+   **Functional API：**这允许我们为层的非循环图形构建模型。使用 Functional API 可以创建更复杂的模型。

![](img/7caf80b6-fee4-4173-be0d-5a6efd584932.png)

请注意，我们可以使用顺序和功能 API 来定义相同的神经网络。从性能的角度来看，使用哪种方法来定义模型并没有任何区别。

1.  **定义学习过程**。

在这一步中，我们定义了三件事：

+   优化器

+   损失函数

+   将量化模型质量的指标：

![](img/c540f046-60b4-4740-8d6a-f164581aed1e.png)

请注意，我们使用`model.compile`函数来定义优化器、损失函数和指标。

1.  **训练模型**。

一旦定义了架构，就是训练模型的时候了：

![](img/96bff123-20b0-49ed-9739-c22f3e8ddff5.png)

请注意，`batch_size`和`epochs`等参数是可配置的参数，使它们成为超参数。

# 选择顺序或功能模型

顺序模型将 ANN 创建为简单的层堆叠。顺序模型易于理解和实现，但其简单的架构也有一个主要限制。每一层只连接到一个输入和输出张量。这意味着如果我们的模型在任何隐藏层的输入或输出处有多个输入或多个输出，那么我们不能使用顺序模型。在这种情况下，我们将不得不使用功能模型。

# 理解 TensorFlow

TensorFlow 是处理神经网络的最流行的库之一。在前面的部分中，我们看到了如何将其用作 Keras 的后端引擎。它是一个开源的高性能库，实际上可以用于任何数值计算。如果我们看一下堆栈，我们可以看到我们可以用高级语言（如 Python 或 C++）编写 TensorFlow 代码，然后由 TensorFlow 分布式执行引擎解释。这使得它对开发人员非常有用和受欢迎。

TensorFlow 的工作方式是创建一个**有向图**（**DG**）来表示您的计算。连接节点的是边，数学运算的输入和输出。它们也代表数据的数组。

# 介绍 TensorFlow 的基本概念

让我们简要了解一下 TensorFlow 的概念，比如标量、向量和矩阵。我们知道，在传统数学中，简单的数字，比如三或五，被称为**标量**。此外，在物理学中，**向量**是具有大小和方向的东西。在 TensorFlow 中，我们使用向量来表示一维数组。延伸这个概念，二维数组被称为**矩阵**。对于三维数组，我们使用术语**3D 张量**。我们使用术语**等级**来捕捉数据结构的维度。因此，**标量**是一个**等级 0**的数据结构，**向量**是一个**等级 1**的数据结构，**矩阵**是一个**等级 2**的数据结构。这些多维结构被称为**张量**，并在下图中显示：

![](img/1eceea7d-8244-4156-90ee-d4b739fe0fe1.png)

在前面的图表中，我们可以看到等级定义了张量的维度。

现在让我们看看另一个参数，`shape`。`shape`是一个整数元组，指定每个维度中数组的长度。下图解释了`shape`的概念：

![](img/909e6fb6-e3be-42fb-aa53-48010ffe319a.png)

使用`shape`和等级，我们可以指定张量的详细信息。

# 理解张量数学

现在让我们看看使用张量进行不同的数学计算：

+   让我们定义两个标量，并尝试使用 TensorFlow 进行加法和乘法：

![](img/5a659402-0713-4684-9f2a-82b06e36c24c.png)

+   我们可以将它们相加和相乘，并显示结果：

![](img/ced54688-183d-4fb6-b225-0363341a3a76.png)

+   我们还可以通过将两个张量相加来创建一个新的标量张量：

![](img/fcdc1e92-3893-494a-ba31-d64e09f19618.png)

+   我们还可以执行复杂的张量函数：

![](img/d8ad73e8-25c0-4028-b542-2da93252e716.png)

# 理解神经网络的类型

神经网络可以有多种构建方式。如果每一层中的每个神经元都连接到另一层中的每个神经元，那么我们称之为密集或全连接神经网络。让我们看看一些其他形式的神经网络。

# 卷积神经网络

**卷积神经网络**（**CNNs**）通常用于分析多媒体数据。为了更多地了解 CNN 如何用于分析基于图像的数据，我们需要掌握以下过程：

+   卷积

+   池化

让我们逐一探索它们。

# 卷积

卷积的过程通过使用另一个较小的图像（也称为**过滤器**或**核**）来处理特定图像中感兴趣的模式。例如，如果我们想要在图像中找到物体的边缘，我们可以使用特定的过滤器对图像进行卷积来得到它们。边缘检测可以帮助我们进行物体检测、物体分类和其他应用。因此，卷积的过程是关于在图像中找到特征和特点。

寻找模式的方法是基于寻找可以在不同数据上重复使用的模式。可重复使用的模式称为过滤器或核。

# 池化

为了进行机器学习的多媒体数据处理的重要部分是对其进行下采样。这提供了两个好处：

+   它减少了问题的整体维度，大大减少了训练模型所需的时间。

+   通过聚合，我们可以提取多媒体数据中不必要的细节，使其更通用并更具代表性。

下采样的执行如下：

![](img/e0c416f9-1cf3-4056-9e3d-26d427784c53.png)

请注意，我们已经用一个像素替换了每个四个像素的块，选择了四个像素中的最高值作为该像素的值。这意味着我们已经按四分之一的比例进行了下采样。由于我们选择了每个块中的最大值，这个过程被称为**最大池化**。我们也可以选择平均值；在那种情况下，它将是平均池化。

# 循环神经网络

**循环神经网络**（**RNNs**）是一种特殊类型的神经网络，它们基于循环架构。这就是为什么它们被称为*循环*。需要注意的重要事情是 RNNs 具有记忆。这意味着它们有能力存储最近迭代的信息。它们被用于分析句子结构以预测句子中的下一个单词等领域。

# 生成对抗网络

**生成对抗网络**（**GANs**）是一种生成合成数据的神经网络类型。它们是由 Ian Goodfellow 及其同事于 2014 年创建的。它们可以用来生成从未存在过的人的照片。更重要的是，它们用于生成合成数据以增加训练数据集。

在接下来的部分中，我们将看到什么是迁移学习。

# 迁移学习

多年来，许多组织、研究团体和开源社区内的个人已经完善了一些使用大量数据进行训练的复杂模型，以供通用用途。在某些情况下，他们已经投入了多年的努力来优化这些模型。一些这些开源模型可以用于以下应用：

+   视频中的物体检测

+   图像中的物体检测

+   音频的转录

+   文本的情感分析

每当我们开始训练一个新的机器学习模型时，我们要问自己的问题是：我们是否可以简单地定制一个经过充分验证的预训练模型，而不是从头开始？换句话说，我们是否可以将现有模型的学习迁移到我们的自定义模型，以便回答我们的业务问题？如果我们能做到这一点，它将提供三个好处：

+   我们的模型训练工作将得到一个快速启动。

+   通过使用经过充分测试和建立的模型，我们的模型整体质量可能会得到提高。

+   如果我们没有足够的数据来解决我们正在处理的问题，使用通过迁移学习的预训练模型可能会有所帮助。

让我们看两个实际例子，这将是有用的：

+   在训练机器人时，我们可以首先使用模拟游戏来训练神经网络模型。在那个模拟中，我们可以创建所有那些在现实世界中很难找到的罕见事件。一旦训练完成，我们可以使用迁移学习来训练模型适用于真实世界。

+   假设我们想要训练一个模型，可以从视频源中分类苹果和 Windows 笔记本电脑。已经有成熟的开源目标检测模型可以准确分类视频源中的各种物体。我们可以使用这些模型作为起点，识别笔记本电脑。一旦我们识别出物体是笔记本电脑，我们可以进一步训练模型区分苹果和 Windows 笔记本电脑。

在下一节中，我们将应用本章涵盖的概念来构建一个欺诈文档分类神经网络。

# 案例研究-使用深度学习进行欺诈检测

使用**机器学习**（**ML**）技术识别欺诈文档是一个活跃且具有挑战性的研究领域。研究人员正在调查神经网络的模式识别能力在多大程度上可以用于这个目的。可以使用原始像素而不是手动属性提取器，用于几种深度学习架构结构。

# 方法论

本节介绍的技术使用了一种称为**Siamese 神经网络**的神经网络架构，它具有两个共享相同架构和参数的分支。使用 Siamese 神经网络来标记欺诈文档如下图所示：

![](img/46b8db83-bcc9-4c6a-9bbd-bb19de3a84ac.png)

当需要验证特定文档的真实性时，我们首先基于其布局和类型对文档进行分类，然后将其与预期的模板和模式进行比较。如果偏离超过一定阈值，它被标记为伪造文档；否则，它被视为真实文档。对于关键用例，我们可以添加一个手动流程，用于边界情况，算法无法确定地将文档分类为真实或伪造。

为了比较文档与其预期模板，我们在 Siamese 架构中使用两个相同的 CNN。CNN 具有学习最佳的平移不变局部特征检测器和可以构建对输入图像的几何失真具有鲁棒性的表示的优势。这非常适合我们的问题，因为我们的目标是通过单个网络传递真实和测试文档，然后比较它们的相似性。为了实现这个目标，我们实施以下步骤。

假设我们想要测试一个文档。对于每类文档，我们执行以下步骤：

1.  获取真实文档的存储图像。我们称之为**真实文档**。测试文档应该看起来像真实文档。

1.  真实文档通过神经网络层，创建一个特征向量，这是真实文档模式的数学表示。我们称之为**特征向量 1**，如前图所示。

1.  需要测试的文档称为**测试文档**。我们通过一个类似于用于创建真实文档特征向量的网络来传递这个文档。测试文档的特征向量称为**特征向量 2**。

1.  我们使用特征向量 1 和特征向量 2 之间的欧氏距离来计算真实文档和测试文档之间的相似度分数。这个相似度分数被称为**相似度测量**（**MOS**）。MOS 是 0 到 1 之间的数字。较高的数字代表文档之间的距离较小，文档相似的可能性较大。

1.  如果神经网络计算的相似度分数低于预定义的阈值，我们将文档标记为欺诈。

让我们看看如何使用 Python 实现 Siamese 神经网络：

1.  首先，让我们导入所需的 Python 包：

```py
import random
import numpy as np
import tensorflow as tf
```

1.  接下来，我们将定义将用于处理 Siamese 网络各个分支的神经网络：

```py
def createTemplate():
      return tf.keras.models.Sequential([
        tf.keras.layers.Flatten(),
        tf.keras.layers.Dense(128, activation='relu'),
        tf.keras.layers.Dropout(0.15),
        tf.keras.layers.Dense(128, activation='relu'),
        tf.keras.layers.Dropout(0.15),
        tf.keras.layers.Dense(64, activation='relu'), 
        ])
```

请注意，为了减少过拟合，我们还指定了`0.15`的丢失率。

1.  为了实现 Siamese 网络，我们将使用 MNIST 图像。MNIST 图像非常适合测试我们的方法的有效性。我们的方法包括以每个样本包含两个图像和一个二进制相似度标志的方式准备数据。这个标志是它们来自相同类别的指示器。现在让我们实现名为`prepareData`的函数，它可以为我们准备数据：

```py
def prepareData(inputs: np.ndarray, labels: np.ndarray):
      classesNumbers = 10
      digitalIdx = [np.where(labels == i)[0] for i in range(classesNumbers)]
      pairs = list()
      labels = list()
      n = min([len(digitalIdx[d]) for d in range(classesNumbers)]) - 1
      for d in range(classesNumbers):
        for i in range(n):
            z1, z2 = digitalIdx[d][i], digitalIdx[d][i + 1]
            pairs += [[inputs[z1], inputs[z2]]]
            inc = random.randrange(1, classesNumbers)
            dn = (d + inc) % classesNumbers
            z1, z2 = digitalIdx[d][i], digitalIdx[dn][i]
            pairs += [[inputs[z1], inputs[z2]]]
            labels += [1, 0] 
      return np.array(pairs), np.array(labels, dtype=np.float32)
```

注意，`prepareData()`将导致所有数字的样本数量相等。

1.  我们现在将准备训练和测试数据集：

```py
(x_train, y_train), (x_test, y_test) = tf.keras.datasets.mnist.load_data()
x_train = x_train.astype(np.float32)
x_test = x_test.astype(np.float32)
x_train /= 255
x_test /= 255
input_shape = x_train.shape[1:]
train_pairs, tr_labels = prepareData(x_train, y_train)
test_pairs, test_labels = prepareData(x_test, y_test)
```

1.  现在，让我们创建 Siamese 系统的两个部分：

```py
input_a = tf.keras.layers.Input(shape=input_shape)
enconder1 = base_network(input_a)
input_b = tf.keras.layers.Input(shape=input_shape)
enconder2 = base_network(input_b)
```

1.  现在，我们将实现**相似度度量**（MOS），它将量化我们想要比较的两个文档之间的距离：

```py
distance = tf.keras.layers.Lambda( 
    lambda embeddings: tf.keras.backend.abs(embeddings[0] - embeddings[1])) ([enconder1, enconder2])
measureOfSimilarity = tf.keras.layers.Dense(1, activation='sigmoid') (distance)
```

现在，让我们训练模型。我们将使用 10 个 epochs 来训练这个模型：

![](img/df9a4124-9b34-4f4e-86ad-464cdf028948.png)

请注意，我们使用 10 个 epochs 达到了 97.49%的准确率。增加 epochs 的数量将进一步提高准确度水平。

# 总结

在本章中，我们首先看了神经网络的细节。我们首先看了神经网络多年来的发展。我们研究了不同类型的神经网络。然后，我们看了神经网络的各种构建模块。我们深入研究了用于训练神经网络的梯度下降算法。我们讨论了各种激活函数，并研究了激活函数在神经网络中的应用。我们还看了迁移学习的概念。最后，我们看了一个实际例子，说明了神经网络如何用于训练可以部署到标记伪造或欺诈文件的机器学习模型。

展望未来，在下一章中，我们将探讨如何将这样的算法用于自然语言处理。我们还将介绍网络嵌入的概念，并将研究循环网络在自然语言处理中的应用。最后，我们还将研究如何实现情感分析。
