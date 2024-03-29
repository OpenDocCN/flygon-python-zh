# 第九章：搜索

所有数据结构中最重要的操作之一是从存储的数据中搜索元素。有各种方法可以在数据结构中搜索元素；在本章中，我们将探讨可以用来在项目集合中查找元素的不同策略。

搜索操作对于排序非常重要。如果没有使用某种搜索操作的变体，几乎不可能对数据进行排序。如果搜索算法有效，排序算法将会很快。在本章中，我们将讨论不同的搜索算法。

搜索操作的性能受到即将搜索的项目是否已经排序的影响，我们将在后续章节中看到。

在本章结束时，您将能够做到以下事情：

+   了解各种搜索算法

+   了解流行搜索算法的实现

+   了解二分搜索算法的实现

+   了解插值的实现

# 技术要求

本章中使用的源代码可在以下 GitHub 链接找到：[`github.com/PacktPublishing/Hands-On-Data-Structures-and-Algorithms-with-Python-3.7-Second-Edition/tree/master/Chapter09`](https://github.com/PacktPublishing/Hands-On-Data-Structures-and-Algorithms-with-Python-3.7-Second-Edition/tree/master/Chapter09)。

# 搜索简介

搜索算法分为两种类型：

+   将搜索算法应用于已经排序的项目列表；即应用于有序的项目集

+   将搜索算法应用于未排序的项目集

# 线性搜索

*搜索*操作是为了从存储的数据中找出给定的项目。如果存储的列表中存在搜索的项目，则返回其所在的索引位置，否则返回未找到该项目。在列表中搜索项目的最简单方法是线性搜索方法，其中我们在整个列表中逐个查找项目。

让我们以`5`个列表项`{60, 1, 88, 10, 11, 100}`为例，以了解线性搜索算法，如下图所示：

![](img/03ac28e0-5e8e-46c6-89c4-92f96fb74625.png)

前面的列表中的元素可以通过列表索引访问。为了在列表中找到一个元素，我们使用线性搜索技术。该技术通过使用索引遍历元素列表，从列表的开头移动到末尾。每个元素都会被检查，如果它与搜索项不匹配，则会检查下一个项目。通过从一个项目跳到下一个项目，列表被顺序遍历。

本章中使用整数值的列表项来帮助您理解概念，因为整数可以很容易地进行比较；但是，列表项也可以保存任何其他数据类型。

# 无序线性搜索

线性搜索方法取决于列表项的存储方式-它们是否按顺序排序或无序存储。让我们首先看看列表是否包含未排序的项目。

考虑一个包含元素 60、1、88、10 和 100 的列表-一个无序列表。列表中的项目没有按大小排序。要在这样的列表上执行搜索操作，首先从第一个项目开始，将其与搜索项进行比较。如果搜索项不匹配，则检查列表中的下一个元素。这将继续进行，直到我们到达列表中的最后一个元素或找到匹配项为止。

以下是 Python 中对无序项目列表进行线性搜索的实现：

```py
    def search(unordered_list, term): 
       unordered_list_size = len(unordered_list) 
        for i in range(unordered_list_size): 
            if term == unordered_list[i]: 
                return i 

        return None 
```

`search`函数接受两个参数；第一个是保存我们数据的列表，第二个参数是我们正在寻找的项目，称为**搜索项**。

获取数组的大小并确定`for`循环执行的次数。以下代码描述了这一点：

```py
        if term == unordered_list[i]: 
            ... 
```

在`for`循环的每次通过中，我们测试搜索项是否等于索引项。如果为真，则表示匹配，无需继续搜索。我们返回在列表中找到搜索项的索引位置。

如果循环运行到列表的末尾而没有找到匹配项，则返回`None`，表示列表中没有这样的项目。

在无序的项目列表中，没有指导规则来插入元素。因此，它影响了搜索的执行方式。因此，我们必须依次访问列表中的所有项目。如下图所示，对术语**66**的搜索从第一个元素开始，并移动到列表中的下一个元素。

因此，首先将**60**与**66**进行比较，如果不相等，我们将**66**与下一个元素**1**进行比较，然后是**88**，依此类推，直到在列表中找到搜索项为止：

![](img/ba66df36-c90d-478a-a31d-9baa5b3407b0.png)

无序线性搜索的最坏情况运行时间为`O(n)`。在找到搜索项之前，可能需要访问所有元素。最坏情况是搜索项位于列表的最后位置。

# 有序线性搜索

线性搜索的另一种情况是，当列表元素已经排序时，我们的搜索算法可以得到改进。假设元素已按升序排序，则搜索操作可以利用列表的有序性使搜索更加高效。

算法简化为以下步骤：

1.  按顺序移动列表

1.  如果搜索项大于循环中当前检查的对象或项，则退出并返回`None`

在遍历列表的过程中，如果搜索项大于当前项，则无需继续搜索。

让我们考虑一个示例来看看这是如何工作的。我们拿一个项目列表，如下图所示，并且我们想搜索术语`5`：

![](img/409f41bf-abb5-425d-a412-05fa3f565249.png)

当搜索操作开始并且第一个元素与搜索项（**5**）进行比较时，找不到匹配项。但是，列表中还有更多元素，因此搜索操作继续检查下一个元素。在排序列表中继续前进的更有力的原因是，我们知道搜索项可能与大于**2**的任何元素匹配。

经过第四次比较，我们得出结论，搜索项无法在列表中后面的任何位置找到**6**所在的位置。换句话说，如果当前项大于搜索项，则表示无需进一步搜索列表。

以下是列表已排序时线性搜索的实现：

```py
    def search(ordered_list, term): 
        ordered_list_size = len(ordered_list) 
        for i in range(ordered_list_size): 
            if term == ordered_list[i]: 
                return i 
            elif ordered_list[i] > term: 
                return None 

        return None 
```

在上述代码中，`if`语句现在用于检查搜索项是否在列表中找到。`elif`测试条件为`ordered_list[i] > term`。如果比较结果为`True`，则该方法返回`None`。

方法中的最后一行返回`None`，因为循环可能会遍历列表，但搜索项仍未在列表中匹配。

有序线性搜索的最坏情况时间复杂度为`O(n)`。一般来说，这种搜索被认为是低效的，特别是在处理大型数据集时。

# 二进制搜索

二进制搜索是一种搜索策略，用于在**排序**的数组或列表中查找元素；因此，二进制搜索算法从给定的排序项目列表中找到给定的项目。这是一种非常快速和高效的搜索元素的算法，唯一的缺点是我们需要一个排序的列表。二进制搜索算法的最坏情况运行时间复杂度为`O(log n)`，而线性搜索的复杂度为`O(n)`。

二分搜索算法的工作方式如下。它通过将给定的列表分成两半来开始搜索项。如果搜索项小于中间值，则只在列表的前半部分查找搜索项，如果搜索项大于中间值，则只在列表的后半部分查找。我们重复相同的过程，直到找到搜索项或者我们已经检查了整个列表。

让我们通过一个例子来理解二分搜索。假设我们有一本 1000 页的书，我们想找到第 250 页。我们知道每本书的页码是从`1`开始顺序编号的。因此，根据二分搜索的类比，我们首先检查搜索项 250，它小于 500（书的中点）。因此，我们只在书的前半部分搜索所需的页面。然后我们再次看到书的前半部分的中点，即使用 500 页作为参考，我们找到中点，即 250。这使我们更接近找到第 250 页。然后我们在书中找到所需的页面。

让我们考虑另一个例子来理解二分搜索的工作原理。我们想从包含 12 个项的列表中搜索**43**，如下图所示：

![](img/692a7fab-d183-4d56-810f-b0ba163436cc.png)

我们通过将其与列表的中间项进行比较来开始搜索项，例如在示例中是**37**。如果搜索项小于中间值，则只查看列表的前半部分；否则，我们将查看另一半。因此，我们只需要在列表的后半部分搜索项。我们遵循相同的概念，直到在列表中找到搜索项**43**，如前图所示。

以下是对有序物品列表进行二分搜索算法的实现：

```py
def binary_search(ordered_list, term): 

    size_of_list = len(ordered_list) - 1 
    index_of_first_element = 0 
    index_of_last_element = size_of_list 
    while index_of_first_element <= index_of_last_element: 
        mid_point = (index_of_first_element + index_of_last_element)//2 
        if ordered_list[mid_point] == term: 
            return mid_point 
        if term > ordered_list[mid_point]: 
            index_of_first_element = mid_point + 1 
        else: 
            index_of_last_element = mid_point - 1 
    if index_of_first_element > index_of_last_element: 
        return None
```

假设我们要找到列表中**10**的位置如下：

![](img/c6fea9e7-dc50-4b14-a11a-c80121e372d0.png)

该算法使用`while`循环来迭代地调整列表中的限制，以便找到搜索项。停止`while`循环的终止条件是起始索引`index_of_first_element`和`index_of_last_element`索引之间的差值应为正数。

该算法首先通过将第一个元素的索引（**0**）加上最后一个元素的索引（**4**）并除以**2**来找到列表的中点，`mid_point`：

```py
mid_point = (index_of_first_element + index_of_last_element)//2 
```

在这种情况下，中点是`100`，值**10**不在列表的中间位置找到。由于我们正在搜索**10**，它小于中点，因此它位于列表的前半部分，因此，我们将索引范围调整为`index_of_first_element`到`mid_point-1`，如下图所示。然而，如果我们正在搜索**120**，在这种情况下，由于 120 大于中间值（100），我们将在列表的后半部分搜索项，并且我们需要将列表索引范围更改为`mid_point +1`到`index_of_last_element`。如下图所示：

![](img/d775d66a-c695-499a-b072-9627fa419a4d.png)

现在我们的`index_of_first_element`和`index_of_last_element`的新索引分别为**0**和**1**，我们计算中点`(0 + 1)/2`，得到`0`。新的中点是**0**，因此我们找到中间项并将其与搜索项进行比较，`ordered_list[0]`，得到值**10**。现在我们找到了搜索项，并返回索引位置。

通过调整`index_of_first_element`和`index_of_last_element`的索引，我们将列表大小减半，只要`index_of_first_element`小于`index_of_last_element`。当这种情况不再成立时，很可能是我们要搜索的项不在列表中。

我们讨论的实现是迭代的。我们也可以通过应用相同的原则并移动标记搜索列表开头和结尾的指针来开发算法的递归变体。考虑以下代码：

```py
def binary_search(ordered_list, first_element_index, last_element_index, term): 

    if (last_element_index < first_element_index): 
        return None 
    else: 
        mid_point = first_element_index + ((last_element_index - first_element_index) // 2) 

        if ordered_list[mid_point] > term: 
            return binary_search(ordered_list, first_element_index, mid_point-1,term) 
        elif ordered_list[mid_point] < term: 
            return binary_search(ordered_list, mid_point+1, last_element_index, term) 
        else: 
            return mid_point 
```

对二分搜索算法的递归实现的调用及其输出如下：

```py
    store = [2, 4, 5, 12, 43, 54, 60, 77]
    print(binary_search(store, 0, 7, 2))   

Output:
>> 0
```

在这里，递归二分搜索和迭代二分搜索之间唯一的区别是函数定义，以及计算`mid_point`的方式。在`((last_element_index - first_element_index) // 2)`操作之后，`mid_point`的计算必须将其结果加到`first_element_index`上。这样，我们定义了尝试搜索的列表部分。

二分搜索算法的最坏情况时间复杂度为`O(log n)`。每次迭代中列表的一半遵循元素数量和它们的进展的`log(n)`。

不言而喻，`log x`假定是指以 2 为底的对数。

# 插值搜索

插值搜索算法是二分搜索算法的改进版本。当排序列表中的元素均匀分布时，它的性能非常高。在二分搜索中，我们总是从列表的中间开始搜索，而在插值搜索中，我们根据要搜索的项确定起始位置。在插值搜索算法中，起始搜索位置很可能最接近列表的开头或结尾，具体取决于搜索项。如果搜索项接近列表中的第一个元素，则起始搜索位置很可能靠近列表的开头。

插值搜索是二分搜索算法的另一种变体，与人类在任何项目列表上执行搜索的方式非常相似。它基于尝试猜测在排序项目列表中可能找到搜索项的索引位置。它的工作方式类似于二分搜索算法，只是确定分割标准以减少比较次数的方法不同。在二分搜索的情况下，我们将数据分成相等的两部分，在插值搜索的情况下，我们使用以下公式来分割数据：

```py
mid_point = lower_bound_index + (( upper_bound_index - lower_bound_index)// (input_list[upper_bound_index] - input_list[lower_bound_index])) * (search_value - input_list[lower_bound_index]) 
```

在上述公式中，`lower_bound_index`变量是下界索引，即列表中最小值的索引，`upper_bound_index`表示列表中最大值的索引位置。`input_list[lower_bound_index]`和`input_list[lower_bound_index]`变量分别是列表中的最小值和最大值。`search_term`变量包含要搜索的项的值。

让我们通过以下包含 7 个项目的列表来考虑一个示例，以了解插值搜索算法的工作原理：

![](img/69b60fe9-60b7-4ff4-a1c2-e7d5ed404713.png)

为了找到**120**，我们知道应该查看列表的右侧部分。我们对二分搜索的初始处理通常会首先检查中间元素，以确定是否与搜索项匹配。

更像人类的方法是选择一个中间元素，以便不仅将数组分成两半，而且尽可能接近搜索项。中间位置是使用以下规则计算的：

```py
mid_point = (index_of_first_element + index_of_last_element)//2 
```

在插值搜索算法的情况下，我们将用更好的公式替换这个公式，以使我们更接近搜索项。`mid_point`将接收`nearest_mid`函数的返回值，该值是使用以下方法计算的：

```py
def nearest_mid(input_list, lower_bound_index, upper_bound_index, search_value):

    return lower_bound_index + (( upper_bound_index -lower_bound_index)// (input_list[upper_bound_index] -input_list[lower_bound_index])) * (search_value -input_list[lower_bound_index]) 
```

`nearest_mid`函数的参数是要进行搜索的列表。`lower_bound_index`和`upper_bound_index`参数表示希望在列表中找到搜索项的范围。此外，`search_value`表示正在搜索的值。

给定我们的搜索列表，**44**，**60**，**75**，**100**，**120**，**230**和**250**，`nearest_mid`将使用以下值进行计算：

```py
lower_bound_index = 0
upper_bound_index = 6
input_list[upper_bound_index] = 250
input_list[lower_bound_index] = 44
search_value = 230
```

让我们计算`mid_point`的值：

```py
mid_point= 0 + (6-0)//(250-44) * (230-44)
         = 5 
```

现在可以看到`mid_point`的值将接收值`5`。因此，在插值搜索的情况下，算法将从索引位置`5`开始搜索，这是我们搜索词的位置索引。因此，要搜索的项将在第一次比较中找到，而在二分搜索的情况下，我们将选择**100**作为`mid_point`，这将需要再次运行算法。

以下是一个更直观的例子，说明了典型的二分搜索与插值搜索的不同之处。在典型的二分搜索中，它找到了看起来在列表中间的**中点**：

![可以看到**中点**实际上站在前面列表的大致中间。这是通过将列表分成两部分得到的结果。另一方面，在插值搜索的情况下，**中点**被移动到最有可能匹配项的位置：![](img/fdb75873-7f16-4c81-bbe3-a62b392095ad.png)

在插值搜索中，**中点**通常更靠左或更靠右。这是由于在除法时使用的乘数的影响。在前面的图表中，我们的**中点**已经向右倾斜。

插值算法的实现与二分搜索的实现相同，只是我们计算**中点**的方式不同。

在这里，我们提供了插值搜索算法的实现，如下所示的代码：

```py
def interpolation_search(ordered_list, term): 

    size_of_list = len(ordered_list) - 1 

    index_of_first_element = 0 
    index_of_last_element = size_of_list 

    while index_of_first_element <= index_of_last_element: 
        mid_point = nearest_mid(ordered_list, index_of_first_element, index_of_last_element, term) 

        if mid_point > index_of_last_element or mid_point < index_of_first_element: 
            return None 

        if ordered_list[mid_point] == term: 
            return mid_point 

        if term > ordered_list[mid_point]: 
            index_of_first_element = mid_point + 1 
        else: 
            index_of_last_element = mid_point - 1 

    if index_of_first_element > index_of_last_element: 
        return None 
```

`nearest_mid`函数使用乘法运算。这可能会产生大于`upper_bound_index`或小于`lower_bound_index`的值。当发生这种情况时，意味着搜索词`term`不在列表中。因此，返回`None`表示这一点。

那么当`ordered_list[mid_point]`不等于搜索词时会发生什么呢？好吧，我们现在必须重新调整`index_of_first_element`和`index_of_last_element`，以便算法将专注于可能包含搜索词的数组部分。这与我们在二分搜索中所做的事情完全相同：

```py
if term > ordered_list[mid_point]: 
    index_of_first_element = mid_point + 1 
```

如果搜索词大于`ordered_list[mid_point]`存储的值，那么我们只需调整`index_of_first_element`变量，指向`mid_point + 1`索引。

以下图表显示了调整的过程。`index_of_first_element`被调整并指向`mid_point+1`索引：

![](img/b4d74ddb-972e-4f1b-8f31-404863c144c7.png)

图表只是说明了中点的调整。在插值搜索中，中点很少将列表分成两个完全相等的部分。

另一方面，如果搜索词小于`ordered_list[mid_point]`存储的值，那么我们只需调整`index_of_last_element`变量，指向索引`mid_point - 1`。这个逻辑在 if 语句的 else 部分中体现：`index_of_last_element = mid_point - 1`。

![](img/62be8deb-9a92-4fcc-bb9a-29e33f89e048.png)

图表显示了对**index_of_last_element**的重新计算对**中点**位置的影响。

让我们用一个更实际的例子来理解二分搜索和插值搜索算法的内部工作原理。

例如，考虑以下元素列表：

```py
[ 2, 4, 5, 12, 43, 54, 60, 77] 
```

在索引 0 处存储值 2，在索引 7 处存储值 77。现在，假设我们要在列表中找到元素 2。这两种不同的算法将如何处理？

如果我们将这个列表传递给`interpolation search`函数，那么`nearest_mid`函数将使用`mid_point`计算公式返回等于`0`的值：

```py
mid_point= 0 + (7-0)//(77-2) * (2-2)
         = 0 
```

当我们得到`mid_point`值`0`时，我们从索引`0`开始插值搜索。只需一次比较，我们就找到了搜索项。

另一方面，二分搜索算法需要三次比较才能找到搜索项，如下图所示：

![

计算得到的第一个`mid_point`值为`3`。第二个`mid_point`值为`1`，最后一个`mid_point`值为搜索项所在的`0`。

因此，很明显，插值搜索算法在大多数情况下比二分搜索效果更好。

# 选择搜索算法

与有序和无序线性搜索函数相比，二分搜索和插值搜索算法在性能上更好。由于有序和无序线性搜索在列表中顺序探测元素以找到搜索项，因此其时间复杂度为`O(n)`。当列表很大时，性能非常差。

另一方面，二分搜索操作在每次搜索尝试时都会将列表切成两半。在每次迭代中，我们比线性策略更快地接近搜索项。时间复杂度为`O(log n)`。尽管使用二分搜索可以获得速度上的优势，但其主要缺点是不能应用于未排序的项目列表，也不建议用于小型列表，因为排序的开销很大。

能够到达包含搜索项的列表部分在很大程度上决定了搜索算法的性能。在插值搜索算法中，中点的计算方式使得更有可能更快地获得我们的搜索项。插值搜索的平均情况时间复杂度为`O(log(log n))`，而最坏情况时间复杂度为`O(n)`。这表明插值搜索比二分搜索更好，并在大多数情况下提供更快的搜索。

# 总结

在本章中，我们讨论了两种重要的搜索算法类型。讨论了线性和二分搜索算法的实现以及它们的比较。本章还详细讨论了二分搜索变体插值搜索。

在下一章中，我们将使用搜索算法的概念进行排序算法。我们还将利用已经获得的知识对项目列表执行排序算法。
