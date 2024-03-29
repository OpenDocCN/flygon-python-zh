# 第二章：基本包、函数和概念

在开始任何实际配方之前，我们将使用本章来介绍几个核心数学概念和结构及其 Python 表示。特别是，我们将研究基本数值类型、基本数学函数（三角函数、指数函数和对数）以及矩阵。由于矩阵与线性方程组的解之间的联系，矩阵在大多数计算应用中都是基本的。我们将在本章中探讨其中一些应用，但矩阵将在整本书中发挥重要作用。

我们将按照以下顺序涵盖以下主要主题：

+   Python 数值类型

+   基本数学函数

+   NumPy 数组

+   矩阵

# 技术要求

在本章和本书的整个过程中，我们将使用 Python 3.8 版本，这是写作时最新的 Python 版本。本书中的大部分代码将适用于 Python 3.6 及更高版本。我们将在不同的地方使用 Python 3.6 引入的功能，包括 f-strings。这意味着您可能需要更改任何终端命令中出现的`python3.8`，以匹配您的 Python 版本。这可能是另一个版本的 Python，如`python3.6`或`python3.7`，或者更一般的命令，如`python3`或`python`。对于后者的命令，您需要使用以下命令检查 Python 的版本至少为 3.6：

```py
          python --version

```

Python 具有内置的数值类型和基本的数学函数，足以满足只涉及小计算的小型应用。NumPy 包提供了高性能的数组类型和相关例程（包括对数组进行高效操作的基本数学函数）。这个包将在本章和本书的其余部分中使用。我们还将在本章的后续配方中使用 SciPy 包。这两个包都可以使用您喜欢的包管理器（如`pip`）进行安装：

```py
          python3.8 -m pip install numpy scipy

```

按照惯例，我们将这些包导入为更短的别名。我们使用以下`import`语句将`numpy`导入为`np`，将`scipy`导入为`sp`：

```py
import numpy as np
import scipy as sp
```

这些约定在这些包的官方文档中使用，以及许多使用这些包的教程和其他材料中使用。

本章的代码可以在 GitHub 存储库的`Chapter 01`文件夹中找到，网址为[`github.com/PacktPublishing/Applying-Math-with-Python/tree/master/Chapter%2001`](https://github.com/PacktPublishing/Applying-Math-with-Python/tree/master/Chapter%2001)。

查看以下视频以查看代码实际操作：[`bit.ly/3g3eBXv`](https://bit.ly/3g3eBXv)。

# Python 数值类型

Python 提供了基本的数值类型，如任意大小的整数和浮点数（双精度）作为标准，但它还提供了几种在精度特别重要的特定应用中有用的附加类型。Python 还提供了对复数的（内置）支持，这对一些更高级的数学应用很有用。

## 十进制类型

对于需要精确算术运算的十进制数字的应用，可以使用 Python 标准库中的`decimal`模块中的`Decimal`类型：

```py
from decimal import Decimal
num1 = Decimal('1.1')
num2 = Decimal('1.563')
num1 + num2  # Decimal('2.663')
```

使用浮点对象进行此计算会得到结果 2.6630000000000003，这包括了一个小误差，这是因为某些数字不能用有限的 2 的幂的和来精确表示。例如，0.1 的二进制展开是 0.000110011...，它不会终止。因此，这个数字的任何浮点表示都会带有一个小误差。请注意，`Decimal`的参数是一个字符串，而不是一个浮点数。

`Decimal` 类型基于 IBM 通用十进制算术规范（[`speleotrove.com/decimal/decarith.html`](http://speleotrove.com/decimal/decarith.html)），这是一种浮点算术的替代规范，它通过使用 10 的幂而不是 2 的幂来精确表示十进制数。这意味着它可以安全地用于金融计算，其中舍入误差的累积将产生严重后果。然而，`Decimal` 格式的内存效率较低，因为它必须存储十进制数字而不是二进制数字（位），并且比传统的浮点数更加计算密集。

`decimal` 包还提供了一个`Context`对象，它允许对`Decimal`对象的精度、显示和属性进行精细控制。可以使用`decimal`模块的`getcontext`函数访问当前（默认）上下文。`getcontext`返回的`Context`对象具有许多可以修改的属性。例如，我们可以设置算术运算的精度：

```py
from decimal import getcontext
ctx = getcontext()
num = Decimal('1.1')
num**4  # Decimal('1.4641')
ctx.prec = 4 # set new precision
num**4  # Decimal('1.464')
```

当我们将精度设置为 `4` 时，而不是默认的 `28`，我们会发现 1.1 的四次方被舍入为 4 个有效数字。

甚至可以使用`localcontext`函数在本地设置上下文，该函数返回一个上下文管理器，在`with`块结束时恢复原始环境：

```py
from decimal import localcontext
num = Decimal("1.1")
with localcontext() as ctx:
    ctx.prec = 2
    num**4  # Decimal('1.5')
num**4  # Decimal('1.4641')

```

这意味着上下文可以在`with`块内自由修改，并且在结束时将返回默认值。

## 分数类型

或者，对于需要准确表示整数分数的应用程序，例如处理比例或概率时，可以使用 Python 标准库中 `fractions` 模块的 `Fraction` 类型。用法类似，只是我们通常将分数的分子和分母作为参数给出：

```py
from fractions import Fraction
num1 = Fraction(1, 3)
num2 = Fraction(1, 7)
num1 * num2  # Fraction(1, 21)
```

`Fraction` 类型只是简单地存储两个整数，即分子和分母，并且使用分数的加法和乘法的基本规则执行算术运算。

## 复数类型

Python 也支持复数，包括在代码中表示复数单位 `1j` 的文字字符。这可能与您从其他复数源上熟悉的表示复数单位的习语不同。大多数数学文本通常会使用符号 *i* 来表示复数单位：

```py
z = 1 + 1j
z + 2  # 3 + 1j
z.conjugate()  # 1 - 1j
```

Python 标准库的 `cmath` 模块提供了专门针对“复数” - 意识的数学函数。

# 基本数学函数

基本数学函数出现在许多应用程序中。例如，对数可以用于将呈指数增长的数据缩放为线性数据。指数函数和三角函数在处理几何信息时是常见的固定内容，*gamma 函数* 出现在组合学中，*高斯误差函数* 在统计学中很重要*.*

Python 标准库中的 `math` 模块提供了所有标准数学函数，以及常见常数和一些实用函数，可以使用以下命令导入：

```py
import math
```

一旦导入，我们可以使用此模块中包含的任何数学函数。例如，要找到非负数的平方根，我们将使用 `math` 中的 `sqrt` 函数：

```py
import math
math.sqrt(4)  #  2.0
```

尝试使用 `sqrt` 函数的负参数将引发 ValueError。这个 `sqrt` 函数不定义负数的平方根，它只处理*实数*。负数的平方根——这将是一个复数——可以使用 Python 标准库中 `cmath` 模块的替代 `sqrt` 函数找到。

三角函数，正弦、余弦和正切，在`math`模块中分别以它们的常见缩写`sin`、`cos`和`tan`可用。`pi` 常数保存了π的值，约为 3.1416：

```py
theta = pi/4
math.cos(theta)  # 0.7071067811865476
math.sin(theta)  # 0.7071067811865475
math.tan(theta)  # 0.9999999999999999
```

`math`模块中的反三角函数分别命名为`acos`、`asin`和`atan`：

```py
math.asin(-1)  # -1.5707963267948966
math.acos(-1)  # 3.141592653589793
math.atan(1)  # 0.7853981633974483
```

`math`模块中的`log`函数执行对数。它有一个可选参数来指定对数的底数（注意第二个参数只能是位置参数）。默认情况下，没有可选参数，它是以*自然对数*为底数*e*。*e*常数可以使用`math.e`来访问：

```py
math.log(10) # 2.302585092994046
math.log(10, 10) # 1.0
```

`math`模块还包含`gamma`函数，即伽玛函数，以及`erf`函数，即高斯误差函数，这在统计学中非常重要。这两个函数都是通过积分来定义的。伽玛函数由积分定义

![](img/cce1e648-6fc9-4ba8-8bda-370d76d95bc6.png)

误差函数由下式定义

![](img/a942862a-1ef4-40bd-8a6c-1bd93716ea47.png)

误差函数定义中的积分无法使用微积分来求解，而必须通过数值计算来完成：

```py
math.gamma(5) # 24.0
math.erf(2) # 0.9953222650189527
```

除了标准函数，如三角函数、对数和指数函数之外，`math`模块还包含各种理论和组合函数。这些包括`comb`和`factorial`函数，它们在各种应用中非常有用。使用参数*n*和*k*调用的`comb`函数返回从*n*个项目的集合中选择*k*个项目的方式数，如果顺序不重要且没有重复。例如，先选择 1 再选择 2 与先选择 2 再选择 1 是相同的。这个数字有时被写为*^nC[k]*。使用参数*n*调用的阶乘函数返回阶乘*n! = n(n-1)(n-2)*…1：

```py
math.comb(5, 2)  # 10
math.factorial(5)  # 120
```

对负数应用阶乘会引发`ValueError`。整数*n*的阶乘与*n + 1*处的伽玛函数的值相符，即

![](img/07b31532-3dc5-4e0e-bda5-374fc8eb499e.png)

`math`模块还包含一个返回其参数的*最大公约数*的函数，称为`gcd`。*a*和*b*的最大公约数是最大的整数*k*，使得*k*能够完全整除*a*和*b*：

```py
math.gcd(2, 4)  # 2
math.gcd(2, 3)  # 1
```

还有一些用于处理浮点数的函数。`fsum`函数对数字的可迭代对象执行加法，并在每一步跟踪总和，以减少结果中的误差。以下示例很好地说明了这一点：

```py
nums = [0.1]*10  # list containing 0.1 ten times
sum(nums)  # 0.9999999999999999
math.fsum(nums)  # 1.0
```

`isclose`函数返回`True`，如果参数之间的差小于公差。这在单元测试中特别有用，因为基于机器架构或数据变异性，结果可能会有小的变化。

最后，`math`中的`floor`和`ceil`函数提供了它们的参数的下限和上限。数字*x*的*floor*是最大的整数*f*，使得*f ≤ x*，*x*的*ceiling*是最小的整数*c*，使得*x ≤ c*。在将一个数字除以另一个数字得到浮点数和整数之间转换时，这些函数非常有用。

`math`模块包含了在 C 中实现的函数（假设你正在运行 CPython），因此比在 Python 中实现的函数要快得多。如果你需要将函数应用于一个相对较小的数字集合，这个模块是一个不错的选择。如果你想要同时将这些函数应用于大量数据集合，最好使用 NumPy 包中的等效函数，这些函数对数组的处理更有效率。总的来说，如果你已经导入了 NumPy 包，那么最好总是使用这些函数的 NumPy 等效函数，以减少出错的机会。

# NumPy 数组

NumPy 提供了高性能的数组类型和用于在 Python 中操作这些数组的例程。这些数组对于处理性能至关重要的大型数据集非常有用。NumPy 构成了 Python 中的数值和科学计算堆栈的基础。在幕后，NumPy 利用低级库来处理向量和矩阵，例如**基本线性代数子程序**（**BLAS**）包，以及**线性代数包**（**LAPACK**）包含更高级的线性代数例程。

传统上，NumPy 包是使用更短的别名`np`导入的，可以使用以下`import`语句来实现：

```py
import numpy as np
```

特别是在 NumPy 文档和更广泛的科学 Python 生态系统（SciPy、Pandas 等）中使用了这种约定。

NumPy 库提供的基本类型是`ndarray`类型（以下简称 NumPy 数组）。通常，您不会创建此类型的自己的实例，而是使用`array`之类的辅助例程之一来正确设置类型。`array`例程从类似数组的对象创建 NumPy 数组，这通常是一组数字或一组（数字）列表。例如，我们可以通过提供包含所需元素的列表来创建一个简单的数组：

```py
ary = np.array([1, 2, 3, 4])  # array([1, 2, 3, 4])
```

NumPy 数组类型（`ndarray`）是围绕基础 C 数组结构的 Python 包装器。数组操作是用 C 实现的，并针对性能进行了优化。NumPy 数组必须由同质数据组成（所有元素具有相同的类型），尽管此类型可以是指向任意 Python 对象的指针。如果在创建时未明确提供使用`dtype`关键字参数，则 NumPy 将推断出适当的数据类型：

```py
np.array([1, 2, 3, 4], dtype=np.float32)
# array([1., 2., 3., 4.], dtype=float32)
```

在幕后，任何形状的 NumPy 数组都是一个包含原始数据的缓冲区，作为一个平坦（一维）数组，并包含一系列额外的元数据，用于指定诸如元素类型之类的细节。

创建后，可以使用数组的`dtype`属性访问数据类型。修改`dtype`属性将产生不良后果，因为构成数组中的原始字节将被重新解释为新的数据类型。例如，如果我们使用 Python 整数创建数组，NumPy 将在数组中将其转换为 64 位整数。更改`dtype`值将导致 NumPy 将这些 64 位整数重新解释为新的数据类型：

```py
arr = np.array([1, 2, 3, 4])
print(arr.dtype) # dtype('int64')
arr.dtype = np.float32
print(arr)
# [1.e-45 0.e+00 3.e-45 0.e+00 4.e-45 0.e+00 6.e-45 0.e+00]
```

每个 64 位整数都被重新解释为两个 32 位浮点数，这显然会产生无意义的值。相反，如果您希望在创建后更改数据类型，请使用`astype`方法指定新类型。更改数据类型的正确方法如下所示：

```py
arr = arr.astype(np.float32)
print(arr)
# [1\. 2\. 3\. 4.]
```

NumPy 还提供了一些用于创建各种标准数组的例程。`zeros`例程创建一个指定形状的数组，其中每个元素都是`0`，而`ones`例程创建一个数组，其中每个元素都是`1`。

## 元素访问

NumPy 数组支持`getitem`协议，因此可以像列表一样访问数组中的元素，并支持所有按组件执行的算术操作。这意味着我们可以使用索引表示法和索引来检索指定索引处的元素，如下所示：

```py
ary = np.array([1, 2, 3, 4])
ary[0]  # 1
ary[2]  # 3
```

这还包括从现有数组中提取数据数组的常规切片语法。数组的切片再次是一个数组，其中包含切片指定的元素。例如，我们可以检索包含`ary`的前两个元素的数组，或者包含偶数索引处的元素的数组，如下所示：

```py
first_two = ary[:2]  # array([1, 2])
even_idx = ary[::2]  # array([1, 3])
```

切片的语法是`start:stop:step`。我们可以省略`start`和`stop`中的一个或两个，以从所有元素的开头或结尾分别获取。我们也可以省略`step`参数，这种情况下我们也会去掉尾部的`:`。`step`参数描述了应该选择的选定范围内的元素。值为`1`选择每个元素，或者如本例中，值为`2`选择每第二个元素（从`0`开始给出偶数编号的元素）。这个语法与切片 Python 列表的语法相同。

## 数组的算术和函数

NumPy 提供了许多*通用函数*（ufunc），这些函数可以高效地操作 NumPy 数组类型。特别是，在*基本数学函数*部分讨论的所有基本数学函数在 NumPy 中都有类似的函数，可以在 NumPy 数组上操作。通用函数还可以执行*广播*，以允许它们在不同但兼容的形状的数组上进行操作。

NumPy 数组上的算术运算是逐分量执行的。以下示例最能说明这一点：

```py
arr_a = np.array([1, 2, 3, 4])
arr_b = np.array([1, 0, -3, 1])
arr_a + arr_b  # array([2, 2, 0, 5])
arr_a - arr_b  # array([0, 2, 6, 3])
arr_a * arr_b  # array([ 1, 0, -9, 4])
arr_b / arr_a  # array([ 1\. , 0\. , -1\. , 0.25])
arr_b**arr_a  # array([1, 0, -27, 1])
```

请注意，数组必须具有相同的形状，这意味着具有相同的长度。对不同形状的数组进行算术运算将导致`ValueError`。通过数字进行加法、减法、乘法或除法将导致数组，其中已对每个分量应用了操作。例如，我们可以使用以下命令将数组中的所有元素乘以`2`：

```py
arr = np.array([1, 2, 3, 4])
new = 2*arr
print(new)
# [2, 4, 6, 8]
```

## 有用的数组创建例程

要在两个给定端点之间以规则间隔生成数字数组，可以使用`arange`例程或`linspace`例程。这两个例程之间的区别在于`linspace`生成一定数量（默认为 50）的值，这些值在两个端点之间具有相等的间距，包括两个端点，而`arange`生成给定步长的数字，但不包括上限。`linspace`例程生成封闭区间*a ≤ x ≤ b*中的值，而`arange`例程生成半开区间*a≤ x < b*中的值：

```py
np.linspace(0, 1, 5)  # array([0., 0.25, 0.5, 0.75, 1.0])
np.arange(0, 1, 0.3)  # array([0.0, 0.3, 0.6, 0.9])
```

请注意，使用`linspace`生成的数组恰好有 5 个点，由第三个参数指定，包括`0`和`1`两个端点。使用`arange`生成的数组有 4 个点，不包括右端点`1`；0.3 的额外步长将等于 1.2，这比 1 大。

## 更高维度的数组

NumPy 可以创建任意维度的数组，使用与简单一维数组相同的`array`例程创建。数组的维数由提供给`array`例程的嵌套列表的数量指定。例如，我们可以通过提供一个列表的列表来创建一个二维数组，其中内部列表的每个成员都是一个数字，如下所示：

```py
mat = np.array([[1, 2], [3, 4]])
```

NumPy 数组具有`shape`*属性，描述了每个维度中元素的排列方式。对于二维数组，形状可以解释为数组的行数和列数。*

*NumPy 将形状存储为数组对象上的`shape`属性，这是一个元组。这个元组中的元素数量就是数组的维数：

```py
vec = np.array([1, 2])
mat.shape  # (2, 2)
vec.shape  # (2,)
```

由于 NumPy 数组中的数据存储在一个扁平（一维）数组中，可以通过简单地更改相关的元数据来以很小的成本重新塑造数组。这是通过 NumPy 数组的`reshape`方法完成的：

```py
mat.reshape(4,)  # array([1, 2, 3, 4])
```

请注意，元素的总数必须保持不变。矩阵`mat`最初的形状为`(2, 2)`，共有 4 个元素，后者是一个形状为`(4,)`的一维数组，再次共有 4 个元素。当总元素数量不匹配时，尝试重新塑造将导致`ValueError`。

要创建更高维度的数组，只需添加更多级别的嵌套列表。为了更清楚地说明这一点，在下面的示例中，我们在构造数组之前将第三维度中的每个元素的列表分开：

```py
mat1 = [[1, 2], [3, 4]]
mat2 = [[5, 6], [7, 8]]
mat3 = [[9, 10], [11, 12]]
arr_3d = np.array([mat1, mat2, mat3])
arr_3d.shape  # (3, 2, 2)
```

请注意，形状的第一个元素是最外层的，最后一个元素是最内层的。

这意味着向数组添加一个额外的维度只是提供相关的元数据。使用`array`例程，`shape`元数据由参数中每个列表的长度描述。最外层列表的长度定义了该维度的相应`shape`参数，依此类推。

NumPy 数组在内存中的大小并不显著取决于维度的数量，而只取决于元素的总数，这是`shape`参数的乘积。但是，请注意，高维数组中的元素总数往往较大。

要访问多维数组中的元素，您可以使用通常的索引表示法，但是不是提供单个数字，而是需要在每个维度中提供索引。对于 2×2 矩阵，这意味着指定所需元素的行和列：

```py
mat[0, 0]  # 1 - top left element
mat[1, 1]  # 4 - bottom right element
```

索引表示法还支持在每个维度上进行切片，因此我们可以使用切片`mat[:, 0]`提取单列的所有成员，如下所示：

```py
mat[:, 0]
# array([1, 3])
```

请注意，切片的结果是一个一维数组。

数组创建函数`zeros`和`ones`可以通过简单地指定一个具有多个维度参数的形状来创建多维数组。

# 矩阵

NumPy 数组也可以作为*矩阵*，在数学和计算编程中是基本的。*矩阵*只是一个二维数组。矩阵在许多应用中都是核心，例如几何变换和同时方程，但也出现在其他领域的有用工具中，例如统计学。矩阵本身只有在我们为它们配备*矩阵算术*时才是独特的（与任何其他数组相比）。矩阵具有逐元素的加法和减法运算，就像 NumPy 数组一样，还有一种称为*标量乘法*的第三种运算，其中我们将矩阵的每个元素乘以一个常数，以及一种不同的*矩阵乘法*概念。矩阵乘法与其他乘法概念根本不同，我们稍后会看到。

矩阵的一个最重要的属性是其形状，与 NumPy 数组的定义完全相同。具有*m*行和*n*列的矩阵通常被描述为*m×n*矩阵。具有与列数相同的行数的矩阵被称为*方阵*，这些矩阵在向量和矩阵理论中起着特殊的作用。

*单位矩阵*（大小为*n*）是*n×n*矩阵，其中（*i*，*i*）-th 条目为 1，而（*i*，*j*）-th 条目对于*i* ≠ *j*为零。有一个数组创建例程，为指定的*n*值提供*n×n*单位矩阵：

```py
np.eye(3)
# array([[1., 0., 0.],
#        [0., 1., 0.],
#        [0., 0., 1.]])
```

## 基本方法和属性

与矩阵相关的术语和数量有很多。我们在这里只提到两个这样的属性，因为它们以后会有用。这些是矩阵的*转置*，其中行和列互换，以及*迹*，它是方阵沿着*主对角线*的元素之和。主对角线由从矩阵左上角到右下角的线上的元素*a[ii]*组成。

NumPy 数组可以通过在`array`对象上调用`transpose`方法轻松转置。实际上，由于这是一个常见的操作，数组有一个方便的属性`T`，它返回矩阵的转置。转置会颠倒矩阵（数组）的形状顺序，使行变为列，列变为行。例如，如果我们从一个 3×2 矩阵（3 行，2 列）开始，那么它的转置将是一个 2×3 矩阵，就像下面的例子一样：

```py
mat = np.array([[1, 2], [3, 4]])
mat.transpose()
# array([[1, 3],
#       [2, 4]])
mat.T
# array([[1, 3],
#       [2, 4]])
```

与矩阵相关的另一个数量有时也是有用的是*trace*。方阵*A*的 trace，其条目如前面的代码所示，被定义为*leading diagonal*上的元素之和，它由从左上角对角线到右下角的元素组成。trace 的公式如下*

*![](img/e8eaa631-c006-4401-853f-b474da2c9319.png)

NumPy 数组有一个`trace`方法，返回矩阵的迹：

```py
A = np.array([[1, 2], [3, 4]])
A.trace()  # 5
```

trace 也可以使用`np.trace`函数访问，它不绑定到数组。

## 矩阵乘法

矩阵乘法是在两个矩阵上执行的操作，它保留了两个矩阵的一些结构和特性。形式上，如果*A*是一个*l × m*矩阵，*B*是一个*m × n*矩阵，如下所述

![](img/76bace88-ee7c-41ba-b7a5-d20ce18c1cca.png)

那么矩阵积*A*和*B*是一个*l × n*矩阵，其(*p*, *q*)-th 条目由下式给出

![](img/9b077bba-836c-4376-acf6-177457bb7e62.png)

请注意，第一个矩阵的列数**必须**与第二个矩阵的行数匹配，以便定义矩阵乘法。如果定义了矩阵乘积*AB*，我们通常写*AB*，如果它被定义。矩阵乘法是一种特殊的操作。它不像大多数其他算术运算那样是*交换的*：即使*AB*和*BA*都可以计算，它们不一定相等。实际上，这意味着矩阵的乘法顺序很重要。这源自矩阵代数作为线性映射的表示的起源，其中乘法对应于函数的组合。

Python 有一个保留给矩阵乘法的运算符`@`，这是在 Python 3.5 中添加的。NumPy 数组实现了这个运算符来执行矩阵乘法。请注意，这与数组的分量乘法`*`在本质上是不同的：

```py
A = np.array([[1, 2], [3, 4]])
B = np.array([[-1, 1], [0, 1]])
A @ B
# array([[-1, 3], 
#        [-3, 7]])
A * B # different from A @ B
# array([[-1, 2], 
#        [ 0, 4]])
```

单位矩阵是矩阵乘法下的*中性元素*。也就是说，如果*A*是任意的*l × m*矩阵，*I*是*m* × *m*单位矩阵，则*AI = A*。可以使用 NumPy 数组轻松检查特定示例：

```py
A = np.array([[1, 2], [3, 4]])
I = np.eye(2)
A @ I
# array([[1, 2],
#        [3, 4]])
```

## 行列式和逆

一个方阵的*determinant*在大多数应用中都很重要，因为它与找到矩阵的逆的强连接。如果行和列的数量相等，则矩阵是*方阵*。特别地，一个具有非零行列式的矩阵具有（唯一的）逆，这对于某些方程组的唯一解是成立的。矩阵的行列式是递归定义的。对于一个 2×2 矩阵

![](img/33bf7b70-0248-45b7-8576-937cb4ef8186.png)

矩阵*A*的*determinant*由以下公式定义

![](img/16497d22-d726-4c2e-877a-ddc3f766124c.png)

对于一个一般的*n*×*n*矩阵

![](img/b9423b8e-3454-4283-bdba-97469ed68558.png)

其中*n* > 2，我们定义子矩阵*A[i,j]*，对于 1 ≤ *i*，*j* ≤ *n*，为从*A*中删除第*i*行和第*j*列的结果。子矩阵*A[i,j]*是一个*(n-1) ×* (*n*-1)矩阵，因此我们可以计算行列式。然后我们定义*A*的行列式为数量

![](img/6d24b037-4dcf-4bb0-9da9-ed1338f1e34b.png)

实际上，出现在前述方程中的索引 1 可以被任何 1 ≤ i≤ *n*替换，结果将是相同的。

计算矩阵行列式的 NumPy 例程包含在一个名为`linalg`的单独模块中。这个模块包含了许多关于*线性代数*的常见例程，线性代数是涵盖向量和矩阵代数的数学分支。计算方阵行列式的例程是`det`例程：

```py
from numpy import linalg
linalg.det(A)  # -2.0000000000000004
```

请注意，在计算行列式时发生了浮点舍入误差。

如果安装了 SciPy 包，还提供了一个扩展了 NumPy`linalg`的`linalg`模块。SciPy 版本不仅包括额外的例程，而且始终使用 BLAS 和 LAPACK 支持进行编译，而对于 NumPy 版本，这是可选的。因此，如果速度很重要，SciPy 变体可能更可取，这取决于 NumPy 的编译方式。

*n × n*矩阵*A*的*逆*是（必然唯一的）*n × n*矩阵*B*，使得*AB*=*BA*=*I*，其中*I*表示*n × n*单位矩阵，这里执行的乘法是矩阵乘法。并非每个方阵都有逆；那些没有逆的有时被称为*奇异*矩阵。事实上，当*A*没有逆时，也就是说，当该矩阵的行列式为 0 时，它是非奇异的。当*A*有逆时，习惯上用*A^(-1)*表示它。

`linalg`模块的`inv`例程计算矩阵的逆，如果存在的话：

```py
linalg.inv(A)
# array([[-2\. , 1\. ],
#        [ 1.5, -0.5]])
```

我们可以通过矩阵乘法（在任一侧）乘以逆矩阵，并检查我们是否得到了 2 × 2 单位矩阵，来检查`inv`例程给出的矩阵是否确实是`A`的矩阵逆：

```py
Ainv = linalg.inv(A)
Ainv @ A
# Approximately
# array([[1., 0.],
#        [0., 1.]])
A @ Ainv
# Approximately
# array([[1., 0.],
#        [0., 1.]])
```

这些计算中会有浮点误差，这已经被隐藏在`Approximately`注释后面，这是由于计算矩阵的逆的方式。

`linalg`包还包含许多其他方法，如`norm`，它计算矩阵的各种范数。它还包含了各种分解矩阵和解方程组的函数。

还有指数函数`expm`、对数函数`logm`、正弦函数`sinm`、余弦函数`cosm`和切线函数`tanm`的矩阵类比。请注意，这些函数与基本 NumPy 包中的标准`exp`、`log`、`sin`、`cos`和`tan`函数不同，后者是在元素基础上应用相应的函数。相反，矩阵指数函数是使用矩阵的“幂级数”定义的。

![](img/3ca5b8f2-59d4-40cd-b789-e6a3561dde62.png)

其中*A*是一个*n × n*矩阵，*A^k*是*A*的第*k*个*矩阵幂*；也就是说，*A*矩阵连续乘以自身*k*次。请注意，这个“幂级数”总是在适当的意义下收敛。按照惯例，我们取*A⁰* = *I*，其中*I*是*n × n*单位矩阵。这与实数或复数的指数函数的常规幂级数定义完全类似，但是用矩阵和矩阵乘法代替了数字和（常规）乘法。其他函数也是以类似的方式定义的，但我们将跳过细节。

## 方程组

解（线性）方程组是研究矩阵的主要动机之一。这类问题在各种应用中经常出现。我们从写成线性方程组的形式开始

![](img/4ebf6e1d-e0bd-4f8c-a95f-58b5dd15d391.png)

其中*n*至少为 2，*a[i,j]*和*b[i]*是已知值，*x[i]*值是我们希望找到的未知值。

在解这样的方程组之前，我们需要将问题转化为矩阵方程。这是通过将系数*a[i,j]*收集到一个*n × n*矩阵中，并使用矩阵乘法的性质将这个矩阵与方程组联系起来实现的。因此，让

![](img/0bf397b7-5253-4e69-a7f5-d6b81502427a.png)

是包含方程中系数的矩阵。然后，如果我们将**x**作为未知数（列）向量，包含*x[i]*值，**b**作为（列）向量，包含已知值*b[i]*，那么我们可以将方程组重写为单个矩阵方程

![](img/08d03070-dc5f-4c12-999d-3e555762c0ae.png)

现在我们可以使用矩阵技术来解决这个问题。在这种情况下，我们将列向量视为*n × 1*矩阵，因此前面方程中的乘法是矩阵乘法。为了解决这个矩阵方程，我们使用`linalg`模块中的`solve`例程。为了说明这种技术，我们将解决以下方程组作为示例：

![](img/47b1fa6c-38f1-4b2d-9a9a-9b78774dcc02.png)

这些方程有三个未知值，*x[1]*，*x[2]*和*x[3]*。首先，我们创建系数矩阵和向量**b**。由于我们使用 NumPy 来处理矩阵和向量，我们为矩阵*A*创建一个二维 NumPy 数组，为**b**创建一个一维数组：

```py
import numpy as np
from numpy import linalg

A = np.array([[3, -2, 1], [1, 1, -2], [-3, -2, 1]])
b = np.array([7, -4, 1])
```

现在，可以使用`solve`例程找到方程组的解：

```py
linalg.solve(A, b)  # array([ 1., -1., 2.])
```

这确实是方程组的解，可以通过计算`A @ x`并将结果与`b`数组进行对比来轻松验证。在这个计算中可能会出现浮点舍入误差。

`solve`函数需要两个输入，即系数矩阵*A*和右侧向量**b**。它使用 LAPACK 例程解决方程组，将矩阵*A*分解为更简单的矩阵，以快速减少为一个可以通过简单替换解决的更简单的问题。这种解决矩阵方程的技术非常强大和高效，并且不太容易受到浮点舍入误差的影响。例如，可以通过与矩阵*A*的逆矩阵相乘（在左侧）来计算方程组的解，如果已知逆矩阵。然而，这通常不如使用`solve`例程好，因为它可能更慢或导致更大的数值误差。

在我们使用的示例中，系数矩阵*A*是方阵。也就是说，方程的数量与未知值的数量相同。在这种情况下，如果且仅当矩阵*A*的行列式不为 0 时，方程组有唯一解。在矩阵*A*的行列式为 0 的情况下，可能会出现两种情况：方程组可能没有解，这种情况下我们称方程组是*不一致*的；或者可能有无穷多个解。一致和不一致系统之间的区别通常由向量**b**决定。例如，考虑以下方程组：

![](img/01c4d5b9-99a5-4ca5-a7e4-b3deb5cc1190.png)

左侧的方程组是一致的，有无穷多个解；例如，取*x* = 1 和*y = 1*或*x = 0*和*y = 2*都是解。右侧的方程组是不一致的，没有解。在上述两种情况下，`solve`例程将失败，因为系数矩阵是奇异的。

系数矩阵不需要是方阵才能解决方程组。例如，如果方程比未知值多（系数矩阵的行数多于列数）。这样的系统被称为*过度规定*，只要是一致的，它就会有解。如果方程比未知值少，那么系统被称为*不足规定*。如果是一致的，不足规定的方程组通常有无穷多个解，因为没有足够的信息来唯一指定所有未知值。不幸的是，即使系统有解，`solve`例程也无法找到系数矩阵不是方阵的系统的解。

## 特征值和特征向量

考虑矩阵方程*A***x** = λ**x**，其中*A*是一个方阵（*n × n*），**x**是一个向量，λ是一个数字。对于这个方程有一个**x**可以解决的λ被称为*特征值*，相应的向量**x**被称为*特征向量*。特征值和相应的特征向量对编码了关于矩阵*A*的信息，因此在许多矩阵出现的应用中非常重要。

我们将演示使用以下矩阵计算特征值和特征向量：

![](img/192ad11a-26cd-4dd1-a9d6-21df5cb79e7e.png)

我们必须首先将其定义为 NumPy 数组：

```py
import numpy as np
from numpy import linalg
A = np.array([[3, -1, 4], [-1, 0, -1], [4, -1, 2]])
```

`linalg`模块中的`eig`例程用于找到方阵的特征值和特征向量。这个例程返回一对`(v, B)`，其中`v`是包含特征值的一维数组，`B`是其列是相应特征向量的二维数组：

```py
v, B = linalg.eig(A)
```

只有具有实数条目的矩阵才可能具有复特征值和特征向量。因此，`eig`例程的返回类型有时将是复数类型，如`complex32`或`complex64`。在某些应用中，复特征值具有特殊含义，而在其他情况下，我们只考虑实特征值。

我们可以使用以下序列从`eig`的输出中提取特征值/特征向量对：

```py
i = 0 # first eigenvalue/eigenvector pair
lambda0 = v[i]
print(lambda0)
# 6.823156164525971
x0 = B[:, i] # ith column of B
print(x0)
# array([ 0.73271846, -0.20260301, 0.649672352])

```

`eig`例程返回的特征向量是*归一化*的，使得它们的范数（长度）为 1。 （*欧几里得范数*被定义为数组成员的平方和的平方根。）我们可以通过使用`linalg`中的`norm`例程计算向量的范数来检查这一点：

```py
linalg.norm(x0)  # 1.0  - eigenvectors are normalized.
```

最后，我们可以通过计算乘积`A @ x0`并检查，直到浮点精度，这等于`lambda0*x0`，来检查这些值确实满足特征值/特征向量对的定义：

```py
lhs = A @ x0
rhs = lambda0*x0
linalg.norm(lhs - rhs)  # 2.8435583831733384e-15 - very small.
```

这里计算的范数表示方程*A***x** = λ**x**的左侧`lhs`和右侧`rhs`之间的“距离”。由于这个距离非常小（小数点后 0 到 14 位），我们可以相当确信它们实际上是相同的。这不为零的事实可能是由于浮点精度误差。

`eig`例程是围绕低级 LAPACK 例程的包装器，用于计算特征值和特征向量。找到特征值和特征向量的理论过程是首先通过解方程找到特征值

![](img/29520bb2-fc2e-42af-bbb9-e3236ea9936a.png)

其中*I*是适当的单位矩阵，以找到值λ。左侧确定的方程是λ的多项式，称为*A*的*特征多项式*。然后可以通过解决矩阵方程找到相应的特征向量

![](img/a8e09b6c-ef9b-406f-980b-cfacf87b2969.png)

其中λ*[j]*是已经找到的特征值之一。实际上，这个过程有些低效，有替代策略可以更有效地计算特征值和特征向量。

特征值和特征向量的一个关键应用是*主成分分析*，这是一种将大型复杂数据集减少到更好地理解内部结构的关键技术。

我们只能计算方阵的特征值和特征向量；对于非方阵，该定义没有意义。有一种将特征值和特征值推广到非方阵的称为*奇异值*的方法。

## 稀疏矩阵

诸如前面讨论的那样的线性方程组在数学中非常常见，特别是在数学计算中。在许多应用中，系数矩阵将非常庞大，有数千行和列，并且可能来自替代来源而不是简单地手动输入。在许多情况下，它还将是*稀疏*矩阵，其中大多数条目为 0。

如果矩阵的大多数元素为零，则矩阵是*稀疏*的。要调用矩阵稀疏，需要为零的确切元素数量并不明确定义。稀疏矩阵可以更有效地表示，例如，只需存储非零的索引（*i*，*j*）和值*a[i,j]*。有整个集合的稀疏矩阵算法，可以在矩阵确实足够稀疏的情况下大大提高性能。

稀疏矩阵出现在许多应用程序中，并且通常遵循某种模式。特别是，解决**偏微分方程**（**PDEs**）的几种技术涉及解决稀疏矩阵方程（请参阅第三章，*微积分和微分方程*），与网络相关的矩阵通常是稀疏的。`sparse.csgraph`模块中包含与网络（图）相关的稀疏矩阵的其他例程。我们将在第五章中进一步讨论这些内容，*处理树和网络*。

`sparse`模块包含几种表示稀疏矩阵存储方式的不同类。存储稀疏矩阵的最基本方式是存储三个数组，其中两个包含表示非零元素的索引的整数，第三个包含相应元素的数据。这是`coo_matrix`类的格式。然后有压缩列 CSC（`csc_matrix`）和压缩行 CSR（`csr_matrix`）格式，它们分别提供了有效的列或行切片。`sparse`中还有三个额外的稀疏矩阵类，包括`dia_matrix`，它有效地存储非零条目沿对角线带出现的矩阵。

来自 SciPy 的`sparse`模块包含用于创建和处理稀疏矩阵的例程。我们使用以下`import`语句从 SciPy 导入`sparse`模块：

```py
import numpy as np
from scipy import sparse
```

稀疏矩阵可以从完整（密集）矩阵或其他某种数据结构创建。这是使用特定格式的构造函数来完成的，您希望将稀疏矩阵存储在其中。

例如，我们可以通过使用以下命令将密集矩阵存储为 CSR 格式：

```py
A = np.array([[1., 0., 0.], [0., 1., 0.], [0., 0., 1.]])
sp_A = sparse.csr_matrix(A)
```

如果您手动生成稀疏矩阵，该矩阵可能遵循某种模式，例如以下*三对角*矩阵：

![](img/0d9a6f28-5695-48e2-b5eb-606945aaddf9.png)

在这里，非零条目出现在对角线上以及对角线两侧，并且每行中的非零条目遵循相同的模式。要创建这样的矩阵，我们可以使用`sparse`中的数组创建例程之一，例如`diags`，这是一个用于创建具有对角线模式的矩阵的便利例程：

```py
T = sparse.diags([-1, 2, -1], (-1, 0, 1), shape=(5, 5), format="csr")
```

这将创建矩阵*T*，并按压缩稀疏行 CSR 格式存储它。第一个参数指定应出现在输出矩阵中的值，第二个参数是相对于应放置值的对角线位置的位置。因此，元组中的 0 索引表示对角线条目，-1 表示在行中对角线的左侧，+1 表示在行中对角线的右侧。`shape`关键字参数给出了生成的矩阵的维度，`format`指定了矩阵的存储格式。如果没有使用可选参数提供格式，则将使用合理的默认值。数组`T`可以使用`toarray`方法扩展为完整（*密集*）矩阵：

```py
T.toarray()
# array([[ 2, -1,  0,  0,  0],
#        [-1,  2, -1,  0,  0],
#        [ 0, -1,  2, -1,  0],
#        [ 0,  0, -1,  2, -1],
#        [ 0,  0,  0, -1,  2]])
```

当矩阵很小时（就像这里一样），稀疏求解例程和通常的求解例程之间的性能差异很小。

一旦矩阵以稀疏格式存储，我们可以使用`sparse`的`linalg`子模块中的稀疏求解例程。例如，我们可以使用该模块的`spsolve`例程来解决矩阵方程。`spsolve`例程将把矩阵转换为 CSR 或 CSC，如果矩阵不是以这些格式之一提供的话，可能会增加额外的计算时间：

```py
from scipy.sparse import linalg
linalg.spsolve(T.tocsr(), np.array([1, 2, 3, 4, 5]))
# array([ 5.83333333, 10.66666667, 13.5 , 13.33333333, 9.16666667])
```

`sparse.linalg`模块还包含许多可以在 NumPy（或 SciPy）的`linalg`模块中找到的接受稀疏矩阵而不是完整 NumPy 数组的例程，例如`eig`和`inv`。

# 摘要

Python 提供了对数学的内置支持，包括一些基本的数值类型、算术和基本的数学函数。然而，对于涉及大量数值值数组的更严肃的计算，您应该使用 NumPy 和 SciPy 软件包。NumPy 提供高性能的数组类型和基本例程，而 SciPy 提供了更多用于解方程和处理稀疏矩阵（以及许多其他内容）的特定工具。

NumPy 数组可以是多维的。特别是，二维数组具有矩阵属性，可以使用 NumPy 或 SciPy 的`linalg`模块（前者是后者的子集）来访问。此外，Python 中有一个特殊的矩阵乘法运算符`@`，它是为 NumPy 数组实现的。

在下一章中，我们将开始查看一些配方。

# 进一步阅读

有许多数学教科书描述矩阵和线性代数的基本属性，线性代数是研究向量和矩阵的学科。一个很好的入门文本是*Blyth, T. and Robertson, E. (2013). Basic Linear Algebra**. London: Springer London, Limited*。

NumPy 和 SciPy 是 Python 数学和科学计算生态系统的一部分，并且有广泛的文档可以从官方网站[`scipy.org`](https://scipy.org)访问。我们将在本书中看到这个生态系统中的几个其他软件包。

有关 NumPy 和 SciPy 在幕后使用的 BLAS 和 LAPACK 库的更多信息可以在以下链接找到：BLAS：[`www.netlib.org/blas/`](https://www.netlib.org/blas/) 和 LAPACK：[`www.netlib.org/lapack/`](https://www.netlib.org/lapack/)。
