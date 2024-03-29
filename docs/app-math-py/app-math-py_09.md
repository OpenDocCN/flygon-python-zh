# 第十章：寻找最优解

在本章中，我们将讨论寻找给定情况下最佳结果的各种方法。这被称为*优化*，通常涉及最小化或最大化目标函数。*目标函数*是一个接受多个参数作为参数并返回代表给定参数选择的成本或回报的单个标量值的函数。关于最小化和最大化函数的问题实际上是相互等价的，因此我们只会在本章讨论最小化目标函数。最小化函数*f*(*x*)等同于最大化函数*-f*(*x*)。在我们讨论第一个配方时将提供更多细节。

我们可以利用的算法来最小化给定函数取决于函数的性质。例如，包含一个或多个变量的简单线性函数与具有许多变量的非线性函数相比，可用的算法不同。线性函数的最小化属于*线性规划*范畴，这是一个发展完善的理论。对于非线性函数，我们通常利用函数的梯度（导数）来寻找最小点。我们将讨论几种不同类型函数的最小化方法。

寻找单变量函数的极小值和极大值特别简单，如果函数的导数已知，可以轻松完成。如果不知道导数，则适用于适当配方的方法。*最小化非线性函数*配方中的注释提供了一些额外细节。

我们还将提供一个非常简短的介绍*博弈论*。广义上讲，这是一个围绕决策制定的理论，并在经济学等学科中具有广泛的影响。特别是，我们将讨论如何在 Python 中将简单的双人游戏表示为对象，计算与某些选择相关的回报，并计算这些游戏的纳什均衡。

我们将首先看如何最小化包含一个或多个变量的线性和非线性函数。然后，我们将继续研究梯度下降方法和使用最小二乘法进行曲线拟合。最后，我们将通过分析双人游戏和纳什均衡来结束本章。

在本章中，我们将涵盖以下配方：

+   最小化简单线性函数

+   最小化非线性函数

+   使用梯度下降法进行优化

+   使用最小二乘法拟合数据的曲线

+   分析简单的双人游戏

+   计算纳什均衡

让我们开始吧！

# 技术要求

在本章中，我们将像往常一样需要 NumPy 包、SciPy 包和 Matplotlib 包。我们还将需要 Nashpy 包用于最后两个配方。这些包可以使用您喜欢的包管理器（如`pip`）进行安装：

```py
          python3.8 -m pip install numpy scipy matplotlib nashpy

```

本章的代码可以在 GitHub 存储库的`Chapter 09`文件夹中找到，网址为[`github.com/PacktPublishing/Applying-Math-with-Python/tree/master/Chapter%2009`](https://github.com/PacktPublishing/Applying-Math-with-Python/tree/master/Chapter%2009)。

查看以下视频以查看代码的实际操作：[`bit.ly/2BjzwGo`](https://bit.ly/2BjzwGo)。

# 最小化简单线性函数

在优化中我们面临的最基本问题是找到函数取得最小值的参数。通常，这个问题受到参数可能值的一些限制的*约束*，这增加了问题的复杂性。显然，如果我们要最小化的函数也很复杂，那么这个问题的复杂性会进一步增加。因此，我们必须首先考虑*线性函数*，它们的形式如下：

![](img/81d86696-3f59-4c53-9c0b-7610003405b0.png)

为了解决这些问题，我们需要将约束转化为计算机可用的形式。在这种情况下，我们通常将它们转化为线性代数问题（矩阵和向量）。一旦完成了这一步，我们就可以使用 NumPy 和 SciPy 中的线性代数包中的工具来找到我们所寻求的参数。幸运的是，由于这类问题经常发生，SciPy 有处理这种转化和随后求解的例程。

在这个配方中，我们将使用 SciPy `optimize`模块的例程来解决以下受限线性最小化问题：

![](img/bf60d0a9-bda0-4b13-b5b8-a21a96bfe9b0.png)

这将受到以下条件的约束：

![](img/06c40ed3-2f7d-48d6-8d32-53756bc49383.png)

## 准备工作

对于这个配方，我们需要在别名`np`下导入 NumPy 包，以`plt`的名称导入 Matplotlib `pyplot`模块，以及 SciPy `optimize`模块。我们还需要从`mpl_toolkits.mplot3d`导入`Axes3D`类，以使 3D 绘图可用：

```py
import numpy as np
from scipy import optimize
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D
```

## 如何做...

按照以下步骤使用 SciPy 解决受限线性最小化问题：

1.  将系统设置为 SciPy 可以识别的形式：

```py
A = np.array([
    [2, 1],   # 2*x0 + x1 <= 6
    [-1, -1]  # -x0 - x1 <= -4
])
b = np.array([6, -4])
x0_bounds = (-3, 14) # -3 <= x0 <= 14
x1_bounds = (2, 12)  # 2 <= x1 <= 12
c = np.array([1, 5])
```

1.  接下来，我们需要定义一个评估线性函数在*x*值处的例程，这是一个向量（NumPy 数组）：

```py
def func(x):
    return np.tensordot(c, x, axes=1)
```

1.  然后，我们创建一个新的图并添加一组`3d`轴，我们可以在上面绘制函数：

```py
fig = plt.figure()
ax = fig.add_subplot(projection="3d")
ax.set(xlabel="x0", ylabel="x1", zlabel="func")
ax.set_title("Values in Feasible region")
```

1.  接下来，我们创建一个覆盖问题区域的值网格，并在该区域上绘制函数的值：

```py
X0 = np.linspace(*x0_bounds)
X1 = np.linspace(*x1_bounds)
x0, x1 = np.meshgrid(X0, X1)
z = func([x0, x1])
ax.plot_surface(x0, x1, z, alpha=0.3)
```

1.  现在，我们在函数值平面上绘制与临界线`2*x0 + x1 == 6`对应的线，并在我们的图上绘制落在范围内的值：

```py
Y = (b[0] - A[0, 0]*X0) / A[0, 1]
I = np.logical_and(Y >= x1_bounds[0], Y <= x1_bounds[1])
ax.plot(X0[I], Y[I], func([X0[I], Y[I]]), "r", lw=1.5)
```

1.  我们重复这个绘图步骤，对第二条临界线`x0 + x1 == -4`：

```py
Y = (b[1] - A[1, 0]*X0) / A[1, 1]
I = np.logical_and(Y >= x1_bounds[0], Y <= x1_bounds[1])
ax.plot(X0[I], Y[I], func([X0[I], Y[I]]), "r", lw=1.5)
```

1.  接下来，我们着色位于两条临界线之间的区域，这对应于最小化问题的可行区域：

```py
B = np.tensordot(A, np.array([x0, x1]), axes=1)
II = np.logical_and(B[0, ...] <= b[0], B[1, ...] <= b[1]) 
ax.plot_trisurf(x0[II], x1[II], z[II], color="b", alpha=0.5)
```

函数值在可行区域上的图可以在以下图片中看到：

![](img/e58a70ae-06b2-4718-892d-8dde0fa89bea.png)图 9.1：突出显示了可行区域的线性函数值

正如我们所看到的，位于这个着色区域内的最小值发生在两条临界线的交点处。

1.  接下来，我们使用`linprog`来解决带有我们在*步骤 1*中创建的边界的受限最小化问题。我们在终端中打印出结果对象：

```py
res = optimize.linprog(c, A_ub=A, b_ub=b, bounds=
    (x0_bounds, x1_bounds))
print(res)
```

1.  最后，我们在可行区域上绘制最小函数值：

```py
ax.plot([res.x[0]], [res.x[1]], [res.fun], "k*")
```

更新后的图可以在以下图片中看到：

![](img/d18cd25f-8786-4d1e-a253-aebdc3d4bd92.png)图 9.2：在可行区域上绘制的最小值

在这里，我们可以看到`linprog`例程确实发现了最小值在两条临界线的交点处。

## 工作原理...

受限线性最小化问题在经济情况中很常见，您尝试在保持参数的其他方面的同时最小化成本。实际上，优化理论中的许多术语都反映了这一事实。解决这些问题的一个非常简单的算法称为**单纯形法**，它使用一系列数组操作来找到最小解。从几何上讲，这些操作代表着改变单纯形的不同顶点（我们在这里不定义），正是这一点赋予了算法其名称。

在我们继续之前，我们将简要概述单纯形法用于解决受限线性优化问题的过程。我们所面临的问题不是一个矩阵方程问题，而是一个矩阵不等式问题。我们可以通过引入**松弛变量**来解决这个问题，将不等式转化为等式。例如，通过引入松弛变量*s[1]*，可以将第一个约束不等式重写如下：

![](img/7d8c2f37-dc9d-4c8c-8718-96a71e709d6f.png)

只要*s[1]*不是负数，这就满足了所需的不等式。第二个约束不等式是大于或等于类型的不等式，我们必须首先将其更改为小于或等于类型。我们通过将所有项乘以-1 来实现这一点。这给了我们在配方中定义的矩阵`A`的第二行。引入第二个松弛变量*s[2]*后，我们得到了第二个方程：

![](img/6aa8a05b-274f-4d44-9eac-9101a8a40105.png)

从中，我们可以构建一个矩阵，其列包含两个参数变量*x[1]*和*x[2]*以及两个松弛变量*s[1]*和*s[2]*的系数。该矩阵的行代表两个边界方程和目标函数。现在可以使用对该矩阵进行初等行操作来解决这个方程组，以获得最小化目标函数的*x[1]*和*x[2]*的值。由于解决矩阵方程很容易且快速，这意味着我们可以快速高效地最小化线性函数。

幸运的是，我们不需要记住如何将不等式系统化简为线性方程组，因为诸如`linprog`之类的例程会为我们完成这一工作。我们只需将边界不等式提供为矩阵和向量对，包括每个系数，以及定义目标函数的单独向量。`linprog`例程负责制定和解决最小化问题。

实际上，单纯形法并不是`linprog`例程用于最小化函数的算法。相反，`linprog`使用内点算法，这更有效率。（可以通过提供`method`关键字参数并使用适当的方法名称将方法设置为`simplex`或`revised-simplex`。在打印的输出结果中，我们可以看到只用了五次迭代就达到了解决方案。）该例程返回的结果对象包含最小值发生的参数值存储在`x`属性中，该最小值存储在`fun`属性中，以及有关解决过程的各种其他信息。如果方法失败，那么`status`属性将包含一个数值代码，描述方法失败的原因。

在本文的*步骤 2*中，我们创建了一个代表此问题的目标函数的函数。该函数以单个数组作为输入，该数组包含应在其上评估函数的参数空间值。在这里，我们使用了 NumPy 的`tensordot`例程（带有`axes=1`）来评估系数向量*c*与每个输入*x*的点积。在这里我们必须非常小心，因为我们传递给函数的值在后续步骤中将是一个 2×50×50 的数组。普通的矩阵乘法（`np.dot`）在这种情况下不会给出我们所期望的 50×50 数组输出。

在*步骤 5*和*6*中，我们计算了临界线上的点，这些点具有以下方程：

![](img/112c73bc-cd66-44aa-941c-c2d03e5f4bbc.png)

然后我们计算了相应的*z*值，以便绘制在由目标函数定义的平面上的线。我们还需要“修剪”这些值，以便只包括在问题中指定范围内的值。

## 还有更多...

本文介绍了受约束的最小化问题以及如何使用 SciPy 解决它。然而，相同的方法也可以用于解决受约束的*最大化*问题。这是因为最大化和最小化在某种意义上是*对偶*的，即最大化函数*f*(*x*)等同于最小化函数*-f*(*x*)，然后取其负值。事实上，我们在本文中使用了这一事实，将第二个约束不等式从≥改为≤。

在这个示例中，我们解决了一个只有两个参数变量的问题，但是相同的方法将适用于涉及两个以上这样的变量的问题（除了绘图步骤）。我们只需要向每个数组添加更多的行和列，以考虑这增加的变量数量 - 这包括提供给例程的边界元组。在处理非常大量的变量时，例程也可以与稀疏矩阵一起使用，以获得额外的效率。

`linprog`例程的名称来自*线性规划*，用于描述这种类型的问题 - 找到满足一些矩阵不等式的*x*的值，受其他条件的限制。由于与矩阵理论和线性代数的理论有非常紧密的联系，因此在线性规划问题中有许多非常快速和高效的技术，这些技术在非线性情境中是不可用的。

# 最小化非线性函数

在上一个示例中，我们看到了如何最小化一个非常简单的线性函数。不幸的是，大多数函数都不是线性的，通常也没有我们希望的良好性质。对于这些非线性函数，我们不能使用为线性问题开发的快速算法，因此我们需要设计可以在这些更一般情况下使用的新方法。我们将使用的算法称为 Nelder-Mead 算法，这是一种健壮且通用的方法，用于找到函数的最小值，并且不依赖于函数的梯度。

在这个示例中，我们将学习如何使用 Nelder-Mead 单纯形法来最小化包含两个变量的非线性函数。

## 准备工作

在这个示例中，我们将使用导入为`np`的 NumPy 包，导入为`plt`的 Matplotlib `pyplot`模块，从`mpl_toolkits.mplot3d`导入的`Axes3D`类以启用 3D 绘图，以及 SciPy `optimize`模块：

```py
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D
from scipy import optimize
```

## 如何做...

以下步骤向您展示如何使用 Nelder-Mead 单纯形法找到一般非线性目标函数的最小值：

1.  定义我们将最小化的目标函数：

```py
def func(x):
    return ((x[0] - 0.5)**2 + (x[1] + 0.5)**2)*
       np.cos(0.5*x[0]*x[1])
```

1.  接下来，创建一个值网格，我们可以在上面绘制我们的目标函数：

```py
x_r = np.linspace(-1, 1)
y_r = np.linspace(-2, 2)
x, y = np.meshgrid(x_r, y_r)
```

1.  现在，我们在这个点网格上评估函数：

```py
z = func([x, y])
```

1.  接下来，我们创建一个带有`3d`轴对象的新图，并设置轴标签和标题：

```py
fig = plt.figure(tight_layout=True)
ax = fig.add_subplot(projection="3d")
ax.tick_params(axis="both", which="major", labelsize=9)
ax.set(xlabel="x", ylabel="y", zlabel="z")
ax.set_title("Objective function")
```

1.  现在，我们可以在我们刚刚创建的轴上将目标函数绘制为表面：

```py
ax.plot_surface(x, y, z, alpha=0.7)
```

1.  我们选择一个初始点，我们的最小化例程将从该点开始迭代，并在表面上绘制这个点：

```py
x0 = np.array([-0.5, 1.0])
ax.plot([x0[0]], [x0[1]], func(x0), "r*")
```

可以在以下图像中看到目标函数表面的绘图，以及初始点。在这里，我们可以看到最小值似乎出现在 x 轴上约 0.5，y 轴上约-0.5 的位置：

![](img/5ab26e54-d0c2-4be6-a1e6-2f3e71f1a30e.png)图 9.3：具有起始值的非线性目标函数

1.  现在，我们使用`optimize`包中的`minimize`例程来找到最小值，并打印它产生的`result`对象：

```py
result = optimize.minimize(func, x0, tol=1e-6, method=
    "Nelder-Mead")
print(result)
```

1.  最后，我们在目标函数表面上绘制`minimize`例程找到的最小值：

```py
ax.plot([result.x[0]], [result.x[1]], [result.fun], "r*")
```

包括`minimize`例程找到的最小点的目标函数的更新绘图可以在以下图像中看到：

![](img/5d9fcc80-cded-4642-a3b3-60f27b872b09.png)图 9.4：具有起始点和最小点的目标函数

## 它是如何工作的...

Nelder-Mead 单纯形法-不要与线性优化问题的单纯形法混淆-是一种简单的算法，用于找到非线性函数的最小值，即使目标函数没有已知的导数也可以工作。（这不适用于此示例中的函数；使用基于梯度的方法的唯一收益是收敛速度。）该方法通过比较单纯形的顶点处的目标函数值来工作，在二维空间中是一个三角形。具有最大函数值的顶点通过相反的边被“反射”，并执行适当的扩展或收缩，实际上将单纯形“下坡”移动。

SciPy`optimize`模块中的`minimize`例程是许多非线性函数最小化算法的入口点。在这个示例中，我们使用了 Nelder-Mead 单纯形算法，但还有许多其他可用的算法。其中许多算法需要对函数的梯度有所了解，该梯度可能会被算法自动计算。可以通过向`method`关键字参数提供适当的名称来使用该算法。

“最小化”例程返回的`result`对象包含有关求解器找到的解决方案的大量信息-或者如果发生错误，则未找到-。特别是，计算出的最小值发生的期望参数存储在结果的`x`属性中，而函数值存储在`fun`属性中。

“最小化”例程需要函数和`x0`的起始值。在这个示例中，我们还提供了一个容差值，最小值应该使用`tol`关键字参数计算。更改此值将修改计算解的准确度。

## 还有更多...

Nelder-Mead 算法是“无梯度”最小化算法的一个例子，因为它不需要任何关于目标函数的梯度（导数）的信息。有几种这样的算法，通常涉及在指定点评估目标函数，然后使用这些信息朝向最小值移动。一般来说，无梯度方法的收敛速度比梯度下降模型慢。但是，它们可以用于几乎任何目标函数，即使很难精确计算梯度或通过近似手段计算。

通常，优化单变量函数比多维情况更容易，并且在 SciPy`optimize`库中有其专用函数。`minimize_scalar`例程对单变量函数执行最小化，并且在这种情况下应该使用而不是`minimize`。

# 在优化中使用梯度下降方法

在上一个示例中，我们使用 Nelder-Mead 单纯形算法最小化包含两个变量的非线性函数。这是一种相当健壮的方法，即使对目标函数了解甚少也可以工作。然而，在许多情况下，我们对目标函数了解更多，这一事实使我们能够设计更快和更有效的最小化函数的算法。我们可以通过利用函数的梯度等属性来做到这一点。

多于一个变量的函数的*梯度*描述了函数在各个分量方向上的变化率。这是函数对每个变量的偏导数的向量。从这个梯度向量中，我们可以推断出函数在哪个方向上增长最快，反之亦然，从任意给定位置开始，函数在哪个方向上下降最快。这为*梯度下降*方法最小化函数提供了基础。算法非常简单：给定一个起始位置**x**，我们计算在这个**x**处的梯度以及梯度最快下降的相应方向，然后沿着那个方向迈出一小步。经过几次迭代，这将从起始位置移动到函数的最小值。

在这个示例中，我们将学习如何实现基于最陡下降算法的算法，以在有界区域内最小化目标函数。

## 准备工作

对于这个示例，我们需要导入 NumPy 包作为`np`，导入 Matplotlib 的`pyplot`模块作为`plt`，并从`mpl_toolkits.mplot3d`导入`Axes3D`对象：

```py
import numpy as np
import matplotlib.pyplot as plt
from mpl_toolkits.mplot3d import Axes3D
```

## 如何做...

在接下来的步骤中，我们将实现一个简单的梯度下降方法，以最小化具有已知梯度函数的目标函数（实际上我们将使用一个生成器函数，以便我们可以看到方法的工作方式）：

1.  我们将首先定义一个`descend`例程，它将执行我们的算法。函数声明如下：

```py
def descend(func, x0, grad, bounds, tol=1e-8, max_iter=100):
```

1.  接下来，我们需要实现这个例程。我们首先定义将在方法运行时保存迭代值的变量：

```py
xn = x0
xnm1 = np.inf
grad_xn = grad(x0)
```

1.  然后，我们开始我们的循环，这将运行迭代。我们立即检查是否在继续之前正在取得有意义的进展：

```py
for i in range(max_iter):
    if np.linalg.norm(xn - xnm1) < tol:
        break
```

1.  方向是梯度向量的负。我们计算一次并将其存储在`direction`变量中：

```py
direction = -grad_xn
```

1.  现在，我们更新先前和当前的值，分别为`xnm1`和`xn`，准备进行下一次迭代。这结束了`descend`例程的代码：

```py
xnm1 = xn
xn = xn + 0.2*direction
```

1.  现在，我们可以计算当前值的梯度并产生所有适当的值：

```py
grad_xn = grad(xn)
yield i, xn, func(xn), grad_xn
```

这结束了`descend`例程的定义。

1.  我们现在可以定义一个要最小化的样本目标函数：

```py
def func(x):
    return ((x[0] - 0.5)**2 + (x[1] + 0.5)**2)*np.cos(0.5*x[0]*x[1])
```

1.  接下来，我们创建一个网格，我们将评估并绘制目标函数：

```py
x_r = np.linspace(-1, 1)
y_r = np.linspace(-2, 2)
x, y = np.meshgrid(x_r, y_r)
```

1.  一旦网格创建完成，我们可以评估我们的函数并将结果存储在`z`变量中：

```py
z = func([x, y])
```

1.  接下来，我们创建目标函数的三维表面图：

```py
surf_fig = plt.figure(tight_layout=True)
surf_ax = surf_fig.add_subplot(projection="3d")
surf_ax.tick_params(axis="both", which="major", labelsize=9)
surf_ax.set(xlabel="x", ylabel="y", zlabel="z")
surf_ax.set_title("Objective function")
surf_ax.plot_surface(x, y, z, alpha=0.7)
```

1.  在我们开始最小化过程之前，我们需要定义一个初始点`x0`。我们在前一步中创建的目标函数图上绘制这一点：

```py
x0 = np.array([-0.8, 1.3])
surf_ax.plot([x0[0]], [x0[1]], func(x0), "r*")
```

目标函数的表面图，以及初始值，可以在以下图像中看到：

![](img/5c4283c9-96a3-4818-8b34-16b400f57003.png)图 9.5：具有初始位置的目标函数表面

1.  我们的`descend`例程需要一个评估目标函数梯度的函数，因此我们将定义一个：

```py
def grad(x):
    c1 = x[0]**2 - x[0] + x[1]**2 + x[1] + 0.5
    cos_t = np.cos(0.5*x[0]*x[1])
    sin_t = np.sin(0.5*x[0]*x[1])
    return np.array([
        (2*x[0]-1)*cos_t - 0.5*x[1]*c1*sin_t,
        (2*x[1]+1)*cos_t - 0.5*x[0]*c1*sin_t
    ])
```

1.  我们将在轮廓图上绘制迭代，因此我们设置如下：

```py
cont_fig, cont_ax = plt.subplots()
cont_ax.set(xlabel="x", ylabel="y")
cont_ax.set_title("Contour plot with iterates")
cont_ax.contour(x, y, z, levels=30)
```

1.  现在，我们创建一个变量，将*x*和*y*方向的边界作为元组的元组保存。这些是*步骤 10*中`linspace`调用中的相同边界：

```py
bounds = ((-1, 1), (-2, 2))
```

1.  我们现在可以使用`for`循环驱动`descend`生成器来产生每个迭代，并将步骤添加到轮廓图中：

```py
xnm1 = x0
for i, xn, fxn, grad_xn in descend(func, x0, grad, bounds):
    cont_ax.plot([xnm1[0], xn[0]], [xnm1[1], xn[1]], "k*--")
    xnm1, grad_xnm1 = xn, grad_xn
```

1.  循环完成后，我们将最终值打印到终端：

```py
print(f"iterations={i}")
print(f"min val at {xn}")
print(f"min func value = {fxn}")
```

前面打印语句的输出如下：

```py
iterations=37
min val at [ 0.49999999 -0.49999999]
min func value = 2.1287163880894953e-16
```

在这里，我们可以看到我们的例程使用了 37 次迭代来找到大约在(0.5，-0.5)处的最小值，这是正确的。

可以在以下图像中看到带有其迭代的轮廓图：

![](img/2925d695-1772-420f-b35e-85cac413956f.png)图 9.6：梯度下降迭代到最小值的目标函数轮廓图

在这里，我们可以看到每次迭代的方向 - 由虚线表示 - 是目标函数下降最快的方向。最终迭代位于目标函数“碗”的中心，这是最小值出现的地方。

## 它是如何工作的...

这个配方的核心是`descend`例程。在这个例程中定义的过程是梯度下降法的一个非常简单的实现。在给定点计算梯度由`grad`参数处理，然后用`direction = -grad`推断迭代的旅行方向。我们将这个方向乘以一个固定的比例因子（有时称为**学习率**）0.2 的值来获得缩放步骤，然后通过添加`0.2*direction`到当前位置来进行这一步。

这个配方的解决方案需要 37 次迭代才能收敛，这比*最小化非线性函数*配方中的 Nelder-Mead 单纯形算法需要 58 次迭代要好一点。（这并不是一个完美的比较，因为我们改变了这个配方的起始位置。）这种性能在很大程度上取决于我们选择的步长。在这种情况下，我们将最大步长固定为方向向量的 0.2 倍。这使得算法简单，但并不特别高效。

在这个配方中，我们选择将算法实现为生成器函数，以便我们可以看到每一步的输出，并在迭代中绘制在等高线图上。在实践中，我们可能不想这样做，而是在迭代完成后返回计算出的最小值。为此，我们可以简单地删除`yield`语句，并在函数的最后，即主函数的缩进位置（即不在循环内），用`return xn`替换它。如果你想防止不收敛，你可以使用`for`循环的`else`特性来捕捉循环完成的情况，因为它已经到达了迭代器的末尾而没有触发`break`关键字。这个`else`块可以引发一个异常，以表明算法未能稳定到一个解决方案。在这个配方中，我们用来结束迭代的条件并不能保证该方法已经达到了最小值，但这通常是这种情况。

## 还有更多...

在实践中，你通常不会为自己实现梯度下降算法，而是使用来自库（如 SciPy `optimize`模块）的通用例程。我们可以使用与前一个配方中使用的相同的`minimize`例程来执行多种不同算法的最小化，包括几种梯度下降算法。这些实现可能比这样的自定义实现具有更高的性能和更强大的鲁棒性。

我们在这个配方中使用的梯度下降方法是一个非常天真的实现，可以通过允许例程在每一步选择步长来大大改进。（允许选择自己步长的方法有时被称为自适应方法。）这种改进的困难部分是选择在这个方向上采取的步长大小。为此，我们需要考虑单变量函数，它由以下方程给出：

![](img/be48ec70-e6d7-44a1-968a-30847a25765e.png)

在这里，**x***[n]*表示当前点，**d**[*n*]表示当前方向，*t*是一个参数。为了简单起见，我们可以使用 SciPy `optimize`模块中的`minimize_scalar`最小化例程来处理标量值函数。不幸的是，这并不像简单地传入这个辅助函数并找到最小值那样简单。我们必须限定*t*的可能值，以便计算出的最小化点**x**[*n*]+ *t***d**[*n*]位于我们感兴趣的区域内。

要理解我们如何限制*t*的值，我们必须首先从几何上看这个构造。我们引入的辅助函数在给定方向上沿着一条直线评估目标函数。我们可以将其想象为通过当前**x***[n]*点在**d**[*n*]方向上穿过的表面的单个横截面。算法的下一步是找到最小化沿着这条直线的目标函数值的步长*t*，这是一个标量函数，要最小化它要容易得多。然后边界应该是*t*值的范围，在这个范围内，这条直线位于由*x*和*y*边界值定义的矩形内。我们确定这条直线穿过这些*x*和*y*边界线的四个值，其中两个将是负值，另外两个将是正值。（这是因为当前点必须位于矩形内。）我们取两个正值中的最小值和两个负值中的最大值，并将这些边界传递给标量最小化例程。这是通过以下代码实现的：

```py
alphas = np.array([
        (bounds[0][0] - xn[0]) / direction[0], # x lower
        (bounds[1][0] - xn[1]) / direction[1], # y lower
        (bounds[0][1] - xn[0]) / direction[0], # x upper
        (bounds[1][1] - xn[1]) / direction[1] # y upper
])

alpha_max = alphas[alphas >= 0].min()
alpha_min = alphas[alphas < 0].max()
result = minimize_scalar(lambda t: func(xn + t*direction), 
        method="bounded", bounds=(alpha_min, alpha_max))
amount = result.x
```

一旦选择了步长，唯一剩下的步骤就是更新当前的`xn`值，如下所示：

```py
xn = xn + amount * direction
```

使用这种自适应步长会增加例程的复杂性，但性能大大提高。使用这个修改后的例程，该方法在只进行了三次迭代后就收敛了，远少于本食谱中使用的朴素代码（37 次迭代）或上一个食谱中使用的 Nelder-Mead 单纯形算法（58 次迭代）。通过在梯度函数中提供更多信息，减少了迭代次数，这正是我们预期的。

我们创建了一个函数，返回给定点处函数的梯度。我们在开始之前手动计算了这个梯度，这并不总是容易或甚至可能的。相反，更常见的是用数值计算的梯度替换这里使用的“解析”梯度，这个数值梯度是使用有限差分或类似算法估计的。这对性能和准确性有影响，就像所有近似一样，但鉴于梯度下降方法提供的收敛速度的提高，这些问题通常是次要的。

梯度下降类型的算法在机器学习应用中特别受欢迎。大多数流行的 Python 机器学习库，包括 PyTorch、TensorFlow 和 Theano，都提供了用于自动计算数据数组梯度的实用工具。这使得梯度下降方法可以在后台使用以提高性能。

梯度下降方法的一种流行变体是**随机梯度下降**，其中梯度是通过随机抽样来估计的，而不是使用整个数据集。这可以显著减少方法的计算负担，但会以更慢的收敛速度为代价，特别是对于高维问题，比如在机器学习应用中常见的问题。随机梯度下降方法通常与反向传播结合，构成了机器学习应用中训练人工神经网络的基础。

基本随机梯度下降算法有几种扩展。例如，动量算法将先前的增量纳入到下一个增量的计算中。另一个例子是自适应梯度算法，它纳入每个参数的学习率，以改善涉及大量稀疏参数的问题的收敛速度。

# 使用最小二乘法拟合数据曲线

最小二乘法是一种强大的技术，用于从相对较小的潜在函数族中找到最能描述特定数据集的函数。这种技术在统计学中特别常见。例如，最小二乘法用于线性回归问题-在这里，潜在函数族是所有线性函数的集合。通常，我们尝试拟合的这个函数族具有相对较少的参数，可以调整以解决问题。

最小二乘法的思想相对简单。对于每个数据点，我们计算残差的平方-点的值与给定函数的期望值之间的差异-并尝试使这些残差的平方和尽可能小（因此最小二乘法）。

在这个配方中，我们将学习如何使用最小二乘法来拟合一组样本数据的曲线。

## 做好准备

对于这个配方，我们将需要导入 NumPy 包，通常作为`np`，并导入 Matplotlib `pyplot`模块作为`plt`：

```py
import numpy as np
import matplotlib.pyplot as plt
```

我们还需要从 NumPy `random`模块中导入默认随机数生成器的实例，如下所示：

```py
from numy.random import default_rng
rng = default_rng(12345)
```

最后，我们需要从 SciPy `optimize`模块中的`curve_fit`例程：

```py
from scipy.optimize import curve_fit
```

## 如何做...

以下步骤向您展示如何使用`curve_fit`例程来拟合一组数据的曲线：

1.  第一步是创建样本数据：

```py
SIZE = 100
x_data = rng.uniform(-3.0, 3.0, size=SIZE)
noise = rng.normal(0.0, 0.8, size=SIZE)
y_data = 2.0*x_data**2 - 4*x_data + noise
```

1.  接下来，我们生成数据的散点图，以查看是否可以识别数据中的潜在趋势：

```py
fig, ax = plt.subplots()
ax.scatter(x_data, y_data)
ax.set(xlabel="x", ylabel="y", title="Scatter plot of sample data")
```

我们生成的散点图可以在下面的图像中看到。在这里，我们可以看到数据显然不遇线性趋势（直线）。由于我们知道趋势是多项式，我们的下一个猜测将是二次趋势。这就是我们在这里使用的：

![](img/3ceb7823-e247-404c-a2d3-f2fe41c0478c.png)图 9.7：样本数据的散点图。我们可以看到数据不遵循线性趋势

1.  接下来，我们创建一个代表我们希望拟合的模型的函数：

```py
def func(x, a, b, c):
    return a*x**2 + b*x + c
```

1.  现在，我们可以使用`curve_fit`例程将模型函数拟合到样本数据中：

```py
coeffs, _ = curve_fit(func, x_data, y_data)
print(coeffs)
# [ 1.99611157 -3.97522213 0.04546998]
```

1.  最后，我们在散点图上绘制最佳拟合曲线，以评估拟合曲线描述数据的效果如何：

```py
x = np.linspace(-3.0, 3.0, SIZE)
y = func(x, coeffs[0], coeffs[1], coeffs[2])
ax.plot(x, y, "k--")
```

更新后的散点图可以在下面的图像中看到：

![](img/dd4d4a24-007e-44fe-be30-d65cbd0cc903.png)图 9.8：散点图，使用最小二乘法找到的最佳拟合曲线

在这里，我们可以看到我们找到的曲线相当合理地拟合了数据。

## 它是如何工作的...

`curve_fit`例程执行最小二乘拟合，将模型的曲线拟合到样本数据中。在实践中，这相当于最小化以下目标函数：

![](img/ba94af51-b092-4000-933f-7e50b33b54ea.png)

这里，配对（*x[i]*，*y[i]*）是样本数据中的点。在这种情况下，我们正在优化一个三维参数空间，每个参数都有一个维度。该例程返回估计的系数-参数空间中的点，其中目标函数被最小化-和包含拟合的协方差矩阵的估计的第二个变量。在这个配方中，我们忽略了这一点。

从`curve_fit`例程返回的估计协方差矩阵可以用于为估计的参数提供置信区间。这是通过取对角线元素的平方根除以样本大小（在这个配方中为 100）来完成的。这给出了估计的标准误差，当乘以与置信度对应的适当值时，给出了置信区间的大小。（我们在第六章中讨论了置信区间，*使用数据和统计*。）

您可能已经注意到，`curve_fit`例程估计的参数接近，但并非完全等于我们用于定义*步骤 1*中的样本数据的参数。这些参数并非完全相等的原因是我们向数据中添加了正态分布的噪声。在这个示例中，我们知道数据的基本结构是二次的 - 也就是二次多项式 - 而不是其他更神秘的函数。实际上，我们不太可能知道数据的基本结构，这就是我们向样本添加噪声的原因。

## 还有更多...

SciPy `optimize`模块中还有另一个用于执行最小二乘拟合的例程，名为`least_squares`。这个例程的签名略微不太直观，但确实返回了一个包含有关优化过程的更多信息的结果对象。然而，这个例程的设置方式可能更类似于我们在*它是如何工作的...*部分中构造基础数学问题的方式。要使用这个例程，我们定义目标函数如下：

```py
def func(params, x, y):
    return y - (params[0]*x**2 + params[1]*x + params[2])
```

我们将这个函数与参数空间中的起始估计`x0`一起传递，例如`(1, 0, 0)`。目标函数的额外参数`func`可以使用`args`关键字参数传递；例如，我们可以使用`args=(x_data, y_data)`。这些参数被传递到目标函数的`x`和`y`参数中。总之，我们可以使用以下调用`least_squares`来估计参数：

```py
results = least_squares(func, [1, 0, 0], args=(x_data, y_data))
```

从`least_squares`例程返回的`results`对象实际上与本章中描述的其他优化例程返回的对象相同。它包含诸如使用的迭代次数、过程是否成功、详细的错误消息、参数值以及最小值处的目标函数值等细节。

# 分析简单的两人游戏

博弈论是数学的一个分支，涉及决策和策略分析。它在经济学、生物学和行为科学中有应用。许多看似复杂的情况可以简化为一个相对简单的数学游戏，可以以系统的方式进行分析，找到“最优”解决方案。

博弈论中的一个经典问题是*囚徒困境*，其原始形式如下：两个同谋被抓住，必须决定是保持沉默还是对对方作证。如果两人都保持沉默，他们都要服刑 1 年；如果一个作证而另一个不作证，作证者将获释，而另一个将服刑 3 年；如果两人都相互作证，他们都将服刑 2 年。每个同谋应该怎么做？事实证明，在对对方有任何合理的不信任的情况下，每个同谋可以做出的最佳选择是作证。采用这种策略，他们将不会服刑或最多服刑 2 年。

由于这本书是关于 Python 的，我们将使用这个经典问题的变体来说明这个问题的普遍性。考虑以下问题：你和你的同事必须为客户编写一些代码。你认为你可以用 Python 更快地编写代码，但你的同事认为他们可以用 C 更快地编写代码。问题是，你应该选择哪种语言来进行项目？

你认为你可以用 Python 代码比 C 代码快 4 倍，所以你用速度 1 写 C，用速度 4 写 Python。你的同事说他们可以比 Python 稍微更快地写 C，所以他们用速度 3 写 C，用速度 2 写 Python。如果你们两个都同意一种语言，那么你们按照你预测的速度编写代码，但如果你们意见不一致，那么更快的程序员的生产力会降低 1。我们可以总结如下：

| **同事/你** | **C** | **Python** |
| --- | --- | --- |
| C | 3 / 1 | 3 / 2 |
| Python | 2 / 1 | 2 / 4 |

在这个配方中，我们将学习如何在 Python 中构建一个对象来表示这个简单的双人游戏，然后对这个游戏的结果进行一些基本分析。

## 准备工作

对于这个配方，我们需要导入 NumPy 包为`np`，导入 Nashpy 包为`nash`：

```py
import numpy as np
import nashpy as nash
```

## 如何做...

以下步骤向您展示了如何使用 Nashpy 创建和执行一些简单的双人游戏分析：

1.  首先，我们需要创建矩阵，用于保存每个玩家（在这个例子中是您和您的同事）的支付信息。

```py
you = np.array([[1, 3], [1, 4]])
colleague = np.array([[3, 2], [2, 2]])
```

1.  接下来，我们创建一个`Game`对象，它保存了由这些支付矩阵表示的游戏：

```py
dilemma = nash.Game(you, colleague)
```

1.  我们使用索引表示法计算给定选择的效用：

```py
print(dilemma[[1, 0], [1, 0]])  # [1 3]
print(dilemma[[1, 0], [0, 1]])  # [3 2]
print(dilemma[[0, 1], [1, 0]])  # [1 2]
print(dilemma[[0, 1], [0, 1]])  # [4 2]
```

1.  我们还可以根据做出特定选择的概率计算预期效用：

```py
print(dilemma[[0.1, 0.9], [0.5, 0.5]]) # [2.45 2.05]
```

## 它是如何工作的...

在这个配方中，我们构建了一个代表非常简单的双人战略游戏的 Python 对象。 这里的想法是有两个“玩家”需要做决定，每个玩家的选择组合都会给出一个特定的支付值。 我们的目标是找到每个玩家可以做出的最佳选择。 假设玩家同时进行一次移动，即没有人知道对方的选择。 每个玩家都有一个确定他们所做选择的策略。

在*步骤 1*中，我们创建两个矩阵 - 每个玩家一个 - 分配给每个选择组合的支付值。 这两个矩阵由 Nashpy 的`Game`类包装，它提供了一个方便且直观（从博弈论的角度来看）的接口来处理游戏。 通过使用索引表示法传递选择，我们可以快速计算给定选择组合的效用。

我们还可以根据一种策略提供预期效用的计算，其中选择是根据某种概率分布随机选择的。 语法与先前描述的确定性情况相同，只是我们为每个选择提供了一个概率向量。 我们根据您选择 Python 的概率计算预期效用为 90％，而您的同事选择 Python 的概率为 50％。 预期速度分别为 2.45 和 2.05。

## 还有更多...

在 Python 中，还有一种计算博弈论的替代方法。 Gambit 项目是一组用于博弈论计算的工具，具有 Python 接口（[`www.gambit-project.org/`](http://www.gambit-project.org/)）。 这是一个成熟的项目，建立在 C 库周围，并提供比 Nashpy 更高的性能。

# 计算纳什均衡

*纳什均衡*是一个双人战略游戏 - 类似于我们在*分析简单的双人游戏*配方中看到的游戏 - 代表每个玩家都看到“最佳可能”结果的“稳态”。 但是，这并不意味着与纳什均衡相关联的结果是最好的。 纳什均衡比这更微妙。 纳什均衡的非正式定义如下：在其中没有个别玩家可以改善他们的结果的行动配置，假设所有其他玩家都遵守该配置。

我们将探讨纳什均衡的概念，使用经典的猜拳游戏。 规则如下。 每个玩家可以选择以下选项之一：石头，纸或剪刀。 石头打败剪刀，但输给纸； 纸打败石头，但输给剪刀； 剪刀打败纸，但输给石头。 如果两名玩家做出相同选择的游戏是平局。 在数值上，我们用+1 表示赢，-1 表示输，0 表示平局。 从中，我们可以构建一个双人游戏，并计算该游戏的纳什均衡。

在这个配方中，我们将计算猜拳游戏的纳什均衡。

## 准备工作

对于这个配方，我们需要导入 NumPy 包为`np`，导入 Nashpy 包为`nash`：

```py
import numpy as np
import nashpy as nash
```

## 如何做...

以下步骤向您展示了如何计算简单的两人游戏的纳什均衡：

1.  首先，我们需要为每个玩家创建一个收益矩阵。我们将从第一个玩家开始：

```py
rps_p1 = np.array([
    [ 0, -1,  1],  # rock payoff
    [ 1,  0, -1],   # paper payoff
    [-1,  1,  0]   # scissors payoff
])
```

1.  第二个玩家的收益矩阵是`rps_p1`的转置：

```py
rps_p2 = rps_p1.transpose()
```

1.  接下来，我们创建`Game`对象来表示游戏：

```py
rock_paper_scissors = nash.Game(rps_p1, rps_p2)
```

1.  我们使用支持枚举算法计算游戏的纳什均衡：

```py
equilibria = rock_paper_scissors.support_enumeration()
```

1.  我们遍历均衡，并打印每个玩家的策略：

```py
for p1, p2 in equilibria:
    print("Player 1", p1)
    print("Player 2", p2)
```

这些打印语句的输出如下：

```py
Player 1 [0.33333333 0.33333333 0.33333333]
Player 2 [0.33333333 0.33333333 0.33333333]
```

## 它是如何工作的...

纳什均衡在博弈论中非常重要，因为它们允许我们分析战略游戏的结果，并确定有利的位置。它们最早由约翰·F·纳什在 1950 年描述，并在现代博弈论中发挥了重要作用。一个两人游戏可能有许多纳什均衡，但任何有限的两人游戏必须至少有一个。问题在于找到给定游戏的所有可能的纳什均衡。

在这个示例中，我们使用了支持枚举，它有效地枚举了所有可能的策略，并筛选出了那些是纳什均衡的策略。在这个示例中，支持枚举算法只找到了一个纳什均衡，这是一个混合策略。这意味着唯一没有改进的策略是随机选择其中一个选项，每个选项的概率为 1/3。这对于任何玩过石头剪刀布的人来说都不足为奇，因为无论我们做出什么选择，我们的对手都有 1/3 的机会（随机）选择打败我们选择的动作。同样，我们有 1/3 的机会平局或赢得比赛，因此我们在所有这些可能性中的期望值如下：

![](img/e8194320-0a11-4aaa-b6ff-58939c4505f7.png)

在不知道我们的对手会选择哪一个选项的情况下，没有办法改进这个预期结果。

## 还有更多...

Nashpy 软件包还提供了其他计算纳什均衡的算法。具体来说，`vertex_enumeration`方法在`Game`对象上使用*顶点枚举*算法，而`lemke_howson_enumeration`方法使用*Lemke Howson*算法。这些替代算法具有不同的特性，对于某些问题可能更有效。

## 另请参阅

Nashpy 软件包的文档包含了有关所涉及的算法和博弈论的更详细信息。其中包括了一些关于博弈论的文本参考。这些文档可以在[`nashpy.readthedocs.io/en/latest/`](https://nashpy.readthedocs.io/en/latest/)找到。

# 进一步阅读

通常情况下，*Numerical Recipes*书是数值算法的良好来源。第十章，*其他主题*，涉及函数的最大化和最小化：

+   *Press, W.H., Teukolsky, S.A., Vetterling, W.T., and Flannery, B.P., 2017\. Numerical recipes: the art of scientific computing. 3rd ed. Cambridge: Cambridge University Press*。

有关优化的更具体信息可以在以下书籍中找到：

+   *Boyd, S.P. and Vandenberghe, L., 2018\. Convex optimization**. Cambridge: Cambridge University Press*。

+   *Griva, I., Nash, S., and Sofer, A., 2009\. Linear and nonlinear optimization.**2nd ed. Philadelphia: Society for Industrial and Applied Mathematics*。

最后，以下书籍是博弈论的良好入门：

+   *Osborne, M.J., 2017\. An introduction to game theory**. Oxford: Oxford University Press*。
