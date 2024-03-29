# 第八章：图和其他算法

在本章中，我们将讨论与图相关的概念。图的概念来自数学的一个分支，称为**图论**。图被用来解决许多计算问题。图是一种非线性数据结构。这种结构通过连接一组节点或顶点以及它们的边来表示数据。这与我们迄今为止所看到的数据结构非常不同，对图的操作（例如遍历）可能是非常规的。在本章中，我们将讨论与图相关的许多概念。此外，我们还将在本章后面讨论优先队列和堆。

到本章结束时，您应该能够做到以下几点：

+   了解图是什么

+   了解图的类型和它们的组成部分

+   了解如何表示图并遍历它

+   获得优先队列的基本概念

+   能够实现优先队列

+   能够确定列表中第 i 个最小的元素

# 技术要求

本章讨论的所有源代码都可以在以下链接的 GitHub 存储库中找到：[`github.com/PacktPublishing/Hands-On-Data-Structures-and-Algorithms-with-Python-Second-Edition/tree/master/Chapter08`](https://github.com/PacktPublishing/Hands-On-Data-Structures-and-Algorithms-with-Python-Second-Edition/tree/master/Chapter08)。

# 图

图是一组顶点和边，它们之间形成连接。在更正式的方法中，图**G**是一个顶点集*V*和边集**E**的有序对，以正式的数学符号表示为`G = (V, E)`。

这里给出了一个图的示例：

![](img/05db308a-34cf-4ecf-90df-2798d1d0153d.png)

让我们讨论一些图的重要定义：

+   **节点或顶点**：图中的一个点或节点称为一个顶点，通常在图中用一个点表示。在前面的图中，顶点或节点是**A**、**B**、**C**、**D**和**E**。

+   **边**：这是两个顶点之间的连接。连接**A**和**B**的线是前面图中边的一个例子。

+   **循环**：当一个节点的边与自身相连时，该边形成一个循环。

+   **顶点的度**：一个给定顶点上的边的总数被称为该顶点的度。例如，前面图中**B**顶点的度为`4`。

+   **邻接**：这指的是任意两个节点之间的连接；因此，如果两个顶点或节点之间有连接，则它们被称为相邻。例如，**C**节点与**A**节点相邻，因为它们之间有一条边。

+   **路径**：任意两个节点之间的顶点和边的序列表示从**A**节点到**B**节点的路径。例如，**CABE**表示从**C**节点到**E**节点的路径。

+   **叶节点**（也称为*挂节点*）：如果一个顶点或节点的度为 1，则称为叶节点或挂节点。

# 有向和无向图

图由节点之间的边表示。连接边可以被认为是有向的或无向的。如果图中的连接边是无向的，则图被称为无向图，如果图中的连接边是有向的，则它被称为有向图。无向图简单地将边表示为节点之间的线。除了它们相互连接之外，关于节点之间关系的其他信息都没有。例如，在下图中，我们展示了一个由四个节点**A**、**B**、**C**和**D**组成的无向图，它们之间通过边相连：

![](img/3b95d068-1a3e-4396-a06e-ccb8d3212bc8.png)

在有向图中，边提供了有关图中任意两个节点之间连接方向的信息。如果从节点**A**到**B**的边是有向的，那么边（**A**，**B**）就不等于边（**B**，**A**）。有向边用带箭头的线表示，箭头指向边连接两个节点的方向。例如，在下图中，我们展示了一个有向图，其中许多节点使用有向边连接：

![](img/2a0b4a49-1e7d-4191-9261-ec2f3631e0c8.png)

边的箭头确定了方向的流动。如前图所示，只能从**A**到**B**，而不能从**B**到**A**。在有向图中，每个节点（或顶点）都有一个入度和一个出度。让我们来看看这些是什么：

+   **入度**：进入图中一个顶点的边的总数称为该顶点的入度。例如，在前面的图中，**E**节点由于边**CE**进入，所以入度为`1`。

+   **出度**：从图中一个顶点出去的边的总数称为该顶点的出度。例如，在前面的图中，**E**节点的出度为`2`，因为它有两条边**EF**和**ED**出去。

+   **孤立顶点**：当一个节点或顶点的度为零时，称为孤立顶点。

+   **源顶点**：如果一个顶点的入度为零，则称为源顶点。例如，在前面的图中，**A**节点是源顶点。

+   **汇点**：如果一个顶点的出度为零，则称为汇点。例如，在前面的图中，**F**节点是汇点。

# 加权图

加权图是一个在图中的边上关联了数值权重的图。它可以是有向图，也可以是无向图。这个数值可以用来表示距离或成本，取决于图的目的。让我们来考虑一个例子。下图表示了从**A**节点到**D**节点的不同路径。你可以直接从**A**到**D**，也可以选择经过**B**和**C**，考虑到每条边的关联权重是到达下一个节点所需的时间（以分钟为单位）：

![](img/16b1ee37-e5b6-4d4e-951c-7dfd2bcf24ba.png)

在这个例子中，**AD**和**ABCD**代表两条不同的路径。路径就是在两个节点之间通过的一系列边。跟随这些路径，你会发现**AD**需要**40**分钟，而**ABCD**只需要**25**分钟。如果唯一关心的是时间，那么沿着**ABCD**路径旅行会更好，即使它可能是一条更长的路线。这里要记住的是边可以是有方向的，并且可能包含其他信息（例如所需时间、要行驶的距离等）。

我们可以以类似的方式实现图形，就像我们对其他数据结构（如链表）所做的那样。对于图形来说，将边看作对象和节点一样是有意义的。就像节点一样，边也可以包含额外的信息，这使得跟随特定路径成为必要。图中的边可以用不同节点之间的链接来表示；如果图中有一个有向边，我们可以用一个箭头从一个节点指向另一个节点来实现它，这在节点类中很容易用`next`或`previous`、`parent`或`child`来表示。

# 图的表示

在 Python 中实现图时，可以用两种主要形式来表示。一种是使用邻接表，另一种是使用邻接矩阵。让我们考虑一个例子，如下图所示，为图开发这两种表示类型：

![](img/56cf0157-d3c0-4cbc-96f5-106bf1595186.png)

# 邻接表

邻接列表存储所有节点，以及与它们在图中直接连接的其他节点。在图`G`中，两个节点`A`和`B`如果之间有直接连接，则称它们是相邻的。在 Python 中，使用`list`数据结构表示图。列表的索引可以用来表示图中的节点或顶点。

在每个索引处，将该顶点的相邻节点存储起来。例如，考虑以下对应于先前显示的示例图的邻接列表：

![](img/ef3c9ee0-ee1c-4d1b-bac9-7ac85ad81b87.png)

方框中的数字代表顶点。`0`索引代表图的`A`顶点，其相邻节点为`B`和`C`。`1`索引代表图的`B`顶点，其相邻节点为`E`、`C`和`A`。类似地，图的其他顶点`C`、`E`和`F`在索引`2`、`3`和`4`处表示，其相邻节点如前图所示。

使用`list`进行表示相当受限制，因为我们缺乏直接使用顶点标签的能力。因此，使用`dictionary`数据结构更适合表示图。要使用`dictionary`数据结构实现相同的先前图，我们可以使用以下语句：

```py
    graph = dict() 
    graph['A'] = ['B', 'C'] 
    graph['B'] = ['E','C', 'A'] 
    graph['C'] = ['A', 'B', 'E','F'] 
    graph['E'] = ['B', 'C'] 
    graph['F'] = ['C'] 
```

现在我们可以很容易地确定**A**顶点的相邻顶点是**B**和**C**。**F**顶点的唯一邻居是**C**。同样，**B**顶点的相邻顶点是**E**、**B**和**A**。

# 邻接矩阵

图可以使用邻接矩阵表示的另一种方法是使用邻接矩阵。矩阵是一个二维数组。这里的想法是用`1`或`0`表示单元格，具体取决于两个顶点是否由边连接。我们在下图中演示了一个示例图，以及其对应的邻接矩阵：

![](img/239d325d-3579-44fc-9574-51a29681914d.png)

可以使用给定的邻接列表来实现邻接矩阵。要实现邻接矩阵，让我们使用图的先前基于字典的实现。首先，我们必须获得邻接矩阵的关键元素。重要的是要注意，这些矩阵元素是图的顶点。我们可以通过对图的键进行排序来获得关键元素。此操作的代码片段如下：

```py
    matrix_elements = sorted(graph.keys()) 
    cols = rows = len(matrix_elements) 
```

接下来，使用图的键的长度来提供邻接矩阵的维度，这些维度存储在`cols`和`rows`中，`cols`和`rows`中的值相等。然后我们创建一个正确大小的空邻接矩阵，大小为`cols`乘以`rows`，并用零填充它。`edges_list`变量将存储图中形成边的元组。例如，A 和 B 节点之间的边将存储为`(A, B)`。初始化空邻接矩阵的代码片段如下：

```py
    adjacency_matrix = [[0 for x in range(rows)] for y in range(cols)] 
    edges_list = []
```

多维数组是使用嵌套的`for`循环填充的：

```py
    for key in matrix_elements: 
        for neighbor in graph[key]: 
            edges_list.append((key, neighbor)) 
```

顶点的邻居是通过`graph[key]`获得的。然后，结合`neighbor`使用`edges_list`存储创建的元组。

用于存储图的边的上述 Python 代码的输出如下：

```py
>>> [('A', 'B'), ('A', 'C'), ('B', 'E'), ('B', 'C'), ('B', 'A'), ('C', 'A'), 
 ('C', 'B'), ('C', 'E'), ('C', 'F'), ('E', 'B'), ('E', 'C'), 
 ('F', 'C')]
```

实现邻接矩阵的下一步是填充它，使用`1`表示图中存在边。这可以通过`adjacency_matrix[index_of_first_vertex][index_of_second_vertex] = 1`语句来完成。标记图的边存在的完整代码片段如下：

```py
    for edge in edges_list: 
        index_of_first_vertex = matrix_elements.index(edge[0]) 
        index_of_second_vertex = matrix_elements.index(edge[1]) 
        adjacency_matrix[index_of_first_vertex][index_of_second_vertex] = 1 
```

`matrix_elements`数组有它的`rows`和`cols`，从`A`到所有其他顶点，索引从`0`到`5`。`for`循环遍历我们的元组列表，并使用`index`方法获取要存储边的相应索引。

先前代码的输出是先前显示的示例图的邻接矩阵。生成的邻接矩阵如下所示：

```py
>>>
[0, 1, 1, 0, 0]
[1, 0, 0, 1, 0]
[1, 1, 0, 1, 1]
[0, 1, 1, 0, 0]
[0, 0, 1, 0, 0]
```

在第`1`行和第`1`列，`0`表示 A 和 A 之间没有边。同样，在第`2`列和第`3`行，有一个值为`1`，表示图中 C 和 B 顶点之间的边。

# 图遍历

图遍历意味着访问图的所有顶点，同时跟踪已经访问和尚未访问的节点或顶点。如果图遍历算法以最短可能的时间遍历图的所有节点，则该算法是高效的。图遍历的常见策略是沿着一条路径前进，直到遇到死胡同，然后向上遍历，直到遇到另一条路径。我们还可以迭代地从一个节点移动到另一个节点，以遍历整个图或部分图。图遍历算法在回答许多基本问题时非常重要——它们可以确定如何从图中的一个顶点到达另一个顶点，以及在图中从 A 到 B 顶点的哪条路径比其他路径更好。在接下来的部分中，我们将讨论两个重要的图遍历算法：**广度优先搜索**（**BFS**）和**深度优先搜索**（**DFS**）。

# 广度优先遍历

广度优先遍历算法以图的广度为基础工作。使用队列数据结构来存储要在图中访问的顶点的信息。我们从起始节点**A**开始。首先，我们访问该节点，然后查找它所有的相邻顶点。我们逐个访问这些相邻顶点，同时将它们的邻居添加到要访问的顶点列表中。我们一直遵循这个过程，直到访问了图的所有顶点，确保没有顶点被访问两次。

让我们通过以下图示例更好地理解图的广度优先遍历：

![](img/f707225d-efca-4c9e-be16-cd6a3bac6ff5.png)

在上图中，左侧有一个五个节点的图，右侧有一个队列数据结构，用于存储要访问的顶点。我们开始访问第一个节点**A**，然后将其所有相邻的顶点**B**、**C**和**E**添加到队列中。在这里，需要注意的是，添加相邻节点到队列有多种方式，因为有三个节点**B**、**C**和**E**，可以按照**BCE**、**CEB**、**CBE**、**BEC**或**ECB**的顺序添加到队列中，每种方式都会给出不同的树遍历结果。

图遍历的所有可能解决方案都是正确的，但在这个例子中，我们将按字母顺序添加节点。如图所示，访问了**A**节点：

![](img/27150f8e-3c54-4124-95e2-52a1a5b48413.png)

一旦我们访问了**A**顶点，接下来，我们访问它的第一个相邻顶点**B**，并添加那些尚未添加到队列或未访问的相邻顶点。在这种情况下，我们需要将**D**顶点添加到队列中：

![](img/3b782d49-aea9-4aa0-bccd-32be854c1741.png)

现在，在访问**B**顶点之后，我们访问队列中的下一个顶点**C**。然后，添加那些尚未添加到队列中的相邻顶点。在这种情况下，没有未记录的顶点，因此不需要进行任何操作：

![](img/cb98173e-c3f9-4254-ac75-cb3eefaa916e.png)

在访问**C**顶点之后，我们访问队列中的下一个顶点**E**：

![](img/a444bc08-d7ec-42e2-a6e7-32b1c06491c4.png)

同样，在访问**E**顶点之后，我们在最后一步访问**D**顶点：

![](img/3e1093a8-d4a6-4d88-9140-2f5d5cb36f85.png)

因此，用于遍历上述图的 BFS 算法按照**A-B-C-E-D**的顺序访问顶点。这是上述图的 BFS 遍历的一种可能解决方案，但根据我们如何将相邻节点添加到队列中，我们可以得到许多可能的解决方案。

要学习 Python 中此算法的实现，让我们考虑另一个无向图的示例。考虑以下图表作为图：

![](img/dbc67290-a5cd-4ffe-835a-3e11ea10b056.png)

图的邻接列表如下：

```py
    graph = dict() 
    graph['A'] = ['B', 'G', 'D'] 
    graph['B'] = ['A', 'F', 'E'] 
    graph['C'] = ['F', 'H'] 
    graph['D'] = ['F', 'A'] 
    graph['E'] = ['B', 'G'] 
    graph['F'] = ['B', 'D', 'C'] 
    graph['G'] = ['A', 'E'] 
    graph['H'] = ['C'] 
```

要使用广度优先算法遍历此图，我们将使用队列。该算法创建一个列表来存储已访问的顶点，遍历过程中。我们将从 A 节点开始遍历。

A 节点被排队并添加到已访问节点列表中。然后，我们使用`while`循环来遍历图。在`while`循环中，A 节点被出队。它的未访问的相邻节点 B、G 和 D 按字母顺序排序并排队。队列现在包含 B、D 和 G 节点。这些节点也被添加到已访问节点列表中。此时，我们开始`while`循环的另一个迭代，因为队列不为空，这也意味着我们并没有真正完成遍历。

B 节点被出队。在其相邻节点 A、F 和 E 中，节点 A 已经被访问。因此，我们只按字母顺序排队 E 和 F 节点。然后，E 和 F 节点被添加到已访问节点列表中。

此时，我们的队列包含以下节点：D、G、E 和 F。已访问节点列表包含 A、B、D、G、E 和 F。

D 节点被出队，但它的所有相邻节点都已被访问，所以我们只需出队。队列前面的下一个节点是 G。我们出队 G 节点，但我们也发现它的所有相邻节点都已被访问，因为它们在已访问节点列表中。因此，G 节点也被出队。我们也出队 E 节点，因为它的所有节点也都已被访问。队列中现在只剩下 F 节点。

F 节点被出队，我们意识到它的相邻节点 B、D 和 C 中，只有 C 尚未被访问。然后，我们将 C 节点入队并添加到已访问节点列表中。然后，C 节点被出队。C 有 F 和 H 两个相邻节点，但 F 已经被访问，只剩下 H 节点。H 节点被入队并添加到已访问节点列表中。

最后一次`while`循环迭代将导致 H 节点被出队。它的唯一相邻节点 C 已经被访问。一旦队列完全为空，循环就会中断。

在图中遍历的输出是 A、B、D、G、E、F、C 和 H。

BFS 的代码如下：

```py
    from collections import deque 

    def breadth_first_search(graph, root): 
        visited_vertices = list() 
        graph_queue = deque([root]) 
        visited_vertices.append(root) 
        node = root 

        while len(graph_queue) > 0: 
            node = graph_queue.popleft() 
            adj_nodes = graph[node] 

            remaining_elements = 
                set(adj_nodes).difference(set(visited_vertices)) 
            if len(remaining_elements) > 0: 
                for elem in sorted(remaining_elements): 
                    visited_vertices.append(elem) 
                    graph_queue.append(elem) 

        return visited_vertices 
```

当我们想要找出一组节点是否在已访问节点列表中时，我们使用`remaining_elements = set(adj_nodes).difference(set(visited_vertices))`语句。这使用`set`对象的`difference`方法来找到在`adj_nodes`中但不在`visited_vertices`中的节点。

在最坏的情况下，每个顶点或节点和边都将被遍历，因此 BFS 算法的时间复杂度为`O(|V| + |E|)`，其中`|V|`是顶点或节点的数量，而`|E|`是图中的边的数量。

# 深度优先搜索

正如其名称所示，DFS 算法在遍历广度之前，会先遍历图中任何特定路径的深度。因此，首先访问子节点，然后是兄弟节点。使用栈数据结构来实现 DFS 算法。

我们从访问 A 节点开始，然后查看 A 顶点的邻居，然后是邻居的邻居，依此类推。让我们在 DFS 的上下文中考虑以下图：

![](img/ed6faf7a-b221-4000-bd58-91e6cf00c945.png)

访问完 A 顶点后，我们访问其邻居之一，B，如下所示：

![](img/ec27341d-6a80-4b5d-a952-8fac290a5ae0.png)

访问完 B 顶点后，我们查看 A 的另一个邻居 S，因为没有与 B 相连的顶点可以访问。接下来，我们查看 S 顶点的邻居，即 C 和 G 顶点。我们访问 C 如下：

![](img/fe9f9af7-5783-4f10-bf19-af222854d129.png)

访问**C**节点后，我们访问其相邻的**D**和**E**节点：

![](img/6c2139c7-20f9-4d2f-9027-4e972e122b5c.png)

类似地，访问**E**顶点后，我们访问**H**和**F**顶点，如下图所示：

![](img/b063f8a5-beec-41da-bd2b-73bf9bec1cc9.png)

最后，我们访问**F**节点：

![](img/bded1b28-229f-4a15-a99b-50dc72925357.png)

DFS 遍历的输出是**A-B-S-C-D-E-H-G-F**。

为了实现 DFS，我们从给定图的邻接表开始。以下是先前图的邻接表：

```py
graph = dict() 
    graph['A'] = ['B', 'S'] 
    graph['B'] = ['A'] 
    graph['S'] = ['A','G','C'] 
    graph['D'] = ['C'] 
    graph['G'] = ['S','F','H'] 
    graph['H'] = ['G','E'] 
    graph['E'] = ['C','H'] 
    graph['F'] = ['C','G'] 
    graph['C'] = ['D','S','E','F'] 
```

DFS 算法的实现始于创建一个列表来存储已访问的节点。`graph_stack`栈变量用于辅助遍历过程。我们使用普通的 Python 列表作为栈。起始节点称为`root`，并与图的邻接矩阵`graph`一起传递。`root`被推入栈中。`node = root`保存栈中的第一个节点：

```py
    def depth_first_search(graph, root): 
        visited_vertices = list() 
        graph_stack = list() 

        graph_stack.append(root) 
        node = root 
```

只要栈不为空，`while`循环的主体将被执行。如果`node`不在已访问节点列表中，我们将其添加。通过`adj_nodes = graph[node]`收集`node`的所有相邻节点。如果所有相邻节点都已经被访问，我们将从栈中弹出该节点，并将`node`设置为`graph_stack[-1]`。`graph_stack[-1]`是栈顶的节点。`continue`语句跳回到`while`循环的测试条件的开始。

```py
        while len(graph_stack) > 0: 
            if node not in visited_vertices: 
                visited_vertices.append(node) 
            adj_nodes = graph[node] 
            if set(adj_nodes).issubset(set(visited_vertices)): 
                graph_stack.pop() 
                if len(graph_stack) > 0: 
                    node = graph_stack[-1] 
                continue 
                else: 
                    remaining_elements =                                
                    set(adj_nodes).difference(set(visited_vertices)) 

            first_adj_node = sorted(remaining_elements)[0] 
            graph_stack.append(first_adj_node) 
            node = first_adj_node 
        return visited_vertices 
```

另一方面，如果并非所有相邻节点都已经被访问，则通过使用`remaining_elements = set(adj_nodes).difference(set(visited_vertices))`语句找到`adj_nodes`和`visited_vertices`之间的差异来获得尚未访问的节点。

`sorted(remaining_elements)`中的第一个项目被分配给`first_adj_node`，并推入栈中。然后我们将栈的顶部指向这个节点。

当`while`循环结束时，我们将返回`visited_vertices`。

现在我们将通过将其与先前的示例相关联来解释源代码的工作。**A**节点被选择为我们的起始节点。**A**被推入栈中，并添加到`visisted_vertices`列表中。这样做时，我们将其标记为已访问。`graph_stack`栈使用简单的 Python 列表实现。我们的栈现在只有 A 作为其唯一元素。我们检查**A**节点的相邻节点**B**和**S**。为了测试**A**的所有相邻节点是否都已经被访问，我们使用`if`语句：

```py
    if set(adj_nodes).issubset(set(visited_vertices)): 
        graph_stack.pop() 
        if len(graph_stack) > 0: 
            node = graph_stack[-1] 
        continue 
```

如果所有节点都已经被访问，我们将弹出栈顶。如果`graph_stack`栈不为空，我们将栈顶的节点赋给`node`，并开始另一个`while`循环主体的执行。如果`set(adj_nodes).issubset(set(visited_vertices))`语句评估为`True`，则表示`adj_nodes`中的所有节点都是`visited_vertices`的子集。如果`if`语句失败，这意味着还有一些节点需要被访问。我们通过`remaining_elements = set(adj_nodes).difference(set(visited_vertices))`获得这些节点的列表。

参考图表，**B**和**S**节点将被存储在`remaining_elements`中。我们将按照字母顺序访问列表，如下所示：

```py
    first_adj_node = sorted(remaining_elements)[0] 
    graph_stack.append(first_adj_node) 
    node = first_adj_node
```

我们对`remaining_elements`进行排序，并将第一个节点返回给`first_adj_node`。这将返回**B**。我们通过将其附加到`graph_stack`来将**B**节点推入栈。我们通过将其分配给`node`来准备访问**B**节点。

在`while`循环的下一次迭代中，我们将**B**节点添加到`visited nodes`列表中。我们发现**B**的唯一相邻节点**A**已经被访问。因为**B**的所有相邻节点都已经被访问，我们将其从栈中弹出，只留下**A**作为栈中的唯一元素。我们返回到**A**，检查它的所有相邻节点是否都已经被访问。**A**节点现在只有**S**节点是未访问的。我们将**S**推入栈中，然后重新开始整个过程。

遍历的输出是`A-B-S-C-D-E-H-G-F`。

深度优先搜索在解决迷宫问题、查找连通分量和查找图的桥梁等方面有应用。

# 其他有用的图方法

我们经常需要使用图来找到两个节点之间的路径。有时，需要找到节点之间的所有路径，在某些情况下，我们可能需要找到节点之间的最短路径。例如，在路由应用中，我们通常使用各种算法来确定从源节点到目标节点的最短路径。对于无权图，我们只需确定它们之间边数最少的路径。如果给定了加权图，我们必须计算通过一组边的总权重。

因此，在不同的情况下，我们可能需要使用不同的算法来找到最长或最短的路径。

# 优先队列和堆

优先队列是一种类似于队列和栈数据结构的数据结构，它存储与其关联的优先级的数据。在优先队列中，具有最高优先级的项目首先被服务。优先队列通常使用堆来实现，因为对于这个目的来说它非常高效；然而，它也可以使用其他数据结构来实现。它是一个修改过的队列，以最高优先级的顺序返回项目，而队列则以添加项目的顺序返回项目。优先队列在许多应用中使用，例如 CPU 调度。

让我们举个例子来演示优先队列比普通队列的重要性。假设在商店里，顾客排队等候服务只能在队列的前端进行。每个顾客在得到服务之前都会在队列中花费一些时间。如果四个顾客在队列中花费的时间分别是 4、30、2 和 1，那么队列中的平均等待时间就变成了`(4 + 34 + 36 + 37)/4`，即`27.75`。然而，如果我们将优先条件与队列中存储的数据关联起来，那么我们可以给予花费时间最少的顾客更高的优先级。在这种情况下，顾客将按照花费时间的顺序进行服务，即按照 1、2、4、30 的顺序。因此，平均等待时间将变为`(1 + 3 + 7 + 37)/4`，现在等于`12`——一个更好的平均等待时间。显然，按照花费时间最少的顾客进行服务是有益的。按照优先级或其他标准选择下一个项目的方法是创建优先队列的基础。优先队列通常使用堆来实现。

堆是满足堆属性的数据结构。堆属性规定父节点和其子节点之间必须存在一定的关系。这个属性必须在整个堆中都适用。

在最小堆中，父节点和子节点之间的关系是父节点的值必须始终小于或等于其子节点的值。由于这个关系，堆中最小的元素必须是根节点。

另一方面，在最大堆中，父节点大于或等于其子节点。由此可知，最大值组成了根节点。

堆是二叉树，尽管我们将使用二叉树，但实际上我们将使用列表来表示它。堆存储完全二叉树。完全二叉树是指在开始填充下一行之前，每一行必须完全填满，如下图所示：

![](img/1cc995f2-9454-47c1-9f92-c16a8b884182.png)

为了使索引的数学运算更容易，我们将把列表中的第一项（索引 0）留空。之后，我们将树节点按照从上到下、从左到右的顺序放入列表中，如下图所示：

![](img/8ddea893-9e46-4226-9160-4fcdd911daef.png)

如果你仔细观察，你会注意到你可以很容易地检索到任何节点的子节点在`n`索引。左子节点位于`2n`，右子节点位于`2n + 1`。这总是成立的。例如，C 节点将位于`3`索引，因为 C 是 A 节点的右子节点，其索引为`1`，所以它变成了`2n+1 = 2*1 + 1 = 3`。

让我们讨论使用 Python 实现最小堆，因为一旦我们理解了最小堆，实现最大堆将更加直接。我们从堆类开始，如下所示：

```py
     class Heap: 
        def __init__(self): 
            self.heap = [0] 
            self.size = 0 
```

我们用零初始化堆列表，以表示虚拟的第一个元素（记住我们只是为了简化数学而这样做）。我们还创建一个变量来保存堆的大小。这并不是必要的，因为我们可以检查列表的大小，但我们总是需要记住将其减少一。因此，我们选择保持一个单独的变量。

# 插入操作

向最小堆插入项目需要分两步进行。首先，我们将新元素添加到列表的末尾（我们理解为树的底部），并将堆的大小增加一。其次，在每次插入操作之后，我们需要将新元素在堆树中安排起来，以使所有节点以满足堆属性的方式组织。这是为了提醒我们，最小堆中最小的元素需要是根元素。

我们首先创建一个名为`arrange`的辅助方法，它负责在插入后安排所有节点。让我们考虑在最小堆中添加元素的示例。我们在下图中提供了一个示例堆，并希望在其中插入值为`2`的元素：

![](img/8bb8b3e5-192f-4127-92f1-d97d8a56fcf2.png)

新元素已经占据了第三行或级别的最后一个插槽。它的索引值为 7。现在我们将该值与其父节点进行比较。父节点的索引为`7/2 = 3`（整数除法）。该元素的值为 6，所以我们交换 2，如下所示：

![](img/1086030c-280d-4d34-bfa6-90f390182773.png)

我们的新元素已经被交换并移动到了**3**索引。我们还没有达到堆的顶部（*3/2 > 0*），所以我们继续。我们元素的新父节点位于索引*3/2=1*。所以我们再次比较，如果需要，再次交换：

![](img/00545df1-f14c-4d8b-8d93-5120d5ab74d9.png)

最终交换后，我们得到了一个堆，如下所示。请注意，它符合堆的定义：

![](img/53fadd7f-0c58-43a7-bdc3-f8ef93057166.png)

在我们向最小堆插入元素后，这是`arrange()`方法的实现：

```py
    def arrange(self, k): 
```

我们将循环直到达到根节点，这样我们就可以将元素安排到需要到达的最高位置。由于我们使用整数除法，一旦小于`2`，循环就会中断：

```py
        while k // 2 > 0: 
```

比较父节点和子节点。如果父节点大于子节点，则交换两个值：

```py
        if self.heap[k] < self.heap[k//2]: 
            self.heap[k], self.heap[k//2] = self.heap[k//2], 
            self.heap[k] 
```

最后，不要忘记向上移动树：

```py
        k //= 2 
```

这个方法确保元素被正确排序。

现在，我们只需要从我们的`insert`方法中调用这个方法：

```py
    def insert(self, item): 
        self.heap.append(item) 
        self.size += 1 
        self.arrange(self.size) 
```

请注意，`insert`中的最后一行调用`arrange()`方法来根据需要重新组织堆。

# 弹出操作

`pop`操作从堆中移除一个元素。从最小堆中移除元素的原因是，首先找出要删除的项目的索引，然后组织堆以满足堆属性。然而，更常见的是从最小堆中弹出最小值，并根据最小堆的属性，我们可以通过其根值获得最小值。因此，为了获取并从最小堆中删除最小值，我们移除根节点并重新组织堆的所有节点。我们还将堆的大小减少一。

然而，一旦根节点被弹出，我们就需要一个新的根节点。为此，我们只需取出列表中的最后一个项目，并将其作为新的根。也就是说，我们将它移动到列表的开头。然而，所选的最后一个节点可能不是堆中最小的元素，因此我们需要重新组织堆的节点。为了根据最小堆属性对所有节点进行结构化，我们遵循了与插入元素时使用的`arrange()`方法相反的策略。我们将最后一个节点作为新的根，然后让它根据需要向下移动（或下沉）。

让我们通过一个例子来帮助理解这个概念。首先，我们弹出`root`元素：

![](img/ea6a7355-42dc-44ce-b906-c8a74c6c2856.png)

如果我们选择移动根的一个子节点，我们将不得不弄清楚如何重新平衡整个树结构，这将更加复杂。因此，我们做一些非常有趣的事情。我们将列表中的最后一个元素移动到`root`元素的位置上；例如，在下面的堆示例中，最后一个元素**6**被放置在根位置上：

![](img/7e7ff95f-1917-4ab7-9a1f-4f2a2d53dbe6.png)

现在，这个元素显然不是堆中最小的。因此，我们需要将它下沉到堆中。首先，我们需要确定是向左还是向右子节点下沉。我们比较两个子节点，以便最小的元素将作为根下沉。在这个例子中，我们比较了根的两个子节点，即**5**和**3**：

![](img/1d633f27-44e6-47c9-acfb-7ccb19717cb0.png)

右子节点显然更小：它的索引是**3**，表示*根索引* 2 + 1*。我们继续将我们的新根节点与该索引处的值进行比较，如下所示：

![](img/bc3c7d19-1995-4618-9569-e595be98aacc.png)

现在我们的节点已经下降到索引**3**。我们需要将其与较小的子节点进行比较。然而，现在我们只有一个子节点，所以我们不需要担心与哪个子节点进行比较（对于最小堆，它总是较小的子节点）：

![](img/2dc6e0f7-7801-4991-b6f6-dd66f46dc674.png)

这里不需要交换。由于没有更多的行，我们不需要做其他事情。请注意，在完成`sink()`操作后，堆符合我们对堆的定义。

现在我们可以开始实现这个了。但在我们实现`sink()`方法之前，我们需要注意如何确定要与父节点进行比较的子节点。让我们将选择放在自己的小方法中，这样代码看起来会简单一些：

```py
    def minindex(self, k): 
```

我们可能会超出列表的末尾——如果是这样，我们返回左子节点的索引：

```py
        if k * 2 + 1 > self.size: 
            return k * 2 
```

否则，我们简单地返回两个子节点中较小的那个的索引：

```py
        elif self.heap[k*2] < self.heap[k*2+1]: 
            return k * 2 
        else: 
            return k * 2 + 1
```

现在我们可以创建`sink`函数。就像以前一样，我们将循环，以便我们可以将我们的元素下沉到需要的位置：

```py
    def sink(self, k): 
          while k*2 <- self.size: 
```

接下来，我们需要知道是要与左子节点还是右子节点进行比较。这就是我们使用`minindex()`函数的地方：

```py
            mi = self.minindex(k)
```

就像我们在插入操作中的`arrange()`方法中所做的那样，我们比较父节点和子节点，看看我们是否需要进行交换：

```py
            if self.heap[k] > self.heap[mi]: 
                self.heap[k], self.heap[mi] = self.heap[mi], 
                self.heap[k] 
```

我们需要确保向下移动树，这样我们就不会陷入循环中：

```py
            k = mi 
```

现在唯一剩下的就是实现主要的`pop()`方法本身。这非常简单，因为`sink()`方法执行了大部分工作：

```py
    def pop(self): 
        item = self.heap[1] 
        self.heap[1] = self.heap[self.size] 
        self.size -= 1 
        self.heap.pop() 
        self.sink(1) 
        return item
```

# 测试堆

现在，让我们测试堆的实现，并通过一个例子来讨论。我们首先通过逐个插入 10 个元素来构建一个堆。让元素为`{4, 8, 7, 2, 9, 10, 5, 1, 3, 6}`。首先，我们手动创建一个包含这些元素的堆，然后我们将实现它并验证我们是否做得正确：

![](img/43b714fe-a272-4abf-8977-2404128f041b.png)

在上图中，我们展示了一个逐步插入元素到堆中的过程。在这里，我们继续按照所示添加元素：

![](img/f378b0d1-6ba0-4ad1-8c70-2fcd91194238.png)

最后，我们向堆中插入一个元素**6**：

![](img/73e8a792-fa08-4fc2-97e3-26ae969442a8.png)

现在，让我们开始创建堆并插入数据，如下所示的代码：

```py
    h = Heap() 
    for i in (4, 8, 7, 2, 9, 10, 5, 1, 3, 6): 
        h.insert(i) 
```

我们可以打印堆列表，只是为了检查元素的排序方式。如果你将其重新绘制为树结构，你会注意到它满足堆的所需属性，类似于我们手动创建的那样：

```py
    print(h.heap) 
```

现在我们将一个一个地弹出项目。注意项目是如何按照从低到高的顺序排序出来的。同时，注意每次`pop`后堆列表是如何改变的。`sink()`方法将重新组织堆中的所有项目：

```py
    for i in range(10): 
        n = h.pop() 
        print(n) 
        print(h.heap) 
```

在前面的部分中，我们讨论了使用最小堆的概念，因此通过简单地颠倒逻辑，实现最大堆应该是一个简单的任务。

我们将在第十章中再次使用我们在这里讨论的最小堆，*排序*，关于排序算法，并将重写列表中元素的排序代码。这些算法被称为堆排序算法。

# 选择算法

选择算法属于一类算法，旨在解决在列表中找到第 i 个最小元素的问题。当列表按升序排序时，列表中的第一个元素将是列表中最小的项。列表中的第二个元素将是列表中第二小的元素。列表中的最后一个元素将是最小的（或最大的）元素。

在创建堆数据结构时，我们已经了解到调用`pop`方法将返回最小堆中的最小元素。从最小堆中弹出的第一个元素是列表中的最小元素。同样，从最小堆中弹出的第七个元素将是列表中第七小的元素。因此，在列表中找到第 i 个最小元素将需要我们弹出堆 i 次。这是在列表中找到第 i 个最小元素的一种非常简单和高效的方法。

然而，在第十一章，*选择算法*中，我们将学习更多寻找列表中第 i 个最小元素的方法。

选择算法在过滤嘈杂数据、查找列表中的中位数、最小和最大元素等方面有应用，并且甚至可以应用在计算机国际象棋程序中。

# 总结

本章讨论了图和堆。图的主题对于许多现实世界的应用非常重要和有用。我们已经看过了用列表和字典表示图的不同方法。为了遍历图，我们使用了两种方法：BFS 和 DFS。

然后我们转向了堆和优先队列，以了解它们的实现。本章以使用堆的概念来查找列表中第 i 个最小元素的讨论结束。

下一章将引领我们进入搜索领域，以及我们可以有效搜索列表中项目的各种方法。
