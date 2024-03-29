# 第四章：比特币数据分析

在本章中，我们将使用 Python 探索比特币价格数据的操作和可视化。我们还将探索比特币交易图表，以及使用 Python 收集和分析*比特币骰子*游戏数据。

# 操作和可视化比特币价格数据

在本节中，我们将介绍以下主题：

+   为数据分析做好准备

+   获取、读取和清理比特币价格数据

+   探索、操作和可视化清理后的数据

我们首先需要安装几个 Python 库，其中包括安装`pandas`模块用于读取数据，以及进行一些探索性分析。我们还将安装`matplotlib`用于创建图表和图表，以及 Jupyter 笔记本，因为它们是进行数据分析的最佳工具。

# 为数据分析做好准备

要安装 Python 模块，请打开命令行程序。在命令行中，要安装`pandas`，请执行以下命令：

```py
pip install pandas
```

类似地，要安装`matplotlib`，请执行以下命令：

```py
pip install matplotlib
```

要安装 Jupyter，请执行以下命令：

```py
pip install jupyter
```

完成安装所需的模块后，通过执行`jupyter notebook`命令启动 Jupyter Notebook。这将在新的浏览器窗口或选项卡中打开，显示已经存在于我们执行`jupyter notebook`命令的文件夹中的文件列表。以下屏幕截图显示了`jupyter notebook`命令：

![](img/af8667a9-bfa8-4170-8ca7-d60ed89c1186.png)

接下来，选择创建一个新的 Python 3 笔记本，如下屏幕截图所示：

![](img/b6e8b34b-841f-4a3b-a49f-292b11303034.png)

# 获取、读取和清理比特币价格数据

我们将首先导入必要的模块。

导入`pandas`以使您能够读取数据并开始探索。以下屏幕截图显示了`import pandas`命令：

![](img/e45e50b1-57f5-496c-ad52-ccce329b2305.png)

还要导入`matplotlib`以从数据中绘制图表。

我们需要为`pandas`和`matplotlib`设置一些选项。以下屏幕截图显示了导入`matplotlib`的命令：

![](img/6f0cf7fa-f6ab-441a-879f-fcf6f2e8d83b.png)

我们将设置的第一个选项称为`options.mode.chained_assignment = None`。

前面的选项是确保清理操作是在 pandas DataFrame 对象上执行的；我们希望清理操作发生在原始 DataFrame 对象上，而不是在副本上。

以下屏幕截图显示了`options.mode.chained_assignment = None`选项：

![](img/ecd35dc5-359e-407b-a34e-3d2007163a89.png)

还要设置`matplotlib`以可视化和显示以下屏幕截图中显示的所有图表：

![](img/d45508cb-0de5-409f-b4e5-bd849c25ff63.png)

我们的价格数据来自`coindesk.com`，如下屏幕截图所示，它可以免费下载：

![](img/0f77ee94-acf1-4414-a787-2e78cb3a83a0.png)

以 CSV 格式下载数据并使用`pandas`读取这些数据。这是一个 CSV 文件，所以我们将使用`pandas`的`read_CSV`方法，如下屏幕截图所示：

![](img/8d52200b-c994-4867-9998-2b3e354c1aaa.png)

# DataFrame

pandas 数据对象中的数据称为 DataFrame。DataFrame 是以表格形式的数据格式。现在，打印一些记录以查看其外观。要打印出来，我们可以在价格 DataFrame 上调用一个名为`head()`的方法。

这样做后，我们会得到两列——`日期`和`收盘价`——表示当天比特币的美元价格。我们还有一个默认索引，从 0 开始的行，这是由`pandas`在读取数据时默认插入的。以下屏幕截图显示了两列，`日期`和`收盘价`：

![](img/4f18a045-01be-4820-8c1f-46cf4ce542ac.png)

要获取有关此数据的顶级信息，请在其上调用`info()`方法。调用此方法后，我们得到 2,592 条记录。有两列：`Date`和`Close Price`。`Date`有 2,592 条非空记录，类型为对象，这意味着`Date`字段已被读取为文本。我们稍后需要将其更改为正确的日期时间格式。我们有收盘价作为数值浮点类型。它有 2,590 条非空记录，比`Date`字段少两条记录。

以下截图显示了`info()`方法的详细信息：

![](img/fb1959b0-af8a-4090-a229-9f12a130dbb1.png)

为了打印底部的记录，调用`tail()`方法。该方法显示最后两条记录不应该存在，因为它们不是日期或价格。在进行进一步分析之前，我们需要删除这些记录。

我们可以看到收盘价有 NaN 值，这意味着它有缺失值。我们可以利用这一因素从 DataFrame 中删除这两条记录。我们在价格上调用`drop any`方法，这将删除具有一个或多个列为空或缺失值的记录。

请记住，我们只是从 DataFrame 价格中删除它，而不是从我们读取数据的 CSV 文件中删除它。

以下截图中的代码显示了`tail()`方法的实现：

![](img/46f61d4e-87b7-426f-ab76-c2b1527be085.png)

此外，再次查看底部行，看看我们想要删除的记录是否已经被删除。我们可以在以下截图中看到，实际上已经被删除了：

![](img/1440387a-1b4d-4fd7-a077-f94c20de33dd.png)

# 数据清理

我们需要做的另一个数据清理任务是将`Date`列从对象或文本格式转换为日期时间格式。我们使用 pandas 的`to_datetime`方法来实现这一点。

在这里，我们要求`to_datetime`方法将`Date`字段或`price` DataFrame 转换，并且我们还提供了格式。然后我们将`Date`字段重新分配给 DataFrame，如下截图所示：

![](img/066e8991-2488-46a0-bfb6-b3716579c3fa.png)

这就是我们将链式赋值设置为`null`的原因，因为我们希望在原始 DataFrame 上进行更改。

再次调用`info()`方法，查看数据清理是否产生影响。我们可以看到`Date`字段现在是日期时间格式，正如我们想要的那样，并且数据中没有非空记录，如下截图所示：

![](img/81f0b204-02f4-492c-9c47-6bf9c10a31e7.png)

# 将索引设置为日期列

我们还需要将索引设置为`Date`列，并将`Date`列删除为单独的列。这将帮助我们对日期数据运行一些有趣的查询。

以下截图中的代码显示了如何将索引设置为`Date`列：

![](img/ed0c269e-745e-4e16-b9ba-e6a77a3390c6.png)

接下来，删除`Date`列作为单独的列，因为它已经设置为索引，如下截图所示：

![](img/5072bbcf-ab02-41b3-8784-8e1b2b5fc755.png)

现在，`Date`列可以看作是索引，而不再是一个单独的列，如下截图所示：

![](img/be7067b2-492d-424d-9257-18d891b7e95e.png)

# 探索、操作和可视化清理后的数据

数据清理完成后，开始进行数据探索任务。我们可以使用 pandas 日期时间功能运行一些有趣的查询。

例如，如果我们想要获取特定年份的所有记录，将该年份传递给 DataFrame 内的方括号。以下截图显示了 2010 年的价格数据：

![](img/33787d3c-0a7e-48e0-87d8-ec34a2356d11.png)

我们还可以指定我们是否要从特定日期获取数据。

以下截图显示了 2017 年 8 月 1 日的比特币价格：

![](img/36bfa72b-8716-43a8-964f-d5c6de623a4b.png)

我们还可以指定我们是否要从特定日期跨越一定日期的特定期间获取数据。

以下屏幕截图显示了 2017 年 8 月 1 日以后的数据：

![](img/70c5c9f1-469a-4487-b489-a104ac298528.png)

还可以使用 pandas 方法检索统计信息。例如，要从数据集中获取最低价格，我们可以使用`min()`方法，如下图所示：

![](img/20c26302-2140-4d74-9b39-841a5e472856.png)

要获取最高价格，使用`max()`方法，如下图所示：

![](img/cdc524fa-fc2f-44cd-96ca-58fda1a7d559.png)

可以使用`describe()`方法一次性获得大量统计信息，如下图所示：

![](img/5a11b1ce-e18e-42e4-afd7-46dca4687e63.png)

# 数据可视化

使用`pandas`和`matplotlib`从数据开始创建图表非常容易。要绘制整个数据，我们将在`price` `DataFrame`上调用`plot`方法，然后将得到一个图表，其中*x*轴是日期，*y*轴是价格数据。

以下屏幕截图描述了绘图，其中*x*轴是日期，*y*轴是价格数据：

![](img/729227c0-4ca6-4570-9c46-f9a39bf65db9.png)

我们还可以放大到特定的时间段。例如，为了仅绘制 2017 年的数据，首先选择来自 2017 年的数据，然后在数据子集上调用`plot()`方法。

在下图中，我们有 2017 年以后的价格数据的图表：

![](img/8cb9d715-b57f-4087-8c20-b9c51b08b205.png)

在接下来的部分，我们将学习如何使用比特币交易图。

# 探索比特币交易图

在本节中，我们将学习如何获取区块链数据，并提供逐步信息，以便探索、清理、分析和可视化这些数据。

# 比特币和区块链图

`Blockchain.info`是查看最新比特币统计数据和图表的最佳地方之一。有关比特币和区块链的不同类型的图表和图形可供分析。我们还可以以各种格式（CSV、JSON 等）下载数据。在上一节中，我们已经以 CSV 格式下载了部分数据，现在我们将在 Jupyter Notebook 中探索这些数据。

我们首先导入所需的模块。我们需要`pandas`来读取、探索和清理数据，还需要`matplotlib`来创建图表。

查看显示流通中比特币总数的数据。读取包含此数据的 CSV 文件，并创建一个`pandas` `DataFrame`。

以下屏幕截图显示了流通中比特币的总数量的数据：

![](img/0743f10c-867e-479c-9d69-55fcc8a6d1b6.png)

# 探索、清理和分析数据

为了探索这些数据，我们使用`head()`方法查看顶部的记录，并在 DataFrame 上调用`info()`方法以获取更多信息，例如有多少记录、有多少空记录或缺失记录，或各列的数据类型是什么。

我们可以看到`Date`列显示为对象。我们将其更改为日期时间以可视化这些数据。为此，我们使用`to_datetime`方法，并将转换后的值分配回同一列——DataFrame。

以下屏幕截图描述了比特币的日期格式：

![](img/2032a12f-45cd-4a9b-9728-693e60776570.png)

将 DataFrame 的索引设置为`Date`列，并删除`Date`列作为单独的列。执行此步骤以利用 pandas 的时间序列功能。

现在，通过在 DataFrame 上调用`info`和`head`来再次检查是否发生了更改。

以下屏幕截图显示了特定日期范围内的比特币：

![](img/9ab5ba6f-7113-403e-82fc-b52528bd4176.png)

现在我们准备从这些数据创建图表。在 DataFrame 上调用`plot()`方法，然后调用`show()`方法显示图表。

它显示了已经在我们有记录的时间段内挖出的比特币总数。

以下截图描述了前述数据的图表：

![](img/458d00ef-995d-458c-a2b0-0d5b0399e572.png)

# 可视化数据

让我们看另一个例子。在这里，我们正在查看我们读入数据的区块数据的交易：

![](img/b1ab2dbc-0c89-4cf8-bc5b-72b6d4157d26.png)

最初，我们使用`head`和`info`方法对这些数据进行可视化探索，如下截图所示：

![](img/47dc98ac-758b-497b-a6f1-e7152bb9324f.png)

接下来，我们清理、转换和重塑数据，如下截图所示：

![](img/22d53244-042e-4a08-aea3-d5dfe393b026.png)

最后，我们将在区块数据中可视化我们的交易，如下截图所示：

![](img/9e50e1ad-4a79-46aa-9442-ea6a4d5c2f89.png)

同样，还有另一个例子，我们应该看一下关于显示挖矿难度的数据。挖矿数据的步骤如下：

1.  读取数据，如下截图所示：

![](img/d1aa04c7-3ce1-4efc-9420-5a3626c1d1a3.png)

1.  探索数据，如下截图所示：

![](img/bf210410-6926-4245-b20b-74aaee8fabba.png)

1.  清理数据，如下截图所示：

![](img/b8a60285-a8af-44be-aa1d-f985330c813c.png)

1.  最后，可视化数据，如下截图所示：

![](img/039aa5f6-de27-4770-be78-7010a56cb92e.png)

我们可以看到多年来挖矿难度逐渐增加，并呈上升趋势。这些只是交易图表的几个例子。比特币和区块链生态系统中还有很多其他可供您探索的数据。

在下一个模块中，我们将学习如何收集和分析*比特币骰子*游戏数据。

# 收集和分析比特币骰子游戏数据

在本节中，我们将查看来自骰子游戏门户的数据，从 API 中读取数据，并使用`pandas`将其转换为表格格式。我们还将导出数据并找到需要清理的内容。我们将清理、操作和重塑数据，使其准备好进行分析，最后，我们将从干净的数据中绘制一个简单的图表。

# 从游戏 Web API 获取数据

用户可以从 MegaDice.com 上的 API 链接探索骰子游戏数据，该数据可从网站的 API 链接[`www.megadice.com`](https://www.megadice.com)获取。

我们将使用`pandas read_JSON`方法从 MegaDice API 链接中读取个人赢家历史数据。我们从这些数据创建了一个名为`leaders`的`pandas DataFrame`，并调用`head()`方法来查看这些数据的样子。

以下截图中的代码显示了`pandas` `DataFrame` `leaders`的创建：

![](img/a44027e0-92ea-473d-99a7-55f28add40e0.png)

我们导入的数据有 703 列，其中大部分是个别日期。我们将删除最后一列`queryTimeInSeconds`，这不是实际数据的一部分，也不应该存在。该列显示在以下截图中：

![](img/1f7c7c51-8f17-4f38-8e88-486352256436.png)

由于列太多，行太少，我们希望将列变成行，其中每个状态都有一行记录。为了执行此操作，我们需要将列翻转为行，反之亦然。为此，我们需要执行以下步骤：

1.  首先，我们重置索引，以便将当前索引作为另一列引入。当我们翻转`DataFrame`时，此列的值将成为列名。使用`head()`方法确认，如下截图所示：

![](img/5f7a32fa-1062-4c86-beda-ce274aaefca3.png)

1.  接下来，使用称为`T`或转置的方法翻转 DataFrame，如下截图所示：

![](img/40467528-0eb5-4c1d-978b-4543ee21c193.png)

1.  现在，在以下截图中，我们可以看到我们的行索引已成为日期，我们之前的行已成为列：

![](img/b08bb4d2-59a0-4a88-90e2-70905c491af6.png)

我们已经得到了想要的列和行，但还有一些其他事情需要做。我们希望日期值以日期时间格式显示，但目前它是文本格式。然而，数据框的转置已经将日期值更改为索引。我们需要首先使用`reset_index`方法将它们转换为列，如下面的屏幕截图所示：

![](img/0f50d3e6-b409-49b4-9114-b59b40d22472.png)

现在我们可以将这些文本数据转换为正确的日期时间格式，如下面的屏幕截图所示：

![](img/7b400b4e-6627-4c48-9c04-ce7ff291565d.png)

调用“info()”方法来确认这一点。我们在适当的日期时间格式中有了新的列“日期”。日期值需要以文本格式显示在列名索引中，如下面的屏幕截图所示：

![](img/602366da-4fe1-46d1-9bb1-6c7d5de73bb1.png)

我们将把索引设置回适当的“日期”列，并将“日期”列作为单独的列删除，如下面的屏幕截图所示：

![](img/d79a9097-673c-453c-8856-dccfbb46bb15.png)

现在，我们已经正确地在“日期”字段上建立了数据框的索引：

![](img/9e7edc0b-ecfd-40e7-843d-a7a68caf6ab3.png)

使用“plot()”方法绘制这些数据的示例图，并使用“plt.show()”方法显示，如下面的屏幕截图所示：

![](img/618c9870-2ba5-4919-93e4-08495b6d77fe.png)

现在，这些数据可以用作`pandas`时间序列函数，或者可以为不同日期子集可视化多个块等。

# 总结

在本章中，我们学习了如何准备数据分析的设置。我们看到了如何获取、读取和清理价格数据。我们还学习了如何探索、操作和可视化清理后的数据。

我们还探索了一些比特币和区块链图表，我们可以创建。我们学习了从哪里获取相关数据，并在 Jupyter Notebook 中读取了这些数据并导入了必要的模块。我们清理和操作了这些数据，最后，我们从这些数据和笔记本中创建了图表，但没有使用 Python。

我们还探索了来自骰子游戏门户的数据。我们添加了来自 API 的数据，并使用 pandas 将其转换为表格格式。我们探索了数据，并找到了需要清理的内容。我们清理和操作了数据，并使其准备好进行分析。
