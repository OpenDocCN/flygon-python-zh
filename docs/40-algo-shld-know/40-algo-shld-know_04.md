# 第四章：排序和搜索算法

在本章中，我们将看一下用于排序和搜索的算法。这是一类重要的算法，可以单独使用，也可以成为更复杂算法的基础（在本书的后面章节中介绍）。本章首先介绍了不同类型的排序算法。它比较了各种设计排序算法的方法的性能。然后，详细介绍了一些搜索算法。最后，探讨了本章介绍的排序和搜索算法的一个实际例子。

在本章结束时，您将能够理解用于排序和搜索的各种算法，并能够理解它们的优势和劣势。由于搜索和排序算法是大多数更复杂算法的基础，详细了解它们将有助于您理解现代复杂算法。

以下是本章讨论的主要概念：

+   介绍排序算法

+   介绍搜索算法

+   一个实际的例子

让我们首先看一些排序算法。

# 介绍排序算法

在大数据时代，能够高效地对复杂数据结构中的项目进行排序和搜索是非常重要的，因为许多现代算法都需要这样做。正确的排序和搜索数据的策略将取决于数据的大小和类型，正如本章所讨论的。虽然最终结果是完全相同的，但对于实际问题的高效解决方案，需要正确的排序和搜索算法。

本章介绍了以下排序算法：

+   冒泡排序

+   归并排序

+   插入排序

+   希尔排序

+   选择排序

# 在 Python 中交换变量

在实现排序和搜索算法时，我们需要交换两个变量的值。在 Python 中，有一种简单的交换两个变量的方式，如下所示：

```py
var1 = 1 
var2 = 2 
var1,var2 = var2,var1
>>> print (var1,var2)
>>> 2 1
```

让我们看看它是如何工作的：

![](img/943d98a8-6cea-43f1-b3dc-1a96ed14a074.png)

在本章中，这种简单的交换值的方式在排序和搜索算法中被广泛使用。

让我们从下一节开始看冒泡排序算法。

# 冒泡排序

冒泡排序是用于排序的最简单和最慢的算法。它被设计成这样，列表中的最高值会在算法循环迭代时冒泡到顶部。正如之前讨论的，它的最坏情况性能是 O(N²)，因此应该用于较小的数据集。

# 理解冒泡排序背后的逻辑

冒泡排序基于各种迭代，称为**通行**。对于大小为*N*的列表，冒泡排序将有*N*-1 个通行。让我们专注于第一次迭代：第一遍。

第一遍的目标是将最高的值推到列表的顶部。随着第一遍的进行，我们会看到列表中最高的值冒泡到顶部。

冒泡排序比较相邻的邻居值。如果较高位置的值比较低索引处的值要大，我们交换这些值。这种迭代会一直持续，直到我们到达列表的末尾。如下图所示：

![](img/1f24bd5d-ae99-4c23-b504-bdb9941e21d7.png)

现在让我们看看如何使用 Python 实现冒泡排序：

```py
#Pass 1 of Bubble Sort
lastElementIndex = len(list)-1 
print(0,list) 
for idx in range(lastElementIndex):                 
                    if list[idx]>list[idx+1]:                                                                             list[idx],list[idx+1]=list[idx+1],list[idx]                                         
print(idx+1,list)
```

如果我们在 Python 中实现冒泡排序的第一遍，它将如下所示：

![](img/4dd07ced-d8e2-4baa-a32d-73496686b03c.png)

第一遍完成后，最高的值在列表的顶部。算法接下来进行第二遍。第二遍的目标是将第二高的值移动到列表中第二高的位置。为了做到这一点，算法将再次比较相邻的邻居值，如果它们不按顺序，则交换它们。第二遍将排除顶部元素，它已经被第一遍放在正确的位置上，不需要再次触摸。

完成第二次通行证后，算法将继续执行第三次通行证，直到列表的所有数据点按升序排列。算法将需要*N*-1 次通行证来完全对大小为*N*的列表进行排序。Python 中冒泡排序的完整实现如下：

![](img/8112422d-7d3a-4910-b2e9-f1240a431250.png)

现在让我们来看一下`BubbleSort`算法的性能。

# 冒泡排序的性能分析

很容易看出，冒泡排序涉及两个级别的循环：

+   **外部循环**：这也被称为**通行证**。例如，通行证一是外部循环的第一次迭代。

+   **内部循环**：这是当列表中剩余的未排序元素被排序时，直到最大值被冒泡到右侧。第一次通行证将有*N*-1 次比较，第二次通行证将有*N*-2 次比较，每次后续通行证将减少一次比较。

由于两个级别的循环，最坏情况下的运行时复杂度将是 O(n²)。

# 插入排序

插入排序的基本思想是，在每次迭代中，我们从数据结构中移除一个数据点，然后将其插入到正确的位置。这就是为什么我们称之为**插入排序算法**。在第一次迭代中，我们选择两个数据点并对它们进行排序。然后，我们扩展我们的选择并选择第三个数据点，并根据其值找到其正确的位置。算法进行到所有数据点都移动到它们的正确位置。这个过程如下图所示：

![](img/8ba25bc4-942a-4704-abbe-558b0a521f69.png)

插入排序算法可以在 Python 中编写如下：

```py
def InsertionSort(list):        
    for i in range(1, len(list)):             
        j = i-1             
        element_next = list[i]             

        while (list[j] > element_next) and (j >= 0):                 
            list[j+1] = list[j]                 
            j=j-1                 
        list[j+1] = element_next
    return list
```

请注意，在主循环中，我们遍历整个列表。在每次迭代中，两个相邻的元素分别是`list[j]`（当前元素）和`list[i]`（下一个元素）。

在`list[j] > element_next`和`j >= 0`中，我们将当前元素与下一个元素进行比较。

让我们使用这段代码来对数组进行排序：

![](img/f82e873e-fb1a-4e6f-a195-799328961525.png)

让我们来看一下插入排序算法的性能。

从算法描述中很明显，如果数据结构已经排序，插入排序将执行得非常快。实际上，如果数据结构已排序，那么插入排序将具有线性运行时间；即 O(n)。最坏情况是每个内部循环都必须移动列表中的所有元素。如果内部循环由*i*定义，插入排序算法的最坏情况性能如下所示：

![](img/23bd35a5-d92c-42c8-80d7-c85821d98ffb.png)

*![](img/e136b412-be8f-4725-91d1-ab716386d1b8.png)*

总通行证数量如下图所示：

![](img/03f8b384-ace0-401b-986f-d9fdfca57d1f.png)

一般来说，插入可以用于小型数据结构。对于较大的数据结构，由于二次平均性能，不建议使用插入排序。

# 归并排序

到目前为止，我们已经介绍了两种排序算法：冒泡排序和插入排序。如果数据部分排序，它们的性能都会更好。本章介绍的第三种算法是**归并排序算法**，它是由约翰·冯·诺伊曼于 1940 年开发的。该算法的特点是其性能不依赖于输入数据是否排序。与 MapReduce 和其他大数据算法一样，它基于分而治之的策略。在第一阶段，称为拆分，算法继续递归地将数据分成两部分，直到数据的大小小于定义的阈值。在第二阶段，称为**合并**，算法继续合并和处理，直到我们得到最终结果。该算法的逻辑如下图所示：

![](img/bf2a1910-f8d4-4703-95e6-b68fe5a7ab3f.png)

让我们首先看一下归并排序算法的伪代码：

```py
mergeSort(list, start, end) 
    if(start < end) 
        midPoint = (end - start) / 2 + start           
        mergeSort(list, start, midPoint)             
        mergeSort(list, midPoint + 1, start)         
        merge(list, start, midPoint, end) 
```

正如我们所看到的算法有以下三个步骤：

1.  它将输入列表分成两个相等的部分

1.  它使用递归分割，直到每个列表的长度为 1

1.  然后，它将排序好的部分合并成一个排序好的列表并返回它

实现`MergeSort`的代码如下所示：

![](img/90b41c82-5962-42c7-b781-57a59557f994.png)

当运行上述 Python 代码时，它会生成以下输出：

![](img/ff2f2912-318f-418a-800c-331c8ad48f00.png)

请注意，代码的结果是一个排序好的列表。

# 谢尔排序

冒泡排序算法比较相邻的元素，如果它们的顺序不正确，则交换它们。如果我们有一个部分排序的列表，冒泡排序应该会有合理的性能，因为它会在循环中不再发生元素交换时立即退出。

但对于完全无序的大小为*N*的列表，你可以说冒泡排序将不得不完全迭代*N*-1 次才能完全排序它。

唐纳德·谢尔提出了谢尔排序（以他的名字命名），质疑了选择相邻元素进行比较和交换的重要性。

现在，让我们理解这个概念。

在第一次通过中，我们不是选择相邻的元素，而是使用固定间隔的元素，最终对由一对数据点组成的子列表进行排序。如下图所示。在第二次通过中，它对包含四个数据点的子列表进行排序（见下图）。在后续的通过中，每个子列表中的数据点数量不断增加，子列表的数量不断减少，直到我们达到只有一个包含所有数据点的子列表的情况。此时，我们可以假设列表已排序：

![](img/495145de-2a5c-4d41-aceb-2a95e51cc8e6.png)

在 Python 中，实现谢尔排序算法的代码如下所示：

```py
def ShellSort(list):     
    distance = len(list) // 2     
    while distance > 0:         
        for i in range(distance, len(list)):             
            temp = input_list[i]             
            j = i 
# Sort the sub list for this distance 
           while j >= distance and list[j - distance] > temp: 
              list[j] = list[j - distance] 
              j = j-distance            
          list[j] = temp 
# Reduce the distance for the next element         
        distance = distance//2
    return list
```

上述代码可以用于对列表进行排序，如下所示：

![](img/0749878d-e9b5-4f42-8610-19075022e645.png)

请注意，调用`ShellSort`函数已导致对输入数组进行排序。

# 谢尔排序的性能分析

谢尔排序不适用于大数据。它用于中等大小的数据集。粗略地说，它在具有多达 6000 个元素的列表上具有相当不错的性能。如果数据部分处于正确的顺序，性能会更好。在最佳情况下，如果列表已经排序，它只需要通过*N*个元素进行一次验证，产生*O(N)*的最佳性能。

# 选择排序

正如我们在本章前面看到的，冒泡排序是最简单的排序算法之一。选择排序是对冒泡排序的改进，我们试图最小化算法所需的总交换次数。它旨在使每次通过只进行一次交换，而不是冒泡排序算法的*N*-1 次通过。我们不是像冒泡排序中那样逐步将最大值向顶部冒泡（导致*N*-1 次交换），而是在每次通过中寻找最大值并将其向顶部移动。因此，在第一次通过后，最大值将位于顶部。第二次通过后，第二大的值将位于顶部值旁边。随着算法的进行，后续的值将根据它们的值移动到它们的正确位置。最后一个值将在第(*N*-1)次通过后移动。因此，选择排序需要*N*-1 次通过来排序*N*个项目：

![](img/6e5e3334-1189-42eb-bb80-cac42d97f922.png)

Python 中选择排序的实现如下所示：

```py
def SelectionSort(list):     
    for fill_slot in range(len(list) - 1, 0, -1):         
        max_index = 0         
        for location in range(1, fill_slot + 1):             
            if list[location] > list[max_index]:                 
                max_index = location         
        list[fill_slot],list[max_index] = list[max_index],list[fill_slot]
```

当选择排序算法被执行时，它将产生以下输出：

![](img/d9494a36-e503-48a3-ad9f-b52b30611260.png)

请注意，最终输出是排序好的列表。

# 选择排序算法的性能

选择排序的最坏情况性能是*O(N**²)*。注意，它的最坏性能类似于冒泡排序，不应该用于对更大的数据集进行排序。尽管如此，选择排序比冒泡排序更好设计，并且由于交换次数的减少，其平均性能比冒泡排序更好。

# 选择排序算法

选择正确的排序算法取决于当前输入数据的大小和状态。对于已排序的小输入列表，使用高级算法会在代码中引入不必要的复杂性，而性能改善微乎其微。例如，对于较小的数据集，我们不需要使用归并排序。冒泡排序会更容易理解和实现。如果数据部分排序，我们可以利用插入排序。对于较大的数据集，归并排序算法是最好的选择。

# 搜索算法简介

在复杂数据结构中高效地搜索数据是最重要的功能之一。最简单的方法是在每个数据点中搜索所需的数据，但随着数据规模的增大，我们需要更复杂的为搜索数据设计的算法。

本节介绍了以下搜索算法：

+   线性搜索

+   二分搜索

+   插值搜索

让我们更详细地看看它们每一个。

# 线性搜索

搜索数据的最简单策略之一是简单地循环遍历每个元素寻找目标。每个数据点都被搜索匹配，当找到匹配时，结果被返回并且算法退出循环。否则，算法会继续搜索直到达到数据的末尾。线性搜索的明显缺点是由于固有的穷举搜索，它非常慢。优点是数据不需要像本章介绍的其他算法那样需要排序。

让我们来看一下线性搜索的代码：

```py
def LinearSearch(list, item):     
    index = 0     
    found = False 
# Match the value with each data element     
    while index < len(list) and found is False:         
        if list[index] == item:             
            found = True         
    else:             
        index = index + 1     
  return found
```

现在让我们来看一下前面代码的输出：

![](img/8406c7c9-2fee-4e53-be31-6c5efcd71876.png)

注意，运行`LinearSearch`函数会在成功找到数据时返回`True`值。

# 线性搜索的性能

正如讨论的那样，线性搜索是一种执行穷举搜索的简单算法。它的最坏情况行为是*O(N)*。

# 二分搜索

二分搜索算法的先决条件是排序数据。该算法迭代地将列表分成两部分，并跟踪最低和最高索引，直到找到所寻找的值：

```py
def BinarySearch(list, item): 
   first = 0 
   last = len(list)-1 
   found = False 

while first<=last and not found:         
    midpoint = (first + last)//2         
    if list[midpoint] == item:             
        found = True         
    else:             
        if item < list[midpoint]:                 
            last = midpoint-1             
        else:                 
            first = midpoint+1     
return found
```

输出如下：

![](img/4b23f9aa-ada6-4d28-841b-eea4256a3c7e.png)

注意，调用`BinarySearch`函数将在输入列表中找到值时返回`True`。

# 二分搜索的性能

二分搜索之所以被这样命名，是因为在每次迭代中，算法将数据分成两部分。如果数据有*N*个项目，迭代最多需要 O(logN)步。这意味着算法具有*O(logN)*的运行时间。

# 插值搜索

二分搜索基于将重点放在数据的中间部分的逻辑。插值搜索更加复杂。它使用目标值来估计已排序数组中元素的位置。让我们通过一个例子来理解它。假设我们想在英语词典中搜索一个单词，比如*river*。我们将利用这些信息进行插值，并开始搜索以*r*开头的单词。更一般化的插值搜索可以编程如下：

```py

def IntPolsearch(list,x ):     
    idx0 = 0     
    idxn = (len(list) - 1)     
    found = False     
    while idx0 <= idxn and x >= list[idx0] and x <= list[idxn]: 
    # Find the mid point         
         mid = idx0 +int(((float(idxn - idx0)/( list[idxn] - list[idx0])) * ( x - list[idx0]))) 
 # Compare the value at mid point with search value 
         if list[mid] == x: 
            found = True             
            return found         
    if list[mid] < x:             
            idx0 = mid + 1     
return found
```

输出如下：

![](img/bc21fe23-43bb-4852-8e42-1458f022db6a.png)

注意，在使用`IntPolsearch`之前，数组首先需要使用排序算法进行排序。

# 插值搜索的性能

如果数据分布不均匀，插值搜索算法的性能将很差。该算法的最坏情况性能为*O(N)*，如果数据相对均匀，最佳性能为 O(log(log N))。

# 实际应用

在给定数据存储库中高效准确地搜索数据对许多现实生活应用至关重要。根据您选择的搜索算法，您可能需要首先对数据进行排序。选择正确的排序和搜索算法将取决于数据的类型和大小，以及您试图解决的问题的性质。

让我们尝试使用本章介绍的算法来解决某个国家移民局新申请人与历史记录匹配的问题。当有人申请签证进入该国时，系统会尝试将申请人与现有的历史记录进行匹配。如果至少找到一个匹配项，那么系统会进一步计算个人过去被批准或拒绝的次数。另一方面，如果没有找到匹配项，系统会将申请人分类为新申请人，并为其发放新的标识符。在历史数据中搜索、定位和识别个人的能力对系统至关重要。这些信息很重要，因为如果某人过去曾申请过并且已知申请被拒绝，那么这可能会对该个人当前的申请产生负面影响。同样，如果某人的申请过去已知被批准，那么这个批准可能会增加该个人当前申请获批准的机会。通常，历史数据库将有数百万行数据，我们需要一个精心设计的解决方案来将新申请人与历史数据库进行匹配。

假设数据库中的历史表如下所示：

| **个人 ID** | **申请 ID** | **名字** | **姓氏** | **出生日期** | **决定** | **决定日期** |
| --- | --- | --- | --- | --- | --- | --- |
| 45583 | 677862 | 约翰 | 多 | 2000-09-19 | 已批准 | 2018-08-07 |
| 54543 | 877653 | Xman | Xsir | 1970-03-10 | 被拒绝 | 2018-06-07 |
| 34332 | 344565 | 阿格罗 | 瓦卡 | 1973-02-15 | 被拒绝 | 2018-05-05 |
| 45583 | 677864 | 约翰 | 多 | 2000-09-19 | 已批准 | 2018-03-02 |
| 22331 | 344553 | 卡尔 | 索茨 | 1975-01-02 | 已批准 | 2018-04-15 |

在这个表中，第一列“个人 ID”与历史数据库中的每个唯一申请人相关联。如果历史数据库中有 3000 万个唯一申请人，那么将有 3000 万个唯一的个人 ID。每个个人 ID 标识历史数据库系统中的一个申请人。

第二列是“申请 ID”。每个申请 ID 标识系统中的一个唯一申请。一个人过去可能申请过多次。这意味着在历史数据库中，我们将有比个人 ID 更多的唯一申请 ID。如上表所示，约翰·多只有一个个人 ID，但有两个申请 ID。

上表仅显示了历史数据集的一部分样本。假设我们的历史数据集中有接近 100 万行数据，其中包含过去 10 年申请人的记录。新申请人以每分钟约 2 人的平均速度持续到达。对于每个申请人，我们需要执行以下操作：

+   为申请人发放新的申请 ID。

+   查看历史数据库中是否有与申请人匹配的记录。

+   如果找到匹配项，则使用历史数据库中找到的个人 ID。我们还需要确定在历史数据库中申请已被批准或拒绝的次数。

+   如果没有找到匹配项，那么我们需要为该个人发放新的个人 ID。

假设一个新的人员带着以下的证件到达：

+   “名字”： “约翰”

+   姓氏：`多`

+   `出生日期`：`2000-09-19`

现在，我们如何设计一个能够执行高效和具有成本效益的搜索的应用程序呢？

搜索数据库中新申请的一个策略可以设计如下：

+   按`出生日期`对历史数据库进行排序。

+   每次有新人到来时，都要为申请人发放新的申请 ID。

+   获取所有与该出生日期匹配的记录。这将是主要搜索。

+   在出现匹配项的记录中，使用名字和姓氏进行次要搜索。

+   如果找到匹配项，请使用`个人 ID`来引用申请人。计算批准和拒绝的次数。

+   如果找不到匹配项，请为申请人发放新的个人 ID。

让我们尝试选择正确的算法来对历史数据库进行排序。我们可以安全地排除冒泡排序，因为数据量很大。希尔排序将表现更好，但仅当我们有部分排序的列表时。因此，归并排序可能是对历史数据库进行排序的最佳选择。

当有新人到来时，我们需要在历史数据库中定位并搜索该人。由于数据已经排序，可以使用插值搜索或二分搜索。因为申请人可能根据`出生日期`均匀分布，所以可以安全地使用二分搜索。

最初，我们基于`出生日期`进行搜索，这将返回一组共享相同出生日期的申请人。现在，我们需要在共享相同出生日期的小子集中找到所需的人。由于我们已成功将数据减少到一个小子集，任何搜索算法，包括冒泡排序，都可以用于搜索申请人。请注意，我们在这里稍微简化了次要搜索问题。如果找到多个匹配项，我们还需要通过汇总搜索结果来计算批准和拒绝的总数。

在现实场景中，每个个体都需要在次要搜索中使用一些模糊搜索算法进行识别，因为名字可能拼写略有不同。搜索可能需要使用某种距离算法来实现模糊搜索，其中相似度高于定义的阈值的数据点被视为相同。

# 总结

在本章中，我们介绍了一组排序和搜索算法。我们还讨论了不同排序和搜索算法的优缺点。我们量化了这些算法的性能，并学会了何时使用每个算法。

在下一章中，我们将学习动态算法。我们还将研究设计算法的实际示例以及页面排名算法的细节。最后，我们将学习线性规划算法。