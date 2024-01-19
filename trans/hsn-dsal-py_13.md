# 设计技术和策略

在本章中，我们退一步，关注计算机算法设计中更广泛的主题。随着编程经验的增长，某些模式开始变得明显。算法的世界包含了大量的技术和设计原则。掌握这些技术是解决该领域更难问题所必需的。

在本章中，我们将讨论不同类型算法的分类方式。将描述和说明设计技术。我们还将进一步讨论算法分析。最后，我们将提供一些非常重要算法的详细实现。

本章将涵盖以下主题：

+   算法的分类

+   各种算法设计方法

+   各种重要算法的实现和解释

# 技术要求

本章使用的源代码可在以下 GitHub 链接中找到：

[`github.com/PacktPublishing/Hands-On-Data-Structures-and-Algorithms-with-Python-3.7-Second-Edition/tree/master/Chapter13`](https://github.com/PacktPublishing/Hands-On-Data-Structures-and-Algorithms-with-Python-3.7-Second-Edition/tree/master/Chapter13)。

# 算法的分类

基于算法设计的目标，有许多分类方案。在之前的章节中，我们实现了各种算法。要问的问题是：这些算法是否具有相同的形式或相似之处？如果答案是肯定的，那么问：作为比较基础使用的相似之处和特征是什么？如果答案是否定的，那么这些算法能否被分成类别？

这些是我们将在随后的小节中讨论的问题。这里我们介绍了分类算法的主要方法。

# 按实现分类

将一系列步骤或流程翻译成工作算法时，可能采用多种形式。算法的核心可能使用以下一个或多个资产。

# 递归

递归算法是指调用自身以重复执行代码直到满足某个条件的算法。有些问题通过递归实现它们的解决方案更容易表达。一个经典的例子是汉诺塔。

简单来说，迭代函数是循环执行代码的一部分，而递归函数是调用自身来重复执行代码的函数。另一方面，迭代算法使用一系列步骤或重复结构来制定解决方案；它迭代执行代码的一部分。

这种重复结构可以是一个简单的`while`循环，或者任何其他类型的循环。迭代解决方案也比递归实现更容易想到。

# 逻辑

算法的一种实现是将其表达为受控逻辑推导。这个逻辑组件由将在计算中使用的公理组成。控制组件确定了推导应用到公理的方式。这表达为形式 a*lgorithm = logic + control*。这构成了逻辑编程范式的基础。

逻辑组件决定了算法的含义。控制组件只影响其效率。在不修改逻辑的情况下，可以通过改进控制组件来提高效率。

# 串行或并行算法

大多数计算机的 RAM 模型允许假设计算是一次执行一条指令的。

串行算法，也称为**顺序算法**，是按顺序执行的算法。执行从开始到结束进行，没有其他执行过程。

为了能够同时处理多条指令，需要不同的模型或计算技术。并行算法可以同时执行多个操作。在 PRAM 模型中，有共享全局内存的串行处理器。处理器还可以并行执行各种算术和逻辑操作。这使得可以同时执行多条指令。

并行/分布式算法将问题分解成子问题，分配给处理器来收集结果。一些排序算法可以有效地并行化，而迭代算法通常是可并行化的。

# 确定性与非确定性算法

确定性算法每次以相同的输入运行时都会产生相同的输出。有一些问题的解决方案设计非常复杂，以至于以确定性的方式表达它们的解决方案可能是一个挑战。

非确定性算法可以改变执行顺序或某些内部子过程，导致每次运行算法时最终结果都会发生变化。

因此，每次运行非确定性算法时，算法的输出都会不同。例如，使用概率值的算法将根据生成的随机数的值，在连续执行时产生不同的输出。

# 按复杂度分类

确定算法的复杂度是为了估计在计算或程序执行期间需要多少空间（内存）和时间。通常，通过它们的复杂度来比较两个算法的性能。较低复杂度的算法，即执行给定任务所需的空间和时间较少的算法，更受青睐。

第三章《算法设计原理》更全面地介绍了复杂性。我们将在这里总结我们所学到的内容。

# 复杂度曲线

让我们考虑一个规模为 n 的问题。为了确定算法的时间复杂度，我们用 T(n)表示。该值可能属于 O(1)、O(log n)、O(n)、O(n log(n))、O(n²)、O(n³)或 O(2^n)。根据算法执行的步骤，时间复杂度可能会受到影响。符号 O(n)捕捉了算法的增长率。

让我们现在来考虑一个实际的场景，来确定哪种算法更适合解决给定的问题。我们如何得出冒泡排序算法比快速排序算法慢的结论？或者，一般来说，我们如何衡量一个算法相对于另一个算法的效率？

好吧，我们可以比较任意数量的算法的大 O 来确定它们的效率。这种方法给我们提供了一个时间度量或增长率，它描述了算法在 n 变大时的行为。

这是一个图表，显示了算法性能可能属于的不同运行时间：

![](img/92ff52c1-8d4d-4f7a-901f-117345b34c1d.png)

按照从最好到最差的顺序，运行时间的列表为 O(1)、O(log n)、O(n)、O(n log n)、O(n²)、O(n³)和 O(2^n)。因此，如果一个算法的时间复杂度为 O(1)，而另一个算法的复杂度为 O(log n)，应该选择第一个算法。

# 按设计分类

在本节中，我们根据它们的设计提出了算法的分类。

一个给定的问题可能有多种解决方案。当分析这些解决方案时，可以观察到每个解决方案都遵循某种模式或技术。我们可以根据它们解决问题的方式对算法进行分类，如下面的小节所示。

# 分而治之

这种问题解决方法正如其名称所示。为了解决（征服）某个问题，算法将其分解为可以轻松解决的子问题。此外，这些子问题的解决方案被组合在一起，以便最终解决方案是原始问题的解决方案。

问题被分解为更小的子问题的方式大多是通过递归完成的。我们将在随后的小节中详细讨论这种技术。使用这种技术的一些算法包括归并排序、快速排序和二分查找。

# 动态规划

这种技术与分而治之类似，即将问题分解为更小的问题。然而，在分而治之中，必须先解决每个子问题，然后才能用其结果来解决更大的问题。

相比之下，动态规划不会计算已经遇到的子问题的解决方案。相反，它使用一种记忆技术来避免重新计算。

动态规划问题具有两个特征——**最优子结构**和**重叠子问题**。我们将在下一节进一步讨论这一点。

# 贪婪算法

确定某个问题的最佳解决方案可能会非常困难。为了克服这一点，我们采用一种方法，从多个可用选项或选择中选择最有前途的选择。

使用贪婪算法，指导规则是始终选择产生最有利的结果的选项，并继续这样做，希望达到完美的解决方案。这种技术旨在通过一系列局部最优选择找到全局最优的最终解决方案。局部最优选择似乎导致解决方案。

# 技术实现

让我们深入讨论一些我们讨论过的理论编程技术的实现。我们从动态规划开始。

# 使用动态规划进行实现

正如我们已经描述的，在这种方法中，我们将给定的问题分解为更小的子问题。在找到解决方案时，要注意不要重新计算任何先前遇到的子问题。

这听起来有点像递归，但这里有些不同。一个问题可能适合使用动态规划来解决，但不一定需要形成递归调用的形式。

使问题成为动态规划的理想候选者的一个特性是它具有**重叠的子问题集**。

一旦我们意识到在计算过程中子问题的形式已经重复，我们就不需要再次计算它。相反，我们返回先前遇到的子问题的预先计算结果。

为了确保我们永远不必重新评估子问题，我们需要一种有效的方法来存储每个子问题的结果。以下两种技术是 readily available。

# 记忆化

这种技术从初始问题集开始，将其分解为小的子问题。在确定了子程序的解决方案之后，我们将结果存储到该特定子问题中。将来，当遇到这个子问题时，我们只返回其预先计算的结果。

# 制表

在制表中，我们填充一个表格，其中包含子问题的解决方案，然后将它们组合起来解决更大的问题。

# 斐波那契数列

让我们考虑一个例子来理解动态规划的工作原理。我们使用斐波那契数列来说明记忆化和制表技术。

斐波那契数列可以使用递推关系来演示。递推关系是用来定义数学函数或序列的递归函数。例如，以下递推关系定义了斐波那契数列[1, 1, 2, 3, 5, 8 ...]：

```py
func(1) = 1
func(0) = 1 
func(n) = func(n-1) + func(n-2)
```

请注意，斐波那契数列可以通过将*n*的值放入序列[1, 2, 3, 4, ...]来生成。

# 记忆化技术

让我们生成斐波那契数列的前五项：

```py
    1 1 2 3 5
```

生成序列的递归式程序如下：

```py
 def fib(n): 
    if n <= 2: 
       return 1 
    else: 
       return fib(n-1) + fib(n-2) 
```

代码非常简单，但由于递归调用的存在，读起来有点棘手，因为最终解决了问题。

当满足基本情况时，`fib()`函数返回 1。如果*n*等于或小于 2，则满足基本情况。

如果未满足基本情况，我们将再次调用`fib()`函数，并这次将第一个调用提供`n-1`，第二个提供`n-2`：

```py
    return fib(n-1) + fib(n-2) 
```

解决斐波那契数列中的第`i^(th)`项的策略布局如下：

![](img/d80891b2-a57d-4b44-9630-76fafc37a303.png)

对树状图的仔细观察显示了一些有趣的模式。对**fib(1)**的调用发生了两次。对**fib(2)**的调用发生了三次。此外，对**fib(3)**的调用发生了两次。

相同函数调用的返回值永远不会改变；例如，每当我们调用**fib(2)**时，其返回值始终相同。**fib(1)**和**fib(3)**也是如此。因此，如果我们每次遇到相同的函数时都重新计算，将浪费计算时间，因为返回的结果相同。

对具有相同参数和输出的函数的重复调用表明存在重叠。某些计算在较小的子问题中重复出现。

更好的方法是在首次遇到**fib(1)**时存储计算结果。同样，我们应该存储**fib(2)**和**fib(3)**的返回值。稍后，每当我们遇到对**fib(1)**、**fib(2)**或**fib(3)**的调用时，我们只需返回它们各自的结果。

我们的`fib`调用的图现在看起来像这样：

![](img/f7165fe9-182b-46e1-9d5d-e83ea6187486.png)

如果多次遇到，我们已经消除了计算**fib(3)**，fib(2)和**fib(1)**的需要。这是备忘录技术的典型，其中在将问题分解为子问题时，不会重新计算重叠调用函数。

我们斐波那契示例中的重叠函数调用是**fib(1)**、**fib(2)**和**fib(3)**：

```py
    def dyna_fib(n, lookup): 
        if n <= 2: 
            lookup[n] = 1 

        if lookup[n] is None: 
            lookup[n] = dyna_fib(n-1, lookup) + dyna_fib(n-2, lookup) 

        return lookup[n]
```

要创建一个包含 1,000 个元素的列表，我们执行以下操作，并将其传递给`dyna_fib`函数的 lookup 参数：

```py
    map_set = [None]*(1000)
```

这个列表将存储对`dyna_fib()`函数的各种调用的计算值：

```py
    if n <= 2: 
        lookup[n] = 1 
```

对于`dyna_fib()`函数的任何小于或等于 2 的*n*的调用都将返回 1。当评估`dyna_fib(1)`时，我们将值存储在`map_set`的索引 1 处。

将`lookup[n]`的条件写为以下内容：

```py
if lookup[n] is None:
    lookup[n] = dyna_fib(n-1, lookup) + dyna_fib(n-2, lookup)
```

我们传递 lookup，以便在评估子问题时可以引用它。对`dyna_fib(n-1, lookup)`和`dyna_fib(n-2, lookup)`的调用存储在`lookup[n]`中。

当我们运行函数的更新实现以找到斐波那契数列的第`*i*^(th)`项时，我们可以看到显着的改进。这个实现比我们最初的实现运行得快得多。将值 20 提供给这两个实现，并观察执行速度的差异。

然而，由于使用额外的内存存储函数调用的结果，更新后的算法牺牲了空间复杂度。

# 表格法

动态规划中的第二种技术涉及使用结果表或在某些情况下使用矩阵来存储计算结果以供以后使用。

这种方法通过首先解决最终解决方案的路径来解决更大的问题。对于`fib()`函数，我们开发了一个表，其中预先确定了`fib(1)`和`fib(2)`的值。基于这两个值，我们将逐步计算`fib(n)`：

```py
    def fib(n): 

        results = [1, 1] 

        for i in range(2, n): 
            results.append(results[i-1] + results[i-2]) 

        return results[-1]
```

`results`变量在索引 0 和 1 处存储值 1 和 1。这代表`fib(1)`和`fib(2)`的返回值。要计算大于 2 的值的`fib()`函数的值，我们只需调用`for`循环，将`results[i-1] + results[i-2]`的和附加到结果列表中。

# 使用分治法实现

这种编程方法强调将问题分解为与原始问题相同类型或形式的较小子问题的需要。这些子问题被解决并组合以获得原始问题的解决方案。

以下三个步骤与这种编程相关。

# 划分

划分意味着分解实体或问题。在这里，我们设计手段将原始问题分解为子问题。我们可以通过迭代或递归调用来实现这一点。

# 征服

无法无限地继续将问题分解为子问题。在某个时候，最小的不可分割问题将返回一个解决方案。一旦这种情况发生，我们可以扭转我们的思维过程，并说如果我们知道最小子问题的解决方案，我们就可以获得原始问题的最终解决方案。

# 合并

为了得到最终解决方案，我们需要结合较小问题的解决方案来解决更大的问题。

还有其他变体的分而治之算法，例如合并和组合，征服和解决。许多算法使用分而治之原则，例如归并排序、快速排序和 Strassen 矩阵乘法。

我们现在将描述归并排序算法的实现，就像我们在第三章“算法设计原理”中看到的那样。

# 归并排序

归并排序算法基于分而治之的原则。给定一列未排序的元素，我们将列表分成两个近似的部分。我们继续递归地将列表分成两半。

经过一段时间，由于递归调用而创建的子列表将只包含一个元素。在那时，我们开始在征服或合并步骤中合并解决方案：

```py
    def merge_sort(unsorted_list): 
        if len(unsorted_list) == 1: 
            return unsorted_list 

        mid_point = int((len(unsorted_list))//2) 

        first_half = unsorted_list[:mid_point] 
        second_half = unsorted_list[mid_point:] 

        half_a = merge_sort(first_half) 
        half_b = merge_sort(second_half) 

        return merge(half_a, half_b) 
```

我们的实现从将未排序的元素列表传递到`merge_sort`函数开始。`if`语句用于建立基本情况，即如果`unsorted_list`中只有一个元素，我们只需再次返回该列表。如果列表中有多于一个元素，我们使用`mid_point = int((len(unsorted_list)) // 2)`找到近似中间位置。

使用这个`mid_point`，我们将列表分成两个子列表，即`first_half`和`second_half`：

```py
    first_half = unsorted_list[:mid_point] 
    second_half = unsorted_list[mid_point:] 
```

通过将这两个子列表再次传递给`merge_sort`函数来进行递归调用：

```py
    half_a = merge_sort(first_half)  
    half_b = merge_sort(second_half)
```

现在是合并步骤。当`half_a`和`half_b`传递了它们的值后，我们调用`merge`函数，该函数将合并或组合存储在`half_a`和`half_b`中的两个解决方案，即列表：

```py
 def merge(first_sublist, second_sublist): 
     i = j = 0 
     merged_list = [] 

     while i < len(first_sublist) and j < len(second_sublist): 
         if first_sublist[i] < second_sublist[j]: 
             merged_list.append(first_sublist[i]) 
             i += 1 
         else: 
             merged_list.append(second_sublist[j]) 
             j += 1 

     while i < len(first_sublist): 
         merged_list.append(first_sublist[i]) 
         i += 1 

     while j < len(second_sublist): 
         merged_list.append(second_sublist[j]) 
         j += 1 

     return merged_list 
```

`merge`函数接受我们要合并的两个列表`first_sublist`和`second_sublist`。`i`和`j`变量被初始化为 0，并用作指针，告诉我们在合并过程中两个列表的位置。

最终的`merged_list`将包含合并后的列表：

```py
    while i < len(first_sublist) and j < len(second_sublist): 
        if first_sublist[i] < second_sublist[j]: 
            merged_list.append(first_sublist[i]) 
            i += 1 
        else: 
            merged_list.append(second_sublist[j]) 
            j += 1 
```

`while`循环开始比较`first_sublist`和`second_sublist`中的元素。`if`语句选择两者中较小的一个，`first_sublist[i]`或`second_sublist[j]`，并将其附加到`merged_list`。`i`或`j`索引递增以反映我们在合并步骤中的位置。当任一子列表为空时，`while`循环停止。

可能会有元素留在`first_sublist`或`second_sublist`中。最后两个`while`循环确保这些元素在返回`merged_list`之前被添加。

对`merge(half_a, half_b)`的最后调用将返回排序后的列表。

让我们通过合并两个子列表`[4, 6, 8]`和`[5, 7, 11, 40]`来对算法进行干扰运行：

| **步骤** | `first_sublist` | `second_sublist` | `merged_list` |
| --- | --- | --- | --- |
| 步骤 0 | `[4 6 8]` | `[5 7 11 40]` | `[]` |
| 步骤 1 | `[ 6 8]` | `[5 7 11 40]` | `[4]` |
| 步骤 2 | `[ 6 8]` | `[ 7 11 40]` | `[4 5]` |
| 步骤 3 | `[ 8]` | `[ 7 11 40]` | `[4 5 6]` |
| 步骤 4 | `[ 8]` | `[ 11 40]` | `[4 5 6 7]` |
| 步骤 5 | `[ ]` | `[ 11 40]` | `[4 5 6 7 8]` |
| 步骤 6 | `[]` | `[ ]` | `[4 5 6 7 8 11 40]` |

注意粗体文本代表循环中当前项的引用，`first_sublist`（使用`i`索引）和`second_sublist`（使用`j`索引）。

在执行的这一点上，合并函数中的第三个`while`循环开始将 11 和 40 移入`merged_list`。返回的`merged_list`将包含完全排序的列表。

请注意，合并算法需要`O(n)`的时间，而合并排序算法的运行时间复杂度为`O(log n) T(n) = O(n)*O(log n) = O(n log n)`。

# 使用贪婪算法的实现

正如我们之前讨论的，贪婪算法做出决策以产生最佳的局部解决方案，从而提供最佳解决方案。这种技术的希望是，通过在每一步做出最佳选择，总路径将导致整体最优解决方案或结束。

贪婪算法的例子包括用于查找最小生成树的**Prim 算法**、**背包问题**和**旅行推销员问题**。

# 硬币计数问题

为了演示贪婪技术的工作原理，让我们看一个例子。考虑一个问题，我们希望计算使给定金额 A 所需的最小硬币数量，其中我们有给定硬币值的无限供应。

例如，在某个任意国家，我们有以下硬币面额：1、5 和 8 GHC。给定一个金额（例如 12 GHC），我们想要找到提供这个金额所需的最少硬币数量。

使用面额`{a[1],a[2],a[3]...a[n]}`来提供给定金额 A 的最小硬币数量的算法如下：

1.  我们对面额列表`{a[1], a[2], a[3] ...a[n]}`进行排序。

1.  我们得到小于 A 的`{a[1], a[2], a[3]...a[n]}`中的最大面额。

1.  我们通过将 A 除以最大面额来获得商。

1.  我们通过使用（A % 最大面额）来获得剩余金额 A。

1.  如果 A 的值变为 0，则返回结果。

1.  否则，如果 A 的值大于 0，我们将最大面额和商变量附加到结果变量中。并重复步骤 2-5。

使用贪婪方法，我们首先选择可用面额中的最大值——8——来除以 12。余数 4 既不能被 8 整除，也不能被比 8 更小的面额 5 整除。所以，我们尝试 1 GHC 面额的硬币，需要四个。最终，使用这种贪婪算法，我们返回了一个 8 GHC 硬币和四个 1 GHC 硬币的答案。

到目前为止，我们的贪婪算法似乎表现得相当不错。返回相应面额的函数如下：

```py
    def basic_small_change(denom, total_amount): 
        sorted_denominations = sorted(denom, reverse=True) 

        number_of_denoms = [] 

        for i in sorted_denominations: 
            div = total_amount // i 
            total_amount = total_amount % i 
            if div > 0: 
                number_of_denoms.append((i, div)) 

        return number_of_denoms
```

这种贪婪算法总是从可能的最大面额开始。注意`denom`是一个面额列表，`sorted(denom, reverse=True)`会将列表按照相反的顺序排序，这样我们就可以在索引`0`处获得最大面额。现在，从排序后的面额列表`sorted_denominations`的索引`0`开始，我们迭代并应用贪婪技术：

```py
    for i in sorted_denominations: 
        div = total_amount // i 
        total_amount = total_amount % i 
        if div > 0: 
            number_of_denoms.append((i, div)) 
```

循环将遍历面额列表。每次循环运行时，它通过将`total_amount`除以当前面额*i*来获得商`div`。`total_amount`变量被更新以存储余数以供进一步处理。如果商大于 0，我们将其存储在`number_of_denoms`中。

然而，有一些可能的情况下这种算法可能会失败。例如，当传入 12 GHC 时，我们的算法返回了一个 8 GHC 和四个 1 GHC 硬币。然而，这个输出并不是最优解。最佳解是使用两个 5 GHC 和两个 1 GHC 硬币。

这里提出了一个更好的贪婪算法。这次，函数返回一个允许我们调查最佳结果的元组列表：

```py
    def optimal_small_change(denom, total_amount): 

        sorted_denominations = sorted(denom, reverse=True) 

        series = [] 
        for j in range(len(sorted_denominations)): 
            term = sorted_denominations[j:] 

            number_of_denoms = [] 
            local_total = total_amount 
            for i in term: 
                div = local_total // i 
                local_total = local_total % i 
                if div > 0: 
                    number_of_denoms.append((i, div)) 

            series.append(number_of_denoms) 

        return series
```

外部`for`循环使我们能够限制我们找到解决方案的面额：

```py
    for j in range(len(sorted_denominations)): 
        term = sorted_denominations[j:] 
        ...     
```

假设我们有列表[5, 4, 3]，在`sorted_denominations`中对其进行切片`[j:]`有助于我们获得子列表[5, 4, 3]，[4, 3]和[3]，从中我们尝试找到正确的组合。

# 最短路径算法

最短路径问题要求我们找出图中节点之间最短可能的路径。在制定从点 A 到点 B 的最有效路径时，这对于地图和路径规划具有重要应用。

迪杰斯特拉算法是解决这个问题的一种非常流行的方法。该算法用于在图中找到从源到所有其他节点或顶点的最短距离。在这里，我们解释了如何使用贪婪方法来解决这个问题。

迪杰斯特拉算法适用于加权有向和无向图。该算法产生了从加权图中给定源节点 A 到最短路径的列表的输出。算法的工作原理如下：

1.  最初，将所有节点标记为未访问，并将它们从给定源节点的距离设置为无穷大（源节点设置为零）。

1.  将源节点设置为当前节点。

1.  对于当前节点，查找所有未访问的相邻节点；计算从源节点通过当前节点到该节点的距离。将新计算的距离与当前分配的距离进行比较，如果更小，则将其设置为新值。

1.  一旦我们考虑了当前节点的所有未访问的相邻节点，我们将其标记为已访问。

1.  接下来，考虑下一个未访问的节点，该节点距离源节点最近。重复步骤 2 到 4。

1.  当未访问节点的列表为空时，我们停止，这意味着我们已经考虑了所有未访问的节点。

考虑以下带有六个节点[A，B，C，D，E，F]的加权图的示例，以了解迪杰斯特拉算法的工作原理：

![](img/30207f74-106b-4b35-8d06-0cf1a2377a4f.png)

通过手动检查，最初看起来节点 A 和节点 D 之间的最短路径似乎是距离为 9 的直线。然而，最短路径意味着最低总距离，即使这包括几个部分。相比之下，从节点 A 到节点 E，然后到节点 F，最后到节点 D 的旅行将产生总距离为 7，这使得它成为更短的路径。

我们将使用单源最短路径算法。它将确定从原点（在本例中为 A）到图中任何其他节点的最短路径。

在第八章中，*图和其他算法*，我们讨论了如何用邻接列表表示图。我们使用邻接列表以及每条边上的权重/成本/距离来表示图，如下面的 Python 代码所示。表用于跟踪从图中源到任何其他节点的最短距离。Python 字典将用于实现此表。

这是起始表：

| **节点** | **距离源的最短距离** | **前一个节点** |
| --- | --- | --- |
| **A** | 0 | None |
| **B** | ∞ | None |
| **C** | ∞ | None |
| **D** | ∞ | None |
| **E** | ∞ | None |
| **F** | ∞ | None |

图和表的邻接列表如下：

```py
    graph = dict() 
    graph['A'] = {'B': 5, 'D': 9, 'E': 2} 
    graph['B'] = {'A': 5, 'C': 2} 
    graph['C'] = {'B': 2, 'D': 3} 
    graph['D'] = {'A': 9, 'F': 2, 'C': 3} 
    graph['E'] = {'A': 2, 'F': 3} 
    graph['F'] = {'E': 3, 'D': 2} 
```

嵌套字典保存了距离和相邻节点。

当算法开始时，给定源节点（A）到任何节点的最短距离是未知的。因此，我们最初将所有其他节点的距离设置为无穷大，除了节点 A，因为从节点 A 到节点 A 的距离为 0。

当算法开始时，没有先前的节点被访问。因此，我们将节点 A 的前一个节点列标记为 None。

在算法的第一步中，我们开始检查节点 A 的相邻节点。要找到从节点 A 到节点 B 的最短距离，我们需要找到从起始节点到节点 B 的前一个节点的距离，这恰好是节点 A，并将其添加到从节点 A 到节点 B 的距离。我们对 A 的其他相邻节点（B、E 和 D）也是这样做的。这在下图中显示：

![](img/9745d5ee-f528-4bd2-b67b-181e3655f5c4.png)

我们将相邻节点 B 作为其从节点 A 的距离最小；从起始节点（A）到前一个节点（None）的距离为 0，从前一个节点到当前节点（B）的距离为 5。这个和与节点 B 的最短距离列中的数据进行比较。由于 5 小于无穷大（∞），我们用两者中较小的 5 替换∞。

每当一个节点的最短距离被较小的值替换时，我们需要为当前节点的所有相邻节点更新前一个节点列。之后，我们将节点 A 标记为已访问：

![](img/220248fc-eec9-4d42-9d69-1ac4a8ca26a2.png)

在第一步结束时，我们的表如下所示：

| **节点** | **源的最短距离** | **前一个节点** |
| --- | --- | --- |
| **A*** | 0 | None |
| B | 5 | A |
| C | ∞ | None |
| D | 9 | A |
| E | 2 | A |
| F | ∞ | None |

此时，节点 A 被视为已访问。因此，我们将节点 A 添加到已访问节点的列表中。在表中，我们通过将文本加粗并在其后附加星号来显示节点 A 已被访问。

在第二步中，我们使用我们的表找到最短距离的节点作为指南。节点 E 的值为 2，具有最短距离。这是我们从节点 E 的表中可以推断出来的。要到达节点 E，我们必须访问节点 A 并覆盖距离 2。从节点 A，我们覆盖 0 的距离到达起始节点，即节点 A 本身。

节点 E 的相邻节点是 A 和 F。但是节点 A 已经被访问过，所以我们只考虑节点 F。要找到到节点 F 的最短路径或距离，我们必须找到从起始节点到节点 E 的距离，并将其添加到节点 E 和 F 之间的距离。我们可以通过查看节点 E 的最短距离列来找到从起始节点到节点 E 的距离，其值为 2。从节点 E 到 F 的距离可以从我们在本节早些时候开发的 Python 中的邻接列表中获得。

这个距离是 3。这两个加起来是 5，小于无穷大。记住我们正在检查相邻的节点 F。由于节点 E 没有更多相邻的节点，我们将节点 E 标记为已访问。我们更新的表和图将具有以下值：

| **节点** | **源的最短距离** | **前一个节点** |
| --- | --- | --- |
| **A*** | 0 | None |
| B | 5 | A |
| C | ∞ | None |
| D | 9 | A |
| **E*** | 2 | A |
| F | 5 | E |

访问节点 E 后，我们在表的最短距离列中找到最小值，即节点 B 和 F 的值为 5。让我们选择 B 而不是 F，纯粹基于字母顺序（我们也可以选择 F）。

节点 B 的相邻节点是 A 和 C，但是节点 A 已经被访问。根据我们之前建立的规则，从 A 到 C 的最短距离是 7。我们得到这个数字是因为从起始节点到节点 B 的距离是 5，而从节点 B 到 C 的距离是 2。

由于 7 小于无穷大，我们将最短距离更新为 7，并用节点 B 更新前一个节点列：

![](img/b6437e76-eaf8-4d3c-910e-cb5751588c6d.png)

现在，B 也被标记为已访问。表和图的新状态如下：

| **节点** | **源的最短距离** | **前一个节点** |
| --- | --- | --- |
| **A*** | 0 | None |
| **B*** | 5 | A |
| C | 7 | B |
| D | 9 | A |
| **E*** | 2 | A |
| F | 5 | E |

最短距离但尚未访问的节点是节点**F**。**F**的相邻节点是节点**D**和**E**。但是节点**E**已经被访问过。因此，我们专注于找到从起始节点到节点**D**的最短距离。

我们通过将从节点**A**到**F**的距离与从节点**F**到**D**的距离相加来计算这个距离。这相加得到 7，小于**9**。因此，我们将**9**更新为**7**，并在节点**D**的上一个节点列中用**F**替换**A**：

![](img/d4a2d0a8-6a4e-46d5-b469-37674c0617df.png)

节点**F**现在被标记为已访问。这是更新后的表格和到目前为止的图：

| **Node** | **Shortest distance from source** | **Previous node** |
| --- | --- | --- |
| **A*** | 0 | None |
| **B*** | 5 | A |
| C | 7 | B |
| D | 7 | F |
| **E*** | 2 | A |
| **F*** | 5 | E |

现在，只剩下两个未访问的节点，**C**和**D**，都具有距离成本为**7**。按字母顺序，我们选择检查**C**，因为这两个节点都与起始节点**A**的最短距离相同：

![](img/09e116c0-42d5-430f-95d8-3b10bf5f1788.png)

然而，所有与**C**相邻的节点都已经被访问。因此，我们除了将节点**C**标记为已访问外，没有其他事情要做。此时表格保持不变：

![](img/d5ea3951-91ea-4723-8337-4d655593f431.png)

最后，我们取节点**D**，发现它的所有相邻节点也都已经被访问。我们只将其标记为已访问。表格保持不变：

| **Node** | **Shortest distance from source** | **Previous node** |
| --- | --- | --- |
| **A*** | 0 | None |
| **B*** | 5 | A |
| **C*** | 7 | B |
| **D*** | 7 | F |
| **E*** | 2 | A |
| **F*** | 5 | E |

让我们用我们的初始图表来验证这个表格。从图表中，我们知道从**A**到**F**的最短距离是**5**。我们需要通过**E**到达节点**F**：

![](img/93a5e488-c767-400f-8fcb-4a27523a7e7d.png)

根据表格，从源列到节点**F**的最短距离是 5。这是正确的。它还告诉我们，要到达节点**F**，我们需要访问节点**E**，然后从**E**到节点**A**，这实际上是最短路径。

为了实现 Dijkstra 算法以找到最短路径，我们开始编写程序，通过表示能够跟踪图中变化的表格来找到最短距离。对于我们使用的图表，这是表格的字典表示：

```py
    table = dict() 
    table = { 
        'A': [0, None], 
        'B': [float("inf"), None], 
        'C': [float("inf"), None], 
        'D': [float("inf"), None], 
        'E': [float("inf"), None], 
        'F': [float("inf"), None], 
    } 
```

表格的初始状态使用`float("inf")`表示无穷大。字典中的每个键映射到一个列表。在列表的第一个索引处，存储了从源头 A 到达的最短距离。在第二个索引处，存储了上一个节点：

```py
    DISTANCE = 0 
    PREVIOUS_NODE = 1 
    INFINITY = float('inf') 
```

为了避免使用魔术数字，我们使用前面的常量。最短路径列的索引由`DISTANCE`引用。上一个节点列的索引由`PREVIOUS_NODE`引用。

现在一切都准备就绪，算法的主要函数将接受图（由邻接列表表示）、表格和起始节点作为参数：

```py
    def find_shortest_path(graph, table, origin): 
        visited_nodes = [] 
        current_node = origin 
        starting_node = origin 
```

我们将访问过的节点列表保存在`visited_nodes`列表中。`current_node`和`starting_node`变量都将指向我们选择的图中的起始节点。`origin`值是相对于找到最短路径的其他节点的参考点。

整个过程的重要工作是通过使用`while`循环完成的：

```py
    while True: 
        adjacent_nodes = graph[current_node] 
        if set(adjacent_nodes).issubset(set(visited_nodes)): 
            # Nothing here to do. All adjacent nodes have been visited. 
            pass 
        else: 
            unvisited_nodes = 
                set(adjacent_nodes).difference(set(visited_nodes)) 
            for vertex in unvisited_nodes: 
                distance_from_starting_node = 
                    get_shortest_distance(table, vertex) 
                if distance_from_starting_node == INFINITY and 
                   current_node == starting_node: 
                    total_distance = get_distance(graph, vertex, 
                                                  current_node) 
                else: 
                    total_distance = get_shortest_distance (table, 
                    current_node) + get_distance(graph, current_node, 
                                                 vertex) 

                if total_distance < distance_from_starting_node: 
                    set_shortest_distance(table, vertex, 
                                          total_distance) 
                    set_previous_node(table, vertex, current_node) 

        visited_nodes.append(current_node) 

        if len(visited_nodes) == len(table.keys()): 
            break 

        current_node = get_next_node(table,visited_nodes) 
```

让我们分解一下`while`循环在做什么。在`while`循环的主体中，我们获取我们想要调查的图中的当前节点，使用`adjacent_nodes = graph[current_node]`。现在，`current_node`应该在之前已经设置好。`if`语句用于查找`current_node`的所有相邻节点是否都已经被访问。

当`while`循环第一次执行时，`current_node`将包含 A，`adjacent_nodes`将包含节点 B、D 和 E。此外，`visited_nodes`也将为空。如果所有节点都已经被访问，我们只会继续执行程序中的其他语句。否则，我们将开始一个全新的步骤。

语句`set(adjacent_nodes).difference(set(visited_nodes))`返回尚未访问的节点。循环遍历这个未访问的节点列表：

```py
    distance_from_starting_node = get_shortest_distance(table, vertex) 
```

`get_shortest_distance(table, vertex)`辅助方法将返回我们表中最短距离列中存储的值，使用`vertex`引用的未访问节点之一：

```py
    if distance_from_starting_node == INFINITY and current_node == starting_node: 
         total_distance = get_distance(graph, vertex, current_node) 
```

当我们检查起始节点的相邻节点时，`distance_from_starting_node == INFINITY and current_node == starting_node` 将评估为 `True`，在这种情况下，我们只需要通过引用图找到起始节点和顶点之间的距离：

```py
    total_distance = get_distance(graph, vertex, current_node)
```

`get_distance`方法是我们用来获取`vertex`和`current_node`之间的边的值（距离）的另一个辅助方法。

如果条件失败，那么我们将把`total_distance`赋值为从起始节点到`current_node`的距离和`current_node`到`vertex`的距离之和。

一旦我们有了总距离，我们需要检查`total_distance`是否小于我们表中最短距离列中的现有数据。如果是，我们就使用这两个辅助方法来更新该行：

```py
    if total_distance < distance_from_starting_node: 
        set_shortest_distance(table, vertex, total_distance) 
    set_previous_node(table, vertex, current_node) 
```

此时，我们将`current_node`添加到已访问节点列表中：

```py
    visited_nodes.append(current_node) 
```

如果所有节点都已经被访问，那么我们必须退出`while`循环。为了检查所有节点是否都已经被访问，我们将`visited_nodes`列表的长度与我们表中的键的数量进行比较。如果它们相等，我们就简单地退出`while`循环。

`get_next_node`辅助方法用于获取下一个要访问的节点。正是这个方法帮助我们使用我们的表从起始节点中找到最短距离列中的最小值。

整个方法最终返回更新后的表。要打印表，我们使用以下语句：

```py
 shortest_distance_table = find_shortest_path(graph, table, 'A') 
 for k in sorted(shortest_distance_table): 
     print("{} - {}".format(k,shortest_distance_table[k])) 
```

这是前面语句的输出：

```py
>>> A - [0, None] B - [5, 'A'] C - [7, 'B'] D - [7, 'F'] E - [2, 'A'] F - [5, 'E']
```

为了完整起见，让我们找出这些辅助方法在做什么：

```py
    def get_shortest_distance(table, vertex): 
        shortest_distance = table[vertex][DISTANCE] 
        return shortest_distance 
```

`get_shortest_distance`函数返回我们表中索引 0 处存储的值。在该索引处，我们始终存储从起始节点到`vertex`的最短距离。`set_shortest_distance`函数只设置该值如下：

```py
    def set_shortest_distance(table, vertex, new_distance): 
        table[vertex][DISTANCE] = new_distance 
```

当我们更新节点的最短距离时，我们使用以下方法更新其上一个节点：

```py
    def set_previous_node(table, vertex, previous_node): 
        table[vertex][PREVIOUS_NODE] = previous_node 
```

请记住，`PREVIOUS_NODE`常量等于 1。在表中，我们将`previous_node`的值存储在`table[vertex][PREVIOUS_NODE]`处。

为了找到任意两个节点之间的距离，我们使用`get_distance`函数：

```py
    def get_distance(graph, first_vertex, second_vertex): 
        return graph[first_vertex][second_vertex] 
```

最后的辅助方法是`get_next_node`函数：

```py
    def get_next_node(table, visited_nodes): 
        unvisited_nodes = 
            list(set(table.keys()).difference(set(visited_nodes))) 
        assumed_min = table[unvisited_nodes[0]][DISTANCE] 
        min_vertex = unvisited_nodes[0] 
        for node in unvisited_nodes: 
            if table[node][DISTANCE] < assumed_min: 
                assumed_min = table[node][DISTANCE] 
                min_vertex = node 

        return min_vertex 
```

`get_next_node`函数类似于在列表中找到最小项的函数。

该函数首先通过使用`visited_nodes`来获取两个列表集合的差异来找到我们表中未访问的节点。`unvisited_nodes`列表中的第一项被假定为`table`中最短距离列中的最小值。

如果在`for`循环运行时找到了更小的值，`min_vertex`将被更新。然后函数将`min_vertex`作为未访问的顶点或距离源点最短的节点返回。

Dijkstra 算法的最坏运行时间是**O**(*|E| + |V| log |V|*)，其中*|V|*是顶点数，*|E|*是边数。

# 复杂度类

复杂度类根据问题的难度级别以及解决它们所需的时间和空间资源进行分组。在本节中，我们讨论了 N、NP、NP-Complete 和 NP-Hard 复杂度类。

# P 与 NP

计算机的出现加快了某些任务的执行速度。总的来说，计算机擅长完善计算的艺术和解决可以归结为一组数学计算的问题。

然而，这种说法并非完全正确。有一些类别的问题对计算机来说需要大量时间来做出合理的猜测，更不用说找到正确的解决方案了。

在计算机科学中，计算机可以使用逻辑步骤的逐步过程在多项式时间内解决的问题类别被称为 P 类型，其中 P 代表多项式。这些问题相对容易解决。

然后还有另一类被认为很难解决的问题。术语*难问题*用于指代在寻找解决方案时问题难度增加的方式。然而，尽管这些问题的难度增长率很高，但可以确定一个提议的解决方案是否在多项式时间内解决问题。这些被称为 NP 类型问题。这里的 NP 代表非确定性多项式时间。

现在百万美元的问题是，*P = NP*吗？

P* = NP*的证明是克莱数学研究所的百万美元问题之一，为正确解决方案提供了百万美元的奖金。

旅行推销员问题是 NP 类型问题的一个例子。问题陈述如下：在一个国家中给定*n*个城市，找到它们之间的最短路线，从而使旅行成本有效。

当城市数量较小时，这个问题可以在合理的时间内解决。然而，当城市数量超过两位数时，计算机所需的时间就会非常长。

许多计算机和网络安全系统都基于 RSA 加密算法。该算法的强度基于它使用的整数因子问题，这是一个 NP 类型问题。

找到由许多位数组成的质数的质因数是非常困难的。当两个大质数相乘时，得到一个大的非质数。这个数的因数分解是许多加密算法借用其强度的地方。

所有 P 类型问题都是**NP**问题的子集。这意味着任何可以在多项式时间内解决的问题也可以在多项式时间内验证：

![](img/cfbbc0d9-bc13-467e-aeb5-982db9ad6891.png)

但是**P** = **NP**调查了可以在多项式时间内验证的问题是否也可以在多项式时间内解决。特别是，如果它们相等，这意味着可以在不需要实际尝试所有可能的解决方案的情况下解决通过尝试多个可能解决方案来解决的问题，从而不可避免地产生某种快捷证明。

当最终发现证明时，它肯定会对密码学、博弈论、数学和许多其他领域产生严重影响。

# NP-Hard

如果 NP 中的所有其他问题都可以在多项式时间内可归约或映射到它，那么问题就是 NP-Hard。它至少和 NP 中最难的问题一样难。

# NP-Complete

**NP-Complete**问题是最困难的问题。如果一个问题是**NP-Hard**问题，同时也在**NP**类中找到，那么它被认为是**NP-Complete**问题。

在这里，我们展示了各种复杂性群的维恩图：

![](img/c47f7c29-0359-481b-b287-34e5ca2e9a45.png)

# 数据中的知识发现

为了从给定数据中提取有用信息，我们首先收集要用于学习模式的原始数据。接下来，我们应用数据预处理技术来去除数据中的噪音。此外，我们从数据中提取重要特征，这些特征代表了数据，用于开发模型。特征提取是机器学习算法有效工作的最关键步骤。一个好的特征必须对机器学习算法具有信息量和区分度。特征选择技术用于去除不相关、冗余和嘈杂的特征。此外，突出的特征被输入到机器学习算法中，以学习数据中的模式。最后，我们应用评估措施来评判开发模型的性能，并使用可视化技术来可视化结果和数据。以下是步骤：

1.  数据收集

1.  数据预处理

1.  特征提取

1.  特征选择

1.  机器学习

1.  评估和可视化

# 总结

在本章中，我们详细讨论了算法设计技术，在计算机科学领域非常重要。在没有太多数学严谨的情况下，我们还讨论了一些算法分类的主要类别。

该领域中的其他设计技术，如分治、动态规划和贪婪算法，也被涵盖，以及重要样本算法的实现。最后，我们对复杂度类进行了简要讨论。我们看到，如果 P = NP 的证明被发现，它肯定会在许多领域产生重大影响。

在下一章中，我们将讨论一些真实世界的应用、工具和机器学习应用的基础知识。
