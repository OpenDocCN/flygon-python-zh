# 第四章：微积分和微分方程

在本章中，我们将讨论与微积分相关的各种主题。微积分是涉及微分和积分过程的数学分支。从几何上讲，函数的导数代表函数曲线的梯度，函数的积分代表函数曲线下方的面积。当然，这些特征只在某些情况下成立，但它们为本章提供了一个合理的基础。

我们首先来看一下简单函数类的微积分：多项式。在第一个示例中，我们创建一个表示多项式的类，并定义不同 iate 和积分多项式的方法。多项式很方便，因为多项式的导数或积分再次是多项式。然后，我们使用 SymPy 包对更一般的函数进行符号微分和积分。之后，我们看到使用 SciPy 包解方程的方法。接下来，我们将注意力转向数值积分（求积）和解微分方程。我们使用 SciPy 包来解常微分方程和常微分方程组，然后使用有限差分方案来解简单的偏微分方程。最后，我们使用快速傅里叶变换来处理嘈杂的信号并滤除噪音。

在本章中，我们将涵盖以下示例：

+   使用多项式和微积分

+   使用 SymPy 进行符号微分和积分

+   解方程

+   使用 SciPy 进行数值积分

+   使用数值方法解简单的微分方程

+   解微分方程组

+   使用数值方法解偏微分方程

+   使用离散傅里叶变换进行信号处理

# 技术要求

除了科学 Python 包 NumPy 和 SciPy 之外，我们还需要 SymPy 包。可以使用您喜欢的软件包管理器（如`pip`）来安装它：

```py
          python3.8 -m pip install sympy

```

本章的代码可以在 GitHub 存储库的`Chapter 03`文件夹中找到，网址为[`github.com/PacktPublishing/Applying-Math-with-Python/tree/master/Chapter%2003`](https://github.com/PacktPublishing/Applying-Math-with-Python/tree/master/Chapter%2003)。

查看以下视频以查看代码的实际操作：[`bit.ly/32HuH4X`](https://bit.ly/32HuH4X)。

# 使用多项式和微积分

多项式是数学中最简单的函数之一，定义为一个求和：

![](img/21d2a94c-cfae-48b2-92cd-b345dd6fe425.png)

*x*代表要替换的占位符，*a[i]*是一个数字。由于多项式很简单，它们提供了一个很好的方式来简要介绍微积分。微积分涉及函数的*微分*和*积分*。积分大致上是*反微分*，因为先积分然后微分会得到原始函数。

在本示例中，我们将定义一个表示多项式的简单类，并为该类编写方法以执行微分和积分。

## 准备工作

从几何上讲，通过*微分*得到的*导数*是函数的*梯度*，通过*积分*得到的*积分*是函数曲线与*x*轴之间的面积，考虑到曲线是在轴的上方还是下方。在实践中，微分和积分是通过一组规则和特别适用于多项式的标准结果来进行符号化处理。

本示例不需要额外的软件包。

## 如何做...

以下步骤描述了如何创建表示多项式的类，并为该类实现微分和积分方法：

1.  让我们首先定义一个简单的类来表示多项式：

```py
class Polynomial:
    """Basic polynomial class"""

    def __init__(self, coeffs):
        self.coeffs = coeffs

    def __repr__(self):
        return f"Polynomial({repr(self.coeffs)})"

    def __call__(self, x):
        return sum(coeff*x**i for i, coeff 
                in enumerate(self.coeffs))
```

1.  现在我们已经为多项式定义了一个基本类，我们可以继续实现这个`Polynomial`类的微分和积分操作，以说明这些操作如何改变多项式。我们从微分开始。我们通过将当前系数列表中的每个元素（不包括第一个元素）相乘来生成新的系数。我们使用这个新的系数列表来创建一个新的`Polynomial`实例，然后返回：

```py
    def differentiate(self):
        """Differentiate the polynomial and return the derivative"""
        coeffs = [i*c for i, c in enumerate(self.coeffs[1:], start=1)]
        return Polynomial(coeffs)
```

1.  要实现积分方法，我们需要创建一个包含由参数给出的新常数（转换为浮点数以保持一致性）的新系数列表。然后我们将旧系数除以它们在列表中的新位置，加到这个系数列表中：

```py
    def integrate(self, constant=0):
        """Integrate the polynomial, returning the integral"""
        coeffs = [float(constant)] 
        coeffs += [c/i for i, c in enumerate(self.coeffs, start=1)]
        return Polynomial(coeffs)
```

1.  最后，为了确保这些方法按预期工作，我们应该用一个简单的案例测试这两种方法。我们可以使用一个非常简单的多项式来检查，比如*x² - 2x + 1*：

```py
p = Polynomial([1, -2, 1])
p.differentiate()
# Polynomial([-2, 2])
p.integrate(constant=1)
# Polynomial([1.0, 1.0, -1.0, 0.3333333333])
```

## 工作原理...

多项式为我们提供了一个对微积分基本操作的简单介绍，但对于其他一般类的函数来说，构建 Python 类并不那么容易。也就是说，多项式非常有用，因为它们被很好地理解，也许更重要的是，对于多项式的微积分非常容易。对于变量*x*的幂，微分的规则是乘以幂并减少 1，因此*x^n*变为*nx^(n-1)*。

积分更复杂，因为函数的积分不是唯一的。我们可以给积分加上任意常数并得到第二个积分。对于变量*x*的幂，积分的规则是将幂增加 1 并除以新的幂，因此*x^n*变为*x^(n+1)/*(*n+1*)，所以要对多项式进行积分，我们将每个*x*的幂增加 1，并将相应的系数除以新的幂。

我们在这个食谱中定义的`Polynomial`类相当简单，但代表了核心思想。多项式由其系数唯一确定，我们可以将其存储为一组数值值的列表。微分和积分是我们可以对这个系数列表执行的操作。我们包括一个简单的`__repr__`方法来帮助显示`Polynomial`对象，以及一个`__call__`方法来促进在特定数值上的评估。这主要是为了演示多项式的评估方式。

多项式对于解决涉及评估计算昂贵函数的某些问题非常有用。对于这样的问题，我们有时可以使用某种多项式插值，其中我们将一个多项式“拟合”到另一个函数，然后利用多项式的性质来帮助解决原始问题。评估多项式比原始函数要“便宜”得多，因此这可能会大大提高速度。这通常是以一些精度为代价的。例如，辛普森法则用二次多项式逼近曲线下的面积，这些多项式是由三个连续网格点定义的间隔内的。每个二次多项式下面的面积可以通过积分轻松计算。

## 还有更多...

多项式在计算编程中扮演的角色远不止是展示微分和积分的效果。因此，NumPy 包中提供了一个更丰富的`Polynomial`类，`numpy.polynomial`。NumPy 的`Polynomial`类和各种派生子类在各种数值问题中都很有用，并支持算术运算以及其他方法。特别是，有用于将多项式拟合到数据集合的方法。

NumPy 还提供了从`Polynomial`派生的类，表示各种特殊类型的多项式。例如，`Legendre`类表示一种特定的多项式系统，称为**Legendre**多项式。Legendre 多项式是为满足*-1 ≤ x ≤ 1*的*x*定义的，并且形成一个正交系统，这对于诸如数值积分和**有限元方法**解决偏微分方程等应用非常重要。Legendre 多项式使用递归关系定义。我们定义

![](img/52bf688b-c298-4854-8933-fe2424d18981.png)

对于每个*n ≥ 2*，我们定义第*n*个 Legendre 多项式满足递推关系，

![](img/70211164-f54e-4915-8a81-c7e8dfb8c69b.png)

还有一些所谓的*正交（系统的）多项式*，包括*Laguerre*多项式*，*Chebyshev 多项式*和*Hermite 多项式*。

## 参见

微积分在数学文本中有很好的文档记录，有许多教科书涵盖了从基本方法到深层理论的内容。正交多项式系统在数值分析文本中也有很好的文档记录。

# 使用 SymPy 进行符号微分和积分

在某些时候，您可能需要区分一个不是简单多项式的函数，并且可能需要以某种自动化的方式来做这件事，例如，如果您正在编写教育软件。Python 科学堆栈包括一个名为 SymPy 的软件包，它允许我们在 Python 中创建和操作符号数学表达式。特别是，SymPy 可以执行符号函数的微分和积分，就像数学家一样。

在这个示例中，我们将创建一个符号函数，然后使用 SymPy 库对这个函数进行微分和积分。

## 准备工作

与其他一些科学 Python 软件包不同，文献中似乎没有一个标准的别名来导入 SymPy。相反，文档在几个地方使用了星号导入，这与 PEP8 风格指南不一致。这可能是为了使数学表达更自然。我们将简单地按照其名称`sympy`导入模块，以避免与`scipy`软件包的标准缩写`sp`混淆（这也是`sympy`的自然选择）：

```py
import sympy
```

在这个示例中，我们将定义一个表示函数的符号表达式

![](img/d45795ee-95b9-4f79-85b3-b118aafc7d48.png)

## 如何做...

使用 SymPy 软件包进行符号微分和积分（就像您手工操作一样）非常容易。按照以下步骤来看看它是如何完成的：

1.  一旦导入了 SymPy，我们就定义将出现在我们的表达式中的符号。这是一个没有特定值的 Python 对象，就像数学变量一样，但可以在公式和表达式中表示许多不同的值。对于这个示例，我们只需要定义一个符号用于*x*，因为除此之外我们只需要常数（文字）符号和函数。我们使用`sympy`中的`symbols`例程来定义一个新符号。为了保持符号简单，我们将这个新符号命名为`x`：

```py
x = sympy.symbols('x')
```

1.  使用`symbols`函数定义的符号支持所有算术运算，因此我们可以直接使用我们刚刚定义的符号`x`构造表达式：

```py
f = (x**2 - 2*x)*sympy.exp(3 - x)
```

1.  现在我们可以使用 SymPy 的符号微积分能力来计算`f`的导数，即对`f`进行微分。我们使用`sympy`中的`diff`例程来完成这个操作，它对指定的符号进行符号表达式微分，并返回导数的表达式。这通常不是以最简形式表达的，因此我们使用`sympy.simplify`例程来简化结果：

```py
fp = sympy.simplify(sympy.diff(f))  # (x*(2 - x) + 2*x - 2)
                                                       *exp(3 - x)
```

1.  我们可以通过以下方式检查使用 SymPy 进行符号微分的结果是否正确，与手工计算的导数相比，定义为 SymPy 表达式：

```py
fp2 = (2*x - 2)*sympy.exp(3 - x) - (x**2 - 2*x)*sympy.exp(3 - x)
```

1.  SymPy 相等性测试两个表达式是否相等，但不测试它们是否在符号上等价。因此，我们必须首先简化我们希望测试的两个语句的差异，并测试是否等于`0`：

```py
sympy.simplify(fp2 - fp) == 0  # True
```

1.  我们可以使用 SymPy 通过`integrate`函数对函数`f`进行积分。还可以通过将其作为第二个可选参数提供来提供要执行积分的符号：

```py
F = sympy.integrate(f, x)  # -x**2*exp(3 - x)
```

## 它是如何工作的...

SymPy 定义了表示某些类型表达式的各种类。例如，由`Symbol`类表示的符号是*原子表达式*的例子。表达式是以与 Python 从源代码构建抽象语法树的方式构建起来的。然后可以使用方法和标准算术运算来操作这些表达式对象。

SymPy 还定义了可以在`Symbol`对象上操作以创建符号表达式的标准数学函数。最重要的特性是能够执行符号微积分 - 而不是我们在本章剩余部分中探索的数值微积分 - 并给出对微积分问题的精确（有时称为*解析*）解决方案。

SymPy 软件包中的`diff`例程对这些符号表达式进行微分。这个例程的结果通常不是最简形式，这就是为什么我们在配方中使用`简化`例程来简化导数的原因。`integrate`例程用给定的符号对`scipy`表达式进行符号积分。（`diff`例程还接受一个符号参数，用于指定微分的符号。）这将返回一个其导数为原始表达式的表达式。这个例程不会添加积分常数，这在手工积分时是一个好的做法。

## 还有更多...

SymPy 可以做的远不止简单的代数和微积分。它有各种数学领域的子模块，如数论、几何和其他离散数学（如组合数学）。

SymPy 表达式（和函数）可以构建成 Python 函数，可以应用于 NumPy 数组。这是使用`sympy.utilities`模块中的`lambdify`例程完成的。这将 SymPy 表达式转换为使用 SymPy 标准函数的 NumPy 等价函数来数值评估表达式。结果类似于定义 Python Lambda，因此得名。例如，我们可以使用这个例程将这个配方中的函数和导数转换为 Python 函数：

```py
from sympy.utilities import lambdify
lam_f = lambdify(x, f)
lam_fp = lambdify(x, fp)
```

`lambdify`例程接受两个参数。第一个是要提供的变量，上一个代码块中的`x`，第二个是在调用此函数时要评估的表达式。例如，我们可以评估之前定义的 lambdified SymPy 表达式，就好像它们是普通的 Python 函数一样：

```py
lam_f(4)  # 2.9430355293715387
lam_fp(7)  # -0.4212596944408861
```

我们甚至可以在 NumPy 数组上评估这些 lambdified 表达式：

```py
lam_f(np.array([0, 1, 2]))  # array([ 0\. , -7.3890561, 0\. ])
```

`lambdify`例程使用 Python 的`exec`例程来执行代码，因此不应该与未经过消毒的输入一起使用。

# 解方程

许多数学问题最终归结为解形式为*f(x*) = 0 的方程，其中*f*是单变量函数。在这里，我们试图找到一个使方程成立的*x*的值。使方程成立的*x*的值有时被称为方程的*根*。有许多算法可以找到这种形式的方程的解。在这个配方中，我们将使用牛顿-拉弗森和弦截法来解决形式为*f(x*) = 0 的方程。

牛顿-拉弗森方法（牛顿法）和弦截法是良好的标准根查找算法，几乎可以应用于任何情况。这些是*迭代方法*，从一个根的近似值开始，并迭代改进这个近似值，直到它在给定的容差范围内。

为了演示这些技术，我们将使用*使用 SymPy 进行符号计算*配方中定义的函数

![](img/c13a1277-2b71-46bc-9151-3a68073ce5b6.png)

它对所有实数*x*都有定义，并且恰好有两个根，一个在*x*=0，另一个在*x*=2。

## 准备工作

SciPy 包含用于解方程的例程（以及许多其他内容）。根查找例程可以在`scipy`包的`optimize`模块中找到。

如果你的方程不是形式上的*f*(*x*) = *0*，那么你需要重新排列它，使其成为这种情况。这通常不太困难，只需要将右侧的任何项移到左侧即可。例如，如果你希望找到函数的不动点，也就是当*g*(*x*)*= x*时，我们会将方法应用于由*f*(*x*) =*g*(*x*)*- x.*给出的相关函数。

## 如何操作...

`optimize`包提供了用于数值根查找的例程。以下说明描述了如何使用该模块中的`newton`例程：

1.  `optimize`模块没有列在`scipy`命名空间中，所以你必须单独导入它：

```py
from scipy import optimize
```

1.  然后我们必须在 Python 中定义这个函数及其导数：

```py
from math import exp

def f(x):
    return x*(x - 2)*exp(3 - x)
```

1.  这个函数的导数在前一个配方中被计算出来：

```py
def fp(x):
    return -(x**2 - 4*x + 2)*exp(3 - x)
```

1.  对于牛顿-拉弗森和割线法，我们使用`optimize`中的`newton`例程。割线法和牛顿-拉弗森法都需要函数和第一个参数以及第一个近似值`x0`作为第二个参数。要使用牛顿-拉弗森法，我们必须提供*f*的导数，使用`fprime`关键字参数：

```py
optimize.newton(f, 1, fprime=fp) # Using the Newton-Raphson method
# 2.0
```

1.  使用割线法时，只需要函数，但是我们必须提供根的前两个近似值；第二个作为`x1`关键字参数提供：

```py
optimize.newton(f, 1., x1=1.5) # Using x1 = 1.5 and the secant method
# 1.9999999999999862
```

牛顿-拉弗森法和割线法都不能保证收敛到根。完全有可能方法的迭代只是在一些点之间循环（周期性）或者波动剧烈（混沌）。

## 工作原理...

对于具有导数*f'*(*x*)和初始近似值*x[0]*的函数*f*(*x*)，牛顿-拉弗森方法使用以下公式进行迭代定义

![](img/f3f99361-b4f0-4dde-8420-e7e76aa123a3.png)

对于每个整数*i* ≥*0*。从几何上讲，这个公式是通过考虑梯度的负方向（所以函数是递减的）如果*f(x[i])*>*0*或正方向（所以函数是递增的）如果*f*(*x[i]*) <*o*。

割线法基于牛顿-拉弗森法，但是用近似值替换了一阶导数

![](img/f2412966-ce93-4807-8fb9-cadb324252e5.png)

当*x[i]*-*x[i-1]*足够小时，这意味着方法正在收敛，这是一个很好的近似值。不需要函数*f*的导数的代价是我们需要一个额外的初始猜测来启动方法。该方法的公式如下

![](img/85704de4-933f-4fd1-86a4-792565350241.png)

一般来说，如果任一方法得到一个足够接近根的初始猜测（割线法的猜测），那么该方法将收敛于该根。牛顿-拉弗森法在迭代中导数为零时也可能失败，此时公式未被很好地定义。

## 还有更多...

本配方中提到的方法是通用方法，但在某些情况下可能有更快或更准确的方法。广义上讲，根查找算法分为两类：在每次迭代中使用函数梯度信息的算法（牛顿-拉弗森、割线、Halley）和需要根位置的界限的算法（二分法、regula-falsi、Brent）。到目前为止讨论的算法属于第一类，虽然通常相当快，但可能无法收敛。

第二种算法是已知根存在于指定区间内的算法*a ≤**x* ≤*b*。我们可以通过检查*f*(*a*)和*f*(*b*)是否有不同的符号来检查根是否在这样的区间内，也就是说，*f*(*a*) <*0*<*f*(*b*)或*f*(*b*) <*0*<*f*(*a*)其中一个为真。（当然，前提是函数是*连续*的，在实践中往往是这样。）这种类型的最基本算法是二分法算法，它重复地将区间二分，直到找到根的足够好的近似值。基本前提是在*a*和*b*之间分割区间，并选择函数改变符号的区间。该算法重复，直到区间非常小。以下是 Python 中此算法的基本实现：

```py
from math import copysign

def bisect(f, a, b, tol=1e-5):
    """Bisection method for root finding"""
    fa, fb = f(a), f(b)
    assert not copysign(fa, fb) == fa, "Function must change signs"
    while (b - a) > tol:
        m = (b - a)/2 # mid point of the interval
        fm = f(m)
        if fm == 0:
            return m
        if copysign(fm, fa) == fm: # fa and fm have the same sign
            a = m
            fa = fm
        else: # fb and fm have the same sign
            b = m
        return a
```

该方法保证收敛，因为在每一步中，距离*b-a*减半。但是，可能需要比牛顿-拉弗森或割线法更多的迭代次数。`optimize`中也可以找到二分法的版本。这个版本是用 C 实现的，比这里呈现的版本要高效得多，但是在大多数情况下，二分法不是最快的方法。

Brent 的方法是对二分法的改进，在`optimize`模块中作为`brentq`可用。它使用二分和插值的组合来快速找到方程的根：

```py
optimize.brentq(f, 1.0, 3.0)  # 1.9999999999998792
```

重要的是要注意，涉及括号（二分法、regula-falsi、Brent）的技术不能用于找到复变量的根函数，而不使用括号（Newton、割线、Halley）的技术可以。

# 使用 SciPy 进行数值积分

积分可以解释为曲线与*x*轴之间的区域，根据这个区域是在轴的上方还是下方进行标记。有些积分无法直接使用符号方法计算，而必须进行数值近似。其中一个经典例子是高斯误差函数，在第一章的*基本数学函数*部分中提到。这是由以下公式定义的

![](img/97d08d9b-2e67-4e07-88c0-f52be50f2ae1.png)

这里出现的积分也无法通过符号方法计算。

在本示例中，我们将看到如何使用 SciPy 包中的数值积分例程来计算函数的积分。

## 准备工作

我们使用`scipy.integrate`模块，其中包含几个用于计算数值积分的例程。我们将此模块导入如下：

```py
from scipy import integrate
```

## 操作步骤...

以下步骤描述了如何使用 SciPy 进行数值积分：

1.  我们评估出现在误差函数定义中的积分在*x = 1*处的值。为此，我们需要在 Python 中定义被积函数（出现在积分内部）：

```py
def erf_integrand(t):
    return np.exp(-t**2)
```

`scipy.integrate`中有两个主要例程用于执行数值积分（求积），可以使用。第一个是`quad`函数，它使用 QUADPACK 执行积分，第二个是`quadrature`。

1.  `quad`例程是一个通用的积分工具。它期望三个参数，即要积分的函数（`erf_integrand`），下限（`-1.0`）和上限（`1.0`）：

```py
val_quad, err_quad = integrate.quad(erf_integrand, -1.0, 1.0)
# (1.493648265624854, 1.6582826951881447e-14)
```

第一个返回值是积分的值，第二个是误差的估计。

1.  使用`quadrature`例程重复计算，我们得到以下结果。参数与`quad`例程相同：

```py
val_quadr, err_quadr = integrate.quadrature(erf_integrand, -1.0,
    1.0)
# (1.4936482656450039, 7.459897144457273e-10)
```

输出与代码的格式相同，先是积分的值，然后是误差的估计。请注意，`quadrature`例程的误差更大。这是因为一旦估计的误差低于给定的容差，方法就会终止，当调用例程时可以修改这个容差。

## 工作原理...

大多数数值积分技术都遵循相同的基本过程。首先，我们选择积分区域中的点*x[i]*，对于*i = 1, 2,…, n*，然后使用这些值和值*f*(*x[i]*)来近似积分。例如，使用梯形法则，我们通过以下方式近似积分

![](img/ae1f28f9-d69a-446b-a943-b34ce41e4bcb.png)

其中*a < x[1]< x[2]< … < x[n-1]< b*，*h*是相邻*x[i]*值之间的（公共）差异，包括端点*a*和*b*。这可以在 Python 中实现如下：

```py
def trapezium(func, a, b, n_steps):
    """Estimate an integral using the trapezium rule"""
    h = (b - a) / n_steps
    x_vals = np.arange(a + h, b, h) 
    y_vals = func(x_vals)
    return 0.5*h*(func(a) + func(b) + 2.*np.sum(y_vals))
```

`quad`和`quadrature`使用的算法比这复杂得多。使用这个函数来近似使用`trapezium`积分`erf_integrand`的积分得到的结果是 1.4936463036001209，这与`quad`和`quadrature`例程的近似结果在 5 位小数的情况下是一致的。

`quadrature`例程使用固定容差的高斯积分，而`quad`例程使用 Fortran 库 QUADPACK 例程中实现的自适应算法。对两个例程进行计时，我们发现对于配方中描述的问题，`quad`例程大约比`quadrature`例程快 5 倍。`quad`例程在大约 27 微秒内执行，平均执行 1 百万次，而`quadrature`例程在大约 134 微秒内执行。（您的结果可能会因系统而异。）

## 还有更多...

本节提到的例程需要知道被积函数，但情况并非总是如此。相反，可能是我们知道一些(*x*,y)对，其中*y = f*(*x*)，但我们不知道要在额外点上评估的函数*f*。在这种情况下，我们可以使用`scipy.integrate`中的采样积分技术之一。如果已知点的数量非常大，并且所有点都是等间距的，我们可以使用 Romberg 积分来很好地近似积分。为此，我们使用`romb`例程。否则，我们可以使用梯形法则的变体（如上所述）使用`trapz`例程，或者使用`simps`例程使用辛普森法则。

# 数值解简单微分方程

微分方程出现在一个数量根据给定关系演变的情况中，通常是随着时间的推移。它们在工程和物理学中非常常见，并且自然地出现。一个经典的（非常简单）微分方程的例子是牛顿提出的冷却定律。物体的温度以与当前温度成比例的速率冷却。从数学上讲，这意味着我们可以写出物体在时间*t > 0*时的温度*T*的导数，使用微分方程

![](img/81295a45-5fdf-4837-86ac-96caf899fa75.png)

常数*k*是一个确定冷却速率的正常数。这个微分方程可以通过首先“分离变量”，然后积分和重新排列来解析地解决。执行完这个过程后，我们得到了一般解

![](img/ddae7c6c-e2c7-43aa-b764-484f7b27b095.png)

其中*T[0]*是初始温度。

在这个配方中，我们将使用 SciPy 的`solve_ivp`例程数值地解决一个简单的常微分方程。

## 准备工作

我们将演示在 Python 中使用先前描述的冷却方程数值地解决微分方程的技术，因为在这种情况下我们可以计算真实解。我们将初始温度取为*T[0]= 50*和*k = 0.2*。让我们也找出*t*值在 0 到 5 之间的解。

一般（一阶）微分方程的形式为

![](img/4df58dda-2ef5-4b6e-a9c6-b14742a31fcb.png)

其中*f*是*t*（自变量）和*y*（因变量）的某个函数。在这个公式中，*T*是因变量，*f(t, T) = -kt*。SciPy 包中用于解决微分方程的例程需要函数*f*和初始值*y[0]*[以及我们需要计算解的*t*值范围。要开始，我们需要在 Python 中定义我们的函数*f*并创建变量*y[0]*[和*t*范围，准备提供给 SciPy 例程：]]

```py
def f(t, y):
    return -0.2*y

t_range = (0, 5)
```

接下来，我们需要定义应从中找到解的初始条件。出于技术原因，初始*y*值必须指定为一维 NumPy 数组：

```py
T0 = np.array([50.])
```

由于在这种情况下，我们已经知道真实解，我们也可以在 Python 中定义这个解，以便与我们将计算的数值解进行比较：

```py
def true_solution(t):
    return 50.*np.exp(-0.2*t)
```

## 如何做到...

按照以下步骤数值求解微分方程并绘制解以及误差：

1.  我们使用 SciPy 中的`integrate`模块中的`solve_ivp`例程来数值求解微分方程。我们添加了一个最大步长的参数，值为`0.1`，这样解就可以在合理数量的点上计算出来：

```py
sol = integrate.solve_ivp(f, t_range, T0, max_step=0.1)
```

1.  接下来，我们从`solve_ivp`方法返回的`sol`对象中提取解的值：

```py
t_vals = sol.t
T_vals = sol.y[0, :]
```

1.  接下来，我们按如下方式在一组坐标轴上绘制解。由于我们还将在同一图中绘制近似误差，我们使用`subplots`例程创建两个子图：

```py
fig, (ax1, ax2) = plt.subplots(1, 2, tight_layout=True)
ax1.plot(t_vals, T_vals)
ax1.set_xlabel("$t$")
ax1.set_ylabel("$T$")
ax1.set_title("Solution of the cooling equation")
```

这在*图 3.1*的左侧显示了解决方案的图。

1.  为此，我们需要计算从`solve_ivp`例程中获得的点处的真实解，然后计算真实解和近似解之间的差的绝对值：

```py
err = np.abs(T_vals - true_solution(t_vals))
```

1.  最后，在*图 3.1*的右侧，我们使用*y*轴上的对数刻度绘制近似误差。然后，我们可以使用`semilogy`绘图命令在右侧使用对数刻度*y*轴，就像我们在第二章中看到的那样，*使用 Matplotlib 进行数学绘图*：

```py
ax2.semilogy(t_vals, err)
ax2.set_xlabel("$t$")
ax2.set_ylabel("Error")
ax2.set_title("Error in approximation")
```

*图 3.1*中的左侧图显示随时间降低的温度，而右侧图显示随着我们远离初始条件给出的已知值，误差增加：

![](img/4c0a6446-02e4-49ec-9940-00d778538fbe.png)图 3.1：使用默认设置使用 solve_ivp 例程获得冷却方程的数值解的绘图

## 它是如何工作的...

解决微分方程的大多数方法都是“时间步进”方法。(*t[i], y[i]*)对是通过采取小的*t*步骤并逼近函数*y*的值来生成的。这可能最好地通过欧拉方法来说明，这是最基本的时间步进方法。固定一个小的步长*h > 0*，我们使用以下公式在第*i*步形成近似值

![](img/62d74fc6-de22-40c3-a06b-eca3c00eaf85.png)

从已知的初始值*y[0]*开始。我们可以轻松地编写一个执行欧拉方法的 Python 例程如下（当然，实现欧拉方法有许多不同的方法；这是一个非常简单的例子）：

1.  首先，通过创建将存储我们将返回的*t*值和*y*值的列表来设置方法：

```py
def euler(func, t_range, y0, step_size):
    """Solve a differential equation using Euler's method"""
    t = [t_range[0]]
    y = [y0]
    i = 0
```

1.  欧拉方法一直持续到我们达到*t*范围的末尾。在这里，我们使用`while`循环来实现这一点。循环的主体非常简单；我们首先递增计数器`i`，然后将新的*t*和*y*值附加到它们各自的列表中。

```py
    while t[i] < t_range[1]:
        i += 1
        t.append(t[i-1] + step_size)  # step t
        y.append(y[i-1] + step_size*func(t[i-1], y[i-1]))  # step y
    return t, y
```

`solve_ivp`例程默认使用龙格-库塔-费尔伯格（RK45）方法，该方法能够自适应步长，以确保近似误差保持在给定的容差范围内。这个例程期望提供三个位置参数：函数*f*，应找到解的*t*范围，以及初始*y*值（在我们的例子中为*T[0]*）。可以提供可选参数来更改求解器、要计算的点数以及其他几个设置。

传递给`solve_ivp`例程的函数必须有两个参数，就像*准备就绪*部分中描述的一般微分方程一样。函数可以有额外的参数，可以使用`args`关键字为`solve_ivp`例程提供这些参数，但这些参数必须位于两个必要参数之后。将我们之前定义的`euler`例程与步长为 0.1 的`solve_ivp`例程进行比较，我们发现`solve_ivp`解的最大真实误差在 10^(-6)数量级，而`euler`解只能达到 31 的误差。`euler`例程是有效的，但步长太大，无法克服累积误差。

`solve_ivp`例程返回一个存储已计算解的信息的解对象。这里最重要的是`t`和`y`属性，它们包含计算解的*t*值和解*y*本身。我们使用这些值来绘制我们计算的解。*y*值存储在形状为`(n, N)`的 NumPy 数组中，其中`n`是方程的分量数（这里是 1），`N`是计算的点数。`sol`中的*y*值存储在一个二维数组中，在这种情况下有 1 行和许多列。我们使用切片`y[0, :]`来提取这个第一行作为一维数组，可以用来在*步骤 4*中绘制解。

我们使用对数缩放的*y*轴来绘制误差，因为有趣的是数量级。在非缩放的*y*轴上绘制它会得到一条非常靠近*x*轴的线，这不会显示出随着*t*值的变化误差的增加。对数缩放的*y*轴清楚地显示了这种增加。

## 还有更多...

`solve_ivp`例程是微分方程的多个求解器的便捷接口，默认为龙格-库塔-费尔伯格（RK45）方法。不同的求解器有不同的优势，但 RK45 方法是一个很好的通用求解器。

## 另请参阅

有关如何在 Matplotlib 中的图中添加子图的更详细说明，请参阅第二章中的*添加子图*示例，*使用 Matplotlib 进行数学绘图*。

# 解决微分方程系统

微分方程有时出现在由两个或更多相互关联的微分方程组成的系统中。一个经典的例子是竞争物种的简单模型。这是一个由以下方程给出的竞争物种的简单模型，标记为*P*（猎物）和*W*（捕食者）：*

*![](img/e6e64a45-d031-4ff3-9d17-bac1ea19c5ec.png)

第一个方程规定了猎物物种*P*的增长，如果没有任何捕食者，它将是指数增长。第二个方程规定了捕食者物种*W*的增长，如果没有任何猎物，它将是指数衰减。当然，这两个方程是*耦合*的；每种群体的变化都取决于两种群体。捕食者以与两种群体的乘积成比例的速率消耗猎物，并且捕食者以与猎物相对丰富度成比例的速率增长（再次是两种群体的乘积）。

在本示例中，我们将分析一个简单的微分方程系统，并使用 SciPy 的`integrate`模块来获得近似解。

## 准备就绪

使用 Python 解决常微分方程组的工具与解决单个方程的工具相同。我们再次使用 SciPy 中的`integrate`模块中的`solve_ivp`例程。然而，这只会给我们一个在给定起始种群下随时间预测的演变。因此，我们还将使用 Matplotlib 中的一些绘图工具来更好地理解这种演变。

## 如何做到...

以下步骤介绍了如何分析一个简单的常微分方程组：

1.  我们的第一个任务是定义一个包含方程组的函数。这个函数需要接受两个参数，就像单个方程一样，除了依赖变量*y*（在*Solving simple differential equations numerically*配方中的符号）现在将是一个包含与方程数量相同的元素的数组。在这里，将有两个元素。在这个配方中，我们需要的函数如下：

```py
def predator_prey_system(t, y):
    return np.array([5*y[0] - 0.1*y[0]*y[1], 0.1*y[1]*y[0] -
       6*y[1]])
```

1.  现在我们已经在 Python 中定义了系统，我们可以使用 Matplotlib 中的`quiver`例程来生成一个图表，描述种群将如何演变——由方程给出——在许多起始种群中。我们首先设置一个网格点，我们将在这些点上绘制这种演变。选择相对较少的点数作为`quiver`例程的一个好主意，否则在图表中很难看到细节。对于这个例子，我们绘制种群值在 0 到 100 之间：

```py
p = np.linspace(0, 100, 25)
w = np.linspace(0, 100, 25)
P, W = np.meshgrid(p, w)
```

1.  现在，我们计算每对这些点的系统值。请注意，系统中的任何一个方程都不是时间相关的（它们是自治的）；时间变量*t*在计算中并不重要。我们为*t*参数提供值`0`：

```py
dp, dw = predator_prey_system(0, np.array([P, W]))
```

1.  现在变量`dp`和`dw`分别保存了种群*P*和*W*在我们的网格中每个点开始时将如何演变的“方向”。我们可以使用`matplotlib.pyplot`中的`quiver`例程一起绘制这些方向：

```py
fig, ax = plt.subplots()
ax.quiver(P, W, dp, dw)
ax.set_title("Population dynamics for two competing species")
ax.set_xlabel("P")
ax.set_ylabel("W")
```

现在绘制这些命令的结果给出了*图 3.2*，它给出了解决方案演变的“全局”图像：

![](img/3e756b45-1557-4de1-99a4-980dfa5dbb69.png)

图 3.2：显示由常微分方程组模拟的两个竞争物种的种群动态的 quiver 图

为了更具体地理解解决方案，我们需要一些初始条件，这样我们就可以使用前面配方中描述的`solve_ivp`例程。

1.  由于我们有两个方程，我们的初始条件将有两个值。（回想一下，在*Solving simple differential equations numerically*配方中，我们看到提供给`solve_ivp`的初始条件需要是一个 NumPy 数组。）让我们考虑初始值*P(0) = 85*和*W(0) = 40*。我们在一个 NumPy 数组中定义这些值，小心地按正确的顺序放置它们：

```py
initial_conditions = np.array([85, 40])
```

1.  现在我们可以使用`scipy.integrate`模块中的`solve_ivp`。我们需要提供`max_step`关键字参数，以确保我们在解决方案中有足够的点来得到平滑的解曲线：

```py
from scipy import integrate
sol = integrate.solve_ivp(predator_prey_system, (0., 5.),
   initial_conditions, max_step=0.01)
```

1.  让我们在现有的图上绘制这个解，以展示这个特定解与我们已经生成的方向图之间的关系。我们同时也绘制初始条件：

```py
ax.plot(initial_conditions[0], initial_conditions[1], "ko")
ax.plot(sol.y[0, :], sol.y[1, :], "k", linewidth=0.5)
```

这个结果显示在*图 3.3*中：

![](img/23767cc7-85e5-4168-93d3-7ab550febf33.png)图 3.3：在显示一般行为的 quiver 图上绘制的解轨迹

## 它是如何工作的...

用于一组常微分方程的方法与单个常微分方程完全相同。我们首先将方程组写成一个单一的向量微分方程，

![](img/949a688b-96e9-4194-80e5-6c1537087601.png)

然后可以使用时间步进方法来解决，就好像**y**是一个简单的标量值一样。

使用`quiver`例程在平面上绘制方向箭头的技术是学习系统如何从给定状态演变的一种快速简单的方法。函数的导数代表曲线的梯度（*x*，*u*（*x*）），因此微分方程描述了解决方案函数在位置*y*和时间*t*的梯度。一组方程描述了在给定位置**y**和时间*t*的单独解决方案函数的梯度。当然，位置现在是一个二维点，所以当我们在一个点上绘制梯度时，我们将其表示为从该点开始的箭头，指向梯度的方向。箭头的长度表示梯度的大小；箭头越长，解决方案曲线在该方向上移动得越“快”。

当我们在这个方向场上绘制解的轨迹时，我们可以看到曲线（从该点开始）遵循箭头指示的方向。解的轨迹所显示的行为是一个*极限环*，其中每个变量的解都是周期性的，因为两种物种的人口增长或下降。如果我们将每个人口随时间的变化绘制成图，从*图 3.4*中可以看出，这种行为描述可能更清晰。从*图 3.3*中并不立即明显的是解的轨迹循环几次，但这在*图 3.4*中清楚地显示出来：

![](img/c2de769a-db1c-4ac4-b755-a8f69457b59d.png)

图 3.4：人口*P*和*W*随时间的变化图。两种人口都表现出周期性行为。

## 还有更多...

通过在变量之间绘制相空间（平面）分析系统的普通微分方程组的技术称为*相空间（平面）分析*。在这个示例中，我们使用`quiver`绘图例程快速生成了微分方程系统的相平面的近似值。通过分析微分方程系统的相平面，我们可以识别解的不同局部和全局特征，如极限环。

# 数值求解偏微分方程

偏微分方程是涉及函数在两个或多个变量中的*偏导数*的微分方程，而不是仅涉及单个变量的*普通导数*。偏微分方程是一个广泛的主题，可以轻松填满一系列书籍。偏微分方程的典型例子是（一维）*热方程*。

![](img/6184866a-5a48-48db-9c1f-a991e60c83b6.png)

其中α是一个正常数，*f*（*t*，*x*）是一个函数。这个偏微分方程的解是一个函数*u*（*t*，*x*），它表示了在给定时间*t*>0 时，占据*x*范围 0≤*x*≤*L*的杆的温度。为了简单起见，我们将取*f*（*t*，*x*）=0，这相当于说系统没有加热/冷却，α=1，*L=2*。在实践中，我们可以重新调整问题来修复常数α，所以这不是一个限制性的问题。在这个例子中，我们将使用边界条件

![](img/245337ee-7e9f-441a-8425-7bbebaec6f5d.png)

这相当于说杆的两端保持在恒定温度 0。我们还将使用初始温度剖面

![](img/9723b438-e560-4a28-a6de-ca9930ec343a.png)

这个初始温度剖面描述了 0 和 2 之间的值之间的平滑曲线，峰值为 3，这可能是将杆在中心加热到 3 度的结果。

我们将使用一种称为*有限差分*的方法，将杆分成若干相等的段，并将时间范围分成若干离散步骤。然后我们计算每个段和每个时间步长的解的近似值。

在这个示例中，我们将使用有限差分来解一个简单的偏微分方程。

## 准备工作

对于这个示例，我们将需要 NumPy 包和 Matplotlib 包，通常导入为`np`和`plt`。我们还需要从`mpl_toolkits`导入`mplot3d`模块，因为我们将生成一个 3D 图：

```py
from mpl_toolkits import mplot3d
```

我们还需要一些来自 SciPy 包的模块。

## 如何做...

在接下来的步骤中，我们将通过有限差分来解决热方程：

1.  首先创建代表系统物理约束的变量：杆的范围和α的值：

```py
alpha = 1
x0 = 0 # Left hand x limit
xL = 2 # Right hand x limit
```

1.  我们首先将*x*范围分成*N*个相等的*间隔—我们在这个例子中取*N = 10*—使用*N+1*个点。我们可以使用 NumPy 中的`linspace`例程生成这些点。我们还需要每个间隔的公共长度*h*:

```py
N = 10
x = np.linspace(x0, xL, N+1)
h = (xL - x0) / N
```

1.  接下来，我们需要设置时间方向上的步长。我们在这里采取了稍微不同的方法；我们设置时间步长*k*和步数（隐含地假设我们从时间 0 开始）：

```py
k = 0.01
steps = 100
t = np.array([i*k for i in range(steps+1)])
```

1.  为了使方法正常运行，我们必须满足：

![](img/bda8c9e8-45ba-4021-99ba-9cfaa5b43f1a.png)

否则系统可能变得不稳定。我们将左侧存储在一个变量中，以便在*步骤 4*中使用，并使用断言来检查这个不等式是否成立：

```py
r = alpha*k / h**2
assert r < 0.5, f"Must have r < 0.5, currently r={r}"
```

1.  现在我们可以构建一个矩阵，其中包含来自有限差分方案的系数。为此，我们使用`scipy.sparse`模块中的`diags`例程创建一个稀疏的三对角矩阵：

```py
from scipy import sparse
diag = [1, *(1-2*r for _ in range(N-1)), 1]
abv_diag = [0, *(r for _ in range(N-1))]
blw_diag = [*(r for _ in range(N-1)), 0]

A = sparse.diags([blw_diag, diag, abv_diag], (-1, 0, 1), shape=(N+1,
      N+1), dtype=np.float64, format="csr")
```

1.  接下来，我们创建一个空白矩阵来保存解决方案：

```py
u = np.zeros((steps+1, N+1), dtype=np.float64)
```

1.  我们需要将初始配置添加到第一行。这样做的最佳方法是创建一个保存初始配置的函数，并将在刚刚创建的矩阵`u`上的`x`数组上评估这个函数的结果：

```py
def initial_profile(x):
    return 3*np.sin(np.pi*x/2)

u[0, :] = initial_profile(x)
```

1.  现在我们可以简单地循环每一步，通过将`A`和前一行相乘来计算矩阵`u`的下一行：

```py
for i in range(steps):
    u[i+1, :] = A @ u[i, :]
```

1.  最后，为了可视化我们刚刚计算的解，我们可以使用 Matplotlib 将解作为曲面绘制出来：

```py
X, T = np.meshgrid(x, t)
fig = plt.figure()
ax = fig.add_subplot(projection="3d")

ax.plot_surface(T, X, u, cmap="hot")
ax.set_title("Solution of the heat equation")
ax.set_xlabel("t")
ax.set_ylabel("x")
ax.set_zlabel("u")
```

这导致了*图 3.5*中显示的曲面图：

![](img/7aca3573-b5b9-40e9-a959-4908ff776868.png)

图 3.5：使用有限差分方法计算的热方程解在范围 0 ≤ *x* ≤ 2 上的曲面图，使用了 10 个网格点

## 它是如何工作的...

有限差分方法通过用仅涉及函数值的简单分数替换每个导数来工作，我们可以估计这些分数。要实现这种方法，我们首先将空间范围和时间范围分解为若干离散间隔，由网格点分隔。这个过程称为*离散化*。然后我们使用微分方程和初始条件以及边界条件来形成连续的近似，这与`solve_ivp`例程在*Solving differential equations numerically*示例中使用的时间步进方法非常相似。

为了解决热方程这样的偏微分方程，我们至少需要三个信息。通常，对于热方程，这将以空间维度的*边界条件*的形式出现，告诉我们在杆的两端行为如何，以及时间维度的*初始条件*，即杆上的初始温度分布。

前面描述的有限差分方案通常被称为**前向时间中心空间**（**FTCS**）方案，因为我们使用*前向有限差分*来估计时间导数，使用*中心有限差分*来估计（二阶）空间导数。这些有限差分的公式如下：

![](img/1d60f63d-ee29-4640-ab39-515bf356bad2.png)

和

![](img/ea3d52cb-c07d-4a36-a313-713d29ff4f74.png)

将这些近似代入热方程，并使用近似值*u*[*i*]^(*j*)来表示在*i*空间点上经过*j*时间步后*u*(*t[j]*, x[i])的值，我们得到

![](img/c3c59b14-5e8f-4dcb-9122-65f09009fd65.png)

可以重新排列以获得公式

![](img/58222441-072c-406c-a4c3-07fe3033a114.png)

粗略地说，这个方程表示给定点的下一个温度取决于以前时间的周围温度。这也显示了为什么`r`值的条件是必要的；如果条件不成立，右侧的中间项将是负的。

我们可以将这个方程组写成矩阵形式，

![](img/37370a20-7786-4985-9625-53e11fbdbe08.png)

其中**u***^j*是包含近似*u[i]^j*和矩阵*A*的向量，矩阵*A*在*步骤 4*中定义。这个矩阵是三对角的，这意味着非零条目出现在或邻近主对角线上。我们使用 SciPy `sparse`模块中的`diag`例程，这是一种定义这种矩阵的实用程序。这与本章中*解方程*配方中描述的过程非常相似。这个矩阵的第一行和最后一行都是零，除了在左上角和右下角，分别代表（不变的）边界条件。其他行的系数由微分方程两侧的有限差分近似给出。我们首先创建对角线条目和对角线上下方的条目，然后我们使用`diags`例程创建稀疏矩阵。矩阵应该有*N+1*行和列，以匹配网格点的数量，并且我们将数据类型设置为双精度浮点数和 CSR 格式。

初始配置给我们向量**u**⁰，从这一点开始，我们可以通过简单地执行矩阵乘法来计算每个后续时间步骤，就像我们在*步骤 7*中看到的那样。

## 还有更多...

我们在这里描述的方法相当粗糙，因为近似可能变得不稳定，正如我们提到的，如果时间步长和空间步长的相对大小没有得到仔细控制。这种方法是*显式*的，因为每个时间步骤都是显式地使用来自上一个时间步骤的信息来计算的。还有*隐式*方法，它给出了一个可以求解以获得下一个时间步骤的方程组。不同的方案在解的稳定性方面具有不同的特性。

当函数*f*(*t*, *x*)不为 0 时，我们可以通过使用赋值来轻松适应这种变化

![](img/34ccf6b9-a549-469c-a194-2d67678204d2.png)

其中函数被适当地向量化以使这个公式有效。在解决问题的代码方面，我们只需要包括函数的定义，然后将解决方案的循环更改如下：

```py
for i in range(steps):
    u[i+1, :] = A @ u[i, :] + f(t[i], x)
```

从物理上讲，这个函数代表了杆上每个点的外部热源（或者热池）。这可能随时间变化，这就是为什么一般来说，这个函数应该有*t*和*x*作为参数（尽管它们不一定都被使用）。

我们在这个例子中给出的边界条件代表了杆的两端保持在恒定温度为 0。这种边界条件有时被称为*Dirichlet*边界条件。还有*Neumann*边界条件，其中函数*u*的导数在边界处给定。例如，我们可能已经给出了边界条件

![](img/20efdf73-2579-492b-ad91-657fd131d376.png)

这在物理上可以被解释为杆的两端被绝缘，因此热量不能通过端点逃逸。对于这种边界条件，我们需要稍微修改矩阵*A*，但方法本质上保持不变。实际上，在边界的左侧插入一个虚拟的*x*值，并在左边界（*x = 0*）使用向后有限差分，我们得到

![](img/3df7eb78-60b0-45a4-8ac8-a3d354f85c02.png)

使用这个二阶有限差分近似，我们得到

![](img/a8b3a28a-812c-4e04-bfc3-e40be3cb4481.png)

这意味着我们矩阵的第一行应包含*1-r*，然后是*r*，然后是 0。对右手极限进行类似的计算得到矩阵的最后一行。

```py
diag = [1-r, *(1-2*r for _ in range(N-1)), 1-r]
abv_diag = [*(r for _ in range(N))]
blw_diag = [*(r for _ in range(N))]

A = sparse.diags([blw_diag, diag, abv_diag], (-1, 0, 1), shape=(N+1, N+1), dtype=np.float64, format="csr")
```

对于涉及偏微分方程的更复杂问题，可能更适合使用*有限元*求解器。有限元方法使用比我们在本篇中看到的有限差分方法更复杂的方法来计算解决方案，通常比偏微分方程更灵活。然而，这需要更多依赖更高级数学理论的设置。另一方面，有一个用于使用有限元方法解决偏微分方程的 Python 包，如 FEniCS ([fenicsproject.org](https://fenicsproject.org))。使用 FEniCS 等包的优势在于它们通常针对性能进行了调整，这在解决复杂问题时对高精度至关重要。

## 另请参阅

FEniCS 文档介绍了有限元方法以及使用该包解决各种经典偏微分方程的示例。有关该方法和理论的更全面介绍可在以下书籍中找到：

+   *Johnson, C. (2009).Numerical solution of partial differential equations by the finite element method. Mineola, N.Y.: Dover Publications.*

有关如何使用 Matplotlib 生成三维曲面图的更多详细信息，请参阅第二章中的*曲面和等高线图*食谱。

# 使用离散傅立叶变换进行信号处理

来自微积分中最有用的工具之一是*傅立叶变换*。粗略地说，傅立叶变换以可逆的方式改变了某些函数的表示。这种表示的改变在处理作为时间函数的信号时特别有用。在这种情况下，傅立叶变换将信号表示为频率函数；我们可以将其描述为从信号空间到频率空间的转换。这可以用于识别信号中存在的频率以进行识别和其他处理。在实践中，我们通常会有信号的离散样本，因此我们必须使用*离散傅立叶变换*来执行这种分析。幸运的是，有一种计算效率高的算法，称为**快速傅立叶变换**（**FFT**），用于对样本应用离散傅立叶变换。

*我们将遵循使用 FFT 处理嘈杂信号的常见过程。第一步是应用 FFT 并使用数据计算信号的功率谱密度。然后我们识别峰值并滤除对信号贡献不足够大的频率。然后我们应用逆 FFT 来获得滤波后的信号。

在本篇中，我们使用 FFT 来分析信号的样本，并识别存在的频率并清除信号中的噪音。

## 准备工作

对于本篇，我们只需要导入 NumPy 和 Matplotlib 包，如往常一样命名为`np`和`plt`。

## 如何做...

按照以下说明使用 FFT 处理嘈杂信号：

1.  我们定义一个函数来生成我们的基础信号：

```py
def signal(t, freq_1=4.0, freq_2=7.0):
    return np.sin(freq_1 * 2 * np.pi * t) + np.sin(freq_2 * 2 *
        np.pi * t)
```

1.  接下来，我们通过向基础信号添加一些高斯噪声来创建我们的样本信号。我们还创建一个数组，以便在以后方便时保存样本*t*值处的真实信号：

```py
state = np.random.RandomState(12345)
sample_size = 2**7 # 128
sample_t = np.linspace(0, 4, sample_size)
sample_y = signal(sample_t) + state.standard_normal(sample_size)
sample_d = 4./(sample_size - 1) # Spacing for linspace array
true_signal = signal(sample_t)
```

1.  我们使用 NumPy 的`fft`模块来计算离散傅立叶变换。在开始分析之前，我们从 NumPy 中导入这个模块：

```py
from numpy import fft
```

1.  要查看嘈杂信号的样子，我们可以绘制样本信号点并叠加真实信号：

```py
fig1, ax1 = plt.subplots()
ax1.plot(sample_t, sample_y, "k.", label="Noisy signal")
ax1.plot(sample_t, signal(sample_t), "k--", label="True signal")

ax1.set_title("Sample signal with noise")
ax1.set_xlabel("Time")
ax1.set_ylabel("Amplitude")
ax1.legend()
```

此处创建的图表显示在*图 3.6*中。正如我们所看到的，嘈杂信号与真实信号几乎没有相似之处（用虚线表示）：

![](img/d0bf476c-f0fd-4b59-b167-0db423f6cc22.png)

图 3.6：带真实信号的噪声信号样本

1.  现在，我们将使用离散傅立叶变换来提取样本信号中存在的频率。`fft`模块中的`fft`例程执行 FFT（离散傅立叶变换）：

```py
spectrum = fft.fft(sample_y)
```

1.  `fft`模块提供了一个用于构建适当频率值的例程，称为`fftfreq`。为了方便起见，我们还生成一个包含正频率出现的整数的数组：

```py
freq = fft.fftfreq(sample_size, sample_d)
pos_freq_i = np.arange(1, sample_size//2, dtype=int)
```

1.  接下来，计算信号的**功率谱密度**（**PSD**）如下：

```py
psd = np.abs(spectrum[pos_freq_i])**2 + np.abs(spectrum[-
        pos_freq_i])**2
```

1.  现在，我们可以绘制信号的正频率的 PSD，并使用这个图来识别频率：

```py
fig2, ax2 = plt.subplots()
ax2.plot(freq[pos_freq_i], psd)
ax2.set_title("PSD of the noisy signal")
ax2.set_xlabel("Frequency")
ax2.set_ylabel("Density")
```

结果可以在*图 3.7*中看到。我们可以在这个图表中看到大约在 4 和 7 左右有尖峰，这些是我们之前定义的信号的频率：

![](img/dbf2eebf-e200-4bc8-8319-4a6d67561fc7.png)

图 3.7：使用 FFT 生成的信号的功率谱密度

1.  我们可以识别这两个频率，尝试从噪声样本中重建真实信号。所有出现的次要峰值都不大于 10,000，所以我们可以将其作为滤波器的截止值。现在，我们从所有正频率索引的列表中提取（希望是 2 个）对应于 PSD 中大于 10,000 的峰值的索引：

```py
filtered = pos_freq_i[psd > 1e4]
```

1.  接下来，我们创建一个新的干净频谱，其中只包含我们从噪声信号中提取的频率。我们通过创建一个只包含 0 的数组，然后将`spectrum`的值从对应于滤波频率及其负值的索引复制过来来实现这一点：

```py
new_spec = np.zeros_like(spectrum)
new_spec[filtered] = spectrum[filtered]
new_spec[-filtered] = spectrum[-filtered]
```

1.  现在，我们使用逆 FFT（使用`ifft`例程）将这个干净的频谱转换回原始样本的时间域。我们使用 NumPy 的`real`例程取实部以消除错误的虚部：

```py
new_sample = np.real(fft.ifft(new_spec))
```

1.  最后，我们绘制这个滤波信号和真实信号进行比较：

```py
fig3, ax3 = plt.subplots()
ax3.plot(sample_t, true_signal, color="#8c8c8c", linewidth=1.5, label="True signal")
ax3.plot(sample_t, new_sample, "k--", label="Filtered signal")
ax3.legend()
ax3.set_title("Plot comparing filtered signal and true signal")
ax3.set_xlabel("Time")
ax3.set_ylabel("Amplitude")
```

*步骤 12*的结果显示在*图 3.8*中。我们可以看到，滤波信号与真实信号非常接近，除了一些小的差异：

![](img/c00691ae-14b1-4470-bda0-b8b048536c69.png)

图 3.8：比较使用 FFT 和滤波生成的滤波信号与真实信号的图

## 工作原理...

函数*f*(*t*)的*傅立叶变换*由积分给出

![](img/4eb1fffb-6aa9-4f17-875a-51c90d9c3040.png)

离散傅立叶变换如下：

![](img/87485bb8-515f-4654-9621-e8930510e1c5.png)

这里，*f[k]*值是复数形式的样本值。可以使用前面的公式计算离散傅立叶变换，但实际上这并不高效。使用这个公式计算的复杂度是*O*(*N*²)。FFT 算法将复杂度提高到*O*(*N* log *N*)，这显然更好。书籍*Numerical Recipes*（在*进一步阅读*部分给出完整的参考文献细节）对 FFT 算法和离散傅立叶变换有很好的描述。

我们将对从已知信号（具有已知频率模式）生成的样本应用离散傅立叶变换，以便我们可以看到我们获得的结果与原始信号之间的联系。为了保持这个信号简单，我们创建了一个只有两个频率分量（值为 4 和 7）的信号。从这个信号，我们生成了我们分析的样本。由于 FFT 的工作方式，最好是样本的大小是 2 的幂；如果不是这种情况，我们可以用零元素填充样本使其成为这种情况。我们向样本信号添加了一些高斯噪声，这是一个正态分布的随机数。

`fft`例程返回的数组包含*N+1*个元素，其中*N*是样本大小。索引为 0 的元素对应于 0 频率，或者直流偏移。接下来的*N/2*个元素是对应于正频率的值，最后的*N/2*个元素是对应于负频率的值。频率的实际值由采样点数*N*和采样间距确定，在这个例子中，采样间距存储在`sample_d`中。

频率*ω*处的功率谱密度由以下公式给出

![](img/6c55d8f3-b759-4931-a310-85478b2e65c6.png)

其中*H*(*ω*)代表信号在频率*ω*处的傅里叶变换。功率谱密度测量了每个频率对整体信号的贡献，这就是为什么我们在大约 4 和 7 处看到峰值。由于 Python 索引允许我们对从序列末尾开始的元素使用负索引，我们可以使用正索引数组从`spectrum`中获取正频率和负频率元素。

在*步骤 9*中，我们提取了在图表上峰值超过 10,000 的两个频率的索引。对应于这些索引的频率分别是 3.984375 和 6.97265625，它们并不完全等于 4 和 7，但非常接近。这种差异的原因是我们使用有限数量的点对连续信号进行了采样。（使用更多点当然会得到更好的近似。）

在*步骤 11*中，我们提取了从逆 FFT 返回的数据的实部。这是因为从技术上讲，FFT 处理复杂数据。由于我们的数据只包含实数据，我们期望这个新信号也只包含实数据。然而，会有一些小错误产生，这意味着结果并非完全是实数。我们可以通过取逆 FFT 的实部来纠正这一点。这是合适的，因为我们可以看到虚部非常小。

我们可以在*图 3.8*中看到，滤波信号非常接近真实信号，但并非完全相同。这是因为，如前所述，我们正在用相对较小的样本来逼近连续信号。

## 还有更多...

在生产环境中的信号处理可能会使用专门的软件包，比如`scipy`中的`signal`模块，或者一些更低级的代码或硬件来执行信号的滤波或清理。这个示例更多地应该被看作是 FFT 作为处理从某种基础周期结构（信号）采样的数据的工具的演示。FFT 对于解决偏微分方程非常有用，比如在*数值解偏微分方程*食谱中看到的热方程。

## 另请参阅

有关随机数和正态分布（高斯）的更多信息可以在第四章中找到，*处理随机性和概率*。

# 进一步阅读

微积分是每门本科数学课程中非常重要的一部分。有许多关于微积分的优秀教科书，包括 Spivak 的经典教科书和 Adams 和 Essex 的更全面的课程：

+   *Spivak, M. (2006). Calculus. 3rd ed. Cambridge: Cambridge University Press*

+   *Adams, R. and Essex, C. (2018). Calculus: A Complete Course. 9th ed. Don Mills, Ont: Pearson.Guassian*

关于数值微分和积分的良好来源是经典的*数值方法*书，其中详细描述了如何在 C++中解决许多计算问题的理论概述：

+   *Press, W., Teukolsky, S., Vetterling, W. and Flannery, B. (2007). Numerical recipes:* *The Art of Scientific Computing. 3rd ed. Cambridge: Cambridge University Press*****
