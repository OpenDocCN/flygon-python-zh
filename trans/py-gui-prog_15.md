# 理解 OOP 概念

在本章中，我们将涵盖以下主题：

+   面向对象编程

+   在 GUI 中使用类

+   使用单一继承

+   使用多层继承

+   使用多重继承

# 面向对象编程

Python 支持**面向对象编程**（**OOP**）。OOP 支持可重用性；也就是说，之前编写的代码可以被重用来制作大型应用程序，而不是从头开始。OOP 中的对象指的是类的变量或实例，其中类是一个结构的模板或蓝图，包括方法和变量。类中的变量称为**数据成员**，方法称为**成员函数**。当类的实例或对象被创建时，对象会自动获得对数据成员和方法的访问。

# 创建一个类

`class`语句用于创建一个类。以下是创建类的语法：

```py
class class_name(base_classes):
    statement(s)
```

这里，`class_name`是一个标识符，用于标识类。在`class`语句之后是构成类主体的语句。`class`主体包括要在该类中定义的不同方法和变量。

您可以创建一个独立的类或继承另一个类。被继承的类称为**基类**。在语法中，`class_name`后的`base_classes`参数表示该类将继承的所有基类。如果有多个基类，则它们需要用逗号分隔。被继承的类称为**超类**或**基类**，继承的类称为**派生类**或**子类**。派生类可以使用基类的方法和变量，从而实现可重用性：

```py
class Student:
    name = ""
    def __init__(self, name):
        self.name = name
    def printName(self):
        return self.name
```

在此示例中，`Student`是一个包含名为`name`的属性的类，该属性初始化为 null。

# 使用内置类属性

`class`语句会自动为某些固定的类属性分配特定的值。这些类属性可以用于获取有关类的信息。类属性的列表如下：

+   `__name__`：表示`class`语句中使用的类名

+   `__bases__`：表示`class`语句中提到的基类名称

+   `__dict__`：表示其他类属性的字典对象

+   `__module__`：表示定义类的模块名称

一个类可以有任意数量的方法，每个方法可以有任意数量的参数。方法中始终定义了一个强制的第一个参数，通常将该第一个参数命名为`self`（尽管您可以为此参数指定任何名称）。`self`参数指的是调用方法的类的实例。在类中定义方法的语法如下：

```py
class class_name(base_classes):
    Syntax:
        variable(s)
    def method 1(self):
        statement(s)
    [def method n(self):
        statement(s)]
```

一个类可以有以下两种类型的数据成员：

+   **类变量**：这些是所有实例可共享的变量，任何一个实例对这些变量所做的更改也可以被其他实例看到。这些是在类的任何方法之外定义的数据成员。

+   **实例变量**：这些变量仅在方法内部定义，仅属于对象的当前实例，并且被称为**实例变量**。任何实例对实例变量所做的更改仅限于该特定实例，并不影响其他实例的实例变量。

让我们看看如何创建一个实例方法以及如何使用它来访问类变量。

# 在实例方法中访问类变量

要访问类变量，必须使用类名作为前缀。例如，要访问`Student`类的`name`类变量，需要按以下方式访问：

```py
Student.name
```

您可以看到，`name`类变量以`Student`类名作为前缀。

# 实例

要使用任何类的变量和方法，我们需要创建其对象或实例。类的实例会得到自己的变量和方法的副本。这意味着一个实例的变量不会干扰另一个实例的变量。我们可以创建任意数量的类的实例。要创建类的实例，需要写类名，后跟参数（如果有）。例如，以下语句创建了一个名为`studentObj`的`Student`类的实例：

```py
studentObj=Student()
```

可以创建任意数量的`Student`类的实例。例如，以下行创建了`Student`类的另一个实例：

```py
courseStudent=Student()
```

现在，实例可以访问类的属性和方法。

在定义方法时需要明确指定`self`。在调用方法时，`self`不是必需的，因为 Python 会自动添加它。

要定义类的变量，我们需要使用`__init__()`方法的帮助。`__init__()`方法类似于传统面向对象编程语言中的构造函数，并且是在创建实例后首先执行的方法。它用于初始化类的变量。根据类中如何定义`__init__()`方法，即是否带有参数，参数可能会传递给`__init__()`方法，也可能不会。

如前所述，每个类方法的第一个参数是一个称为`self`的类实例。在`__init__()`方法中，`self`指的是新创建的实例：

```py
class Student:
    name = ""
    def __init__(self):
        self.name = "David"
        studentObj=Student()
```

在上面的例子中，`studentObj`实例是正在创建的`Student`类的实例，并且其类变量将被初始化为`David`字符串。

甚至可以将参数传递给`__init__()`方法，如下例所示：

```py
class Student:
    name = ""
    def __init__(self, name):
        self.name = name
        studentObj=Student("David")
```

在上面的例子中，创建了`studentObj`实例并将`David`字符串传递给它。该字符串将被分配给`__init__()`方法中定义的`name`参数，然后用于初始化实例的类变量`name`。请记住，`__init__()`方法不能返回值。

与类变量一样，可以通过类的实例访问类的方法，后跟方法名，中间用句点(`.`)分隔。假设`Student`类中有一个`printName()`方法，可以通过以下语句通过`studentObj`实例访问：

```py
studentObj.printName()
```

# 在 GUI 中使用类

通过 GUI 从用户接收的数据可以直接通过简单变量进行处理，并且处理后的数据只能通过变量显示。但是，为了保持数据的结构化格式并获得面向对象编程的好处，我们将学习将数据保存在类的形式中。也就是说，用户通过 GUI 访问的数据可以分配给类变量，通过类方法进行处理和显示。

让我们创建一个应用程序，提示用户输入姓名，并在输入姓名后点击推送按钮时，应用程序将显示一个 hello 消息以及输入的姓名。用户输入的姓名将被分配给一个类变量，并且 hello 消息也将通过调用类的类方法生成。

# 如何做...

本节的重点是理解用户输入的数据如何分配给类变量，以及如何通过类方法访问显示的消息。让我们基于没有按钮的对话框模板创建一个新应用程序，并按照以下步骤进行操作：

1.  将两个标签小部件、一个行编辑和一个推送按钮小部件拖放到表单上。

1.  将第一个标签小部件的文本属性设置为`输入您的姓名`。

让我们不要更改第二个标签小部件的文本属性，并将其文本属性保持为默认值`TextLabel`。这是因为它的文本属性将通过代码设置以显示 hello 消息。

1.  将推送按钮小部件的文本属性设置为`Click`。

1.  将 LineEdit 小部件的 objectName 属性设置为`lineEditName`。

1.  将 Label 小部件的 objectName 属性设置为`labelResponse`。

1.  将 Push Button 小部件的 objectName 属性设置为`ButtonClickMe`。

1.  将应用程序保存为名称为`LineEditClass.ui`的应用程序。应用程序将显示如下屏幕截图所示：

![](img/8899b4c1-7dfa-4483-8ceb-5e17f7e60834.png)

使用 Qt Designer 创建的用户界面存储在`.ui`文件中，这是一个 XML 文件，需要转换为 Python 代码。

1.  要进行转换，您需要打开命令提示符窗口，导航到保存文件的文件夹，并发出以下命令行：

```py
C:\Pythonbook\PyQt5>pyuic5 LineEdit.uiClass -o LineEditClass.py
```

可以在本书的源代码包中看到生成的 Python 脚本`LineEditClass.py`。

1.  将上述代码视为头文件，并将其导入到将调用其用户界面设计的文件中。

1.  创建另一个名为`callLineEditClass.pyw`的 Python 文件，并将`LineEditClass.py`代码导入其中：

```py
import sys
from PyQt5.QtWidgets import QDialog, QApplication
from LineEditClass import *
class Student:
    name = ""
    def __init__(self, name):
        self.name = name
    def printName(self):
        return self.name
class MyForm(QDialog):
    def __init__(self):
        super().__init__()
        self.ui = Ui_Dialog()
        self.ui.setupUi(self)
        self.ui.ButtonClickMe.clicked.connect(self.dispmessage)
        self.show()
    def dispmessage(self):
        studentObj=Student(self.ui.lineEditName.text())
        self.ui.labelResponse.setText("Hello 
        "+studentObj.printName())
if __name__=="__main__":
    app = QApplication(sys.argv)
    w = MyForm()
    w.show()
    sys.exit(app.exec_())
```

# 它是如何工作的...

在`LineEditClass.py`文件中，创建了一个名为顶级对象的类，其名称为`Ui_ prepended`。也就是说，对于顶级对象`Dialog`，创建了`Ui_Dialog`类，并存储了小部件的接口元素。该类有两个方法，`setupUi()`和`retranslateUi()`。`setupUi()`方法创建了在 Qt Designer 中定义用户界面时使用的小部件。此方法还设置了小部件的属性。`setupUi()`方法接受一个参数，即应用程序的顶级小部件，即`QDialog`的实例。`retranslateUi()`方法翻译了界面。

在`callLineEditClass.py`文件中，可以看到定义了一个名为`Student`的类。`Student`类包括一个名为`name`的类变量和以下两个方法：

+   `__init__()`: 这是一个构造函数，它接受强制的`self`参数和一个`name`参数，该参数将用于初始化`name`类变量

+   `printName`: 此方法简单地返回名称类变量中的值

将 Push Button 小部件的`clicked()`事件连接到`dispmessage()`方法；在 LineEdit 小部件中输入名称后，当用户单击按钮时，将调用`dispmessage()`方法。`dispmessage()`方法通过名称定义了`Student`类的对象，`studentObj`，并将用户在 LineEdit 小部件中输入的名称作为参数传递。因此，将调用`Student`类的构造函数，并将用户输入的名称传递给构造函数。在 LineEdit 小部件中输入的名称将被分配给类变量`name`。之后，名为`labelResponse`的 Label 小部件将设置为显示字符串`Hello`，并调用`Student`类的`printName`方法，该方法返回分配给名称变量的字符串。

因此，单击按钮后，Label 小部件将设置为显示字符串`Hello`，然后是用户在 LineEdit 框中输入的名称，如下面的屏幕截图所示：

![](img/4087bf43-9f4a-438f-9afc-3896bed1d43a.png)

# 使应用程序更加详细

我们还可以在类中使用两个或更多类属性。

假设除了类名`Student`之外，我们还想将学生的代码添加到类中。在这种情况下，我们需要向类中添加一个名为`code`的属性，还需要一个`getCode()`方法，该方法将访问分配的学生代码。除了类之外，GUI 也将发生变化。

我们需要向应用程序添加一个以上的 Label 小部件和一个 LineEdit 小部件，并将其保存为另一个名称`demoStudentClass`。添加 Label 和 LineEdit 小部件后，用户界面将显示如下屏幕截图所示：

![](img/a7049b4f-3d2b-444c-9acd-db786bb48423.png)

用户界面文件`demoStudentClass.ui`需要转换为 Python 代码。可以在本书的源代码包中看到生成的 Python 脚本`demoStudentClass.py`。

让我们创建另一个名为`callStudentClass.pyw`的 Python 文件，并将`demoStudentClass.py`代码导入其中。`callStudentClass.pyw`中的代码如下：

```py
import sys
from PyQt5.QtWidgets import QDialog, QApplication
from demoStudentClass import *
class Student:
    name = ""
    code = ""
    def __init__(self, code, name):
        self.code = code
        self.name = name
    def getCode(self):
        return self.code
    def getName(self):
        return self.name
class MyForm(QDialog):
    def __init__(self):
        super().__init__()
        self.ui = Ui_Dialog()
        self.ui.setupUi(self)
        self.ui.ButtonClickMe.clicked.connect(self.dispmessage)
        self.show()
    def dispmessage(self):
        studentObj=Student(self.ui.lineEditCode.text(),             
        self.ui.lineEditName.text())
        self.ui.labelResponse.setText("Code: 
        "+studentObj.getCode()+", Name:"+studentObj.getName())
if __name__=="__main__":
    app = QApplication(sys.argv)
    w = MyForm()
    w.show()
    sys.exit(app.exec_())
```

在上述代码中，您可以看到定义了一个名为`Student`的类。`Student`类包括两个名为`name`和`code`的类变量。除了这两个类变量，`Student`类还包括以下三个方法：

+   `__init__()`: 这是一个构造函数，它接受强制的`self`参数和两个参数，`code`和`name`，它们将用于初始化两个类变量，`code`和`name`

+   `getCode()`: 该方法简单地返回`code`类变量中的值

+   `getName()`: 该方法简单地返回`name`类变量中的值

推按钮小部件的`clicked()`事件连接到`dispmessage()`方法；在行编辑小部件中输入代码和名称后，用户单击推按钮，将调用`dispmessage()`方法。`dispmessage()`方法通过名称定义`Student`类的对象，`studentObj`，并将用户在行编辑小部件中输入的代码和名称作为参数传递。`Student`类的构造函数`__init__()`将被调用，并将用户输入的代码和名称传递给它。输入的代码和名称将分别分配给类变量 code 和 name。之后，标签小部件称为`labelResponse`被设置为通过`Student`类的`studentObj`对象调用两个方法`getCode`和`getName`显示输入的代码和名称。

因此，单击推按钮后，标签小部件将显示用户在两个行编辑小部件中输入的代码和名称，如下截图所示：

![](img/e9877017-e033-432c-a3c9-fd5b0d47c65f.png)

# 继承

继承是一个概念，通过该概念，现有类的方法和变量可以被另一个类重用，而无需重新编写它们。也就是说，经过测试和运行的现有代码可以立即在其他类中重用。

# 继承的类型

以下是三种继承类型：

+   **单一继承**: 一个类继承另一个类

+   **多级继承**: 一个类继承另一个类，而后者又被另一个类继承

+   **多重继承**: 一个类继承两个或更多个类

# 使用单一继承

单一继承是最简单的继承类型，其中一个类从另一个单一类派生，如下图所示：

![](img/a30a06b6-5b72-4811-863e-2597433190bf.png)

类**B**继承类**A**。在这里，类**A**将被称为超类或基类，类**B**将被称为派生类或子类。

以下语句定义了单一继承，其中`Marks`类继承了`Student`类：

```py
class Marks(Student):
```

在上述语句中，`Student`是基类，`Marks`是派生类。因此，`Marks`类的实例可以访问`Student`类的方法和变量。

# 准备就绪

为了通过一个运行示例理解单一继承的概念，让我们创建一个应用程序，提示用户输入学生的代码、名称、历史和地理成绩，并在单击按钮时显示它们。

用户输入的代码和名称将被分配给名为`Student`的类的类成员。历史和地理成绩将被分配给名为`Marks`的另一个类的类成员。

为了访问代码和名称，以及历史和地理成绩，`Marks`类将继承`Student`类。使用继承，`Marks`类的实例将访问并显示`Student`类的代码和名称。

# 如何做...

启动 Qt Designer，并根据以下步骤创建一个基于无按钮对话框模板的新应用程序：

1.  在应用程序中，将五个标签小部件、四个行编辑小部件和一个按钮小部件拖放到表单上。

1.  将四个标签小部件的文本属性设置为`学生代码`，`学生姓名`，`历史成绩`和`地理成绩`。

1.  删除第五个标签小部件的文本属性，因为它的文本属性将通过代码设置以显示代码、名称、历史和地理成绩。

1.  将按钮小部件的文本属性设置为`点击`。

1.  将四个行编辑小部件的 objectName 属性设置为`lineEditCode`，`lineEditName`，`lineEditHistoryMarks`和`lineEditGeographyMarks`。

1.  将标签小部件的 objectName 属性设置为`labelResponse`，将按钮小部件的 objectName 属性设置为`ButtonClickMe`。

1.  使用名称`demoSimpleInheritance.ui`保存应用程序。应用程序将显示如下截图所示：

![](img/ae5f11dc-7444-41b8-bbb6-8ee75483b7ac.png)

用户界面文件`demoSimpleInheritance.ui`是一个 XML 文件，并使用`pyuic5`实用程序转换为 Python 代码。您可以在本书的源代码包中找到生成的 Python 脚本`demoSimpleInheritance.py`。上述代码将被用作头文件，并将被导入到另一个 Python 脚本文件中，该文件将调用在`demoSimpleInheritance.py`文件中定义的用户界面设计。

1.  创建另一个名为`callSimpleInheritance.pyw`的 Python 文件，并将`demoSimpleInheritance.py`代码导入其中。Python 脚本`callSimpleInheritance.pyw`中的代码如下所示：

```py
import sys
from PyQt5.QtWidgets import QDialog, QApplication
from demoSimpleInheritance import *
class Student:
    name = ""
    code = ""
    def __init__(self, code, name):
        self.code = code
        self.name = name
    def getCode(self):
        return self.code
    def getName(self):
        return self.name
class Marks(Student):
    historyMarks = 0
    geographyMarks = 0
    def __init__(self, code, name, historyMarks, 
    geographyMarks):
        Student.__init__(self,code,name)
        self.historyMarks = historyMarks
        self.geographyMarks = geographyMarks
    def getHistoryMarks(self):
        return self.historyMarks
    def getGeographyMarks(self):
        return self.geographyMarks
class MyForm(QDialog):
    def __init__(self):
        super().__init__()
        self.ui = Ui_Dialog()
        self.ui.setupUi(self)
        self.ui.ButtonClickMe.clicked.connect(self.dispmessage)
        self.show()
    def dispmessage(self):
        marksObj=Marks(self.ui.lineEditCode.text(),                           
        self.ui.lineEditName.text(), 
        self.ui.lineEditHistoryMarks.text(),     
        self.ui.lineEditGeographyMarks.text())
        self.ui.labelResponse.setText("Code:     
        "+marksObj.getCode()+", Name:"+marksObj.getName()+"
        nHistory Marks:"+marksObj.getHistoryMarks()+", Geography         
        Marks:"+marksObj.getGeographyMarks())
if __name__=="__main__":
    app = QApplication(sys.argv)
    w = MyForm()
    w.show()
    sys.exit(app.exec_())
```

# 它是如何工作的...

在这段代码中，您可以看到定义了一个名为`Student`的类。`Student`类包括两个名为`name`和`code`的类变量，以及以下三个方法：

+   `__init__()`: 这是一个构造函数，它接受强制的`self`参数和两个参数，`code`和`name`，这些参数将用于初始化两个类变量，`code`和`name`

+   `getCode()`: 这个方法简单地返回`code`类变量中的值

+   `getName()`: 这个方法简单地返回`name`类变量中的值

`Marks`类继承了`Student`类。因此，`Marks`类的实例不仅能够访问自己的成员，还能够访问`Student`类的成员。

`Marks`类包括两个名为`historyMarks`和`geographyMarks`的类变量，以及以下三个方法：

+   `__init__()`: 这是一个构造函数，它接受强制的`self`参数和四个参数，`code`，`name`，`historyMarks`和`geographyMarks`。从这个构造函数中，将调用`Student`类的构造函数，并将`code`和`name`参数传递给这个构造函数。`historyMarks`和`geographyMarks`参数将用于初始化类成员`historyMarks`和`geographyMarks`。

+   `getHistoryMarks()`: 这个方法简单地返回`historyMarks`类变量中的值。

+   `getGeographyMarks()`: 这个方法简单地返回`geographyMarks`类变量中的值。

按钮的`clicked()`事件连接到`dispmessage()`方法。在 Line Edit 小部件中输入代码、姓名、历史和地理成绩后，用户单击按钮时，将调用`dispmessage()`方法。`dispmessage()`方法通过名称定义了`Marks`类的对象`marksObj`，并将用户在 Line Edit 小部件中输入的代码、姓名、历史和地理成绩作为参数传递。`Marks`类的构造函数`__init__()`将被调用，并将用户输入的代码、姓名、历史和地理成绩传递给它。从`Marks`类的构造函数中，将调用`Student`类的构造函数，并将`code`和`name`传递给该构造函数。`code`和`name`参数将分别分配给`Student`类的`code`和`name`类变量。

类似地，历史和地理成绩将分配给`Marks`类的`historyMarks`和`geographyMarks`类变量。之后，将设置名为`labelResponse`的 Label 小部件，以通过调用四个方法`getCode`、`getName`、`getHistoryMarks`和`getGeographyMarks`来显示用户输入的代码、姓名、历史和地理成绩。通过`marksObj`对象，`Marks`类的`marksObj`对象获得了访问`Student`类的`getCode`和`getName`方法的权限，因为使用了继承。

因此，单击按钮后，Label 小部件将通过名为`labelResponse`的 Label 小部件显示用户输入的代码、姓名、历史成绩和地理成绩，如下图所示：

![](img/85fd758c-d18c-4054-82f6-e71290dc9ff3.png)

# 使用多级继承

多级继承是指一个类继承另一个单一类。转而继承第三个类，如下图所示：

![](img/60be84f7-6eaa-49f2-b0bb-7f9adc6f8627.png)

在上图中，您可以看到类**B**继承了类**A**，而类**C**又继承了类**B**。

以下语句定义了多级继承，其中`Result`类继承了`Marks`类，而`Marks`类又继承了`Student`类：

```py
class Student:
    class Marks(Student):
        class Result(Marks):
```

在上述语句中，`Student`是基类，`Marks`类继承了`Student`类。`Result`类继承了`Marks`类。因此，`Result`类的实例可以访问`Marks`类的方法和变量，而`Marks`类的实例可以访问`Student`类的方法和变量。

# 准备就绪

为了理解多级继承的概念，让我们创建一个应用程序，提示用户输入学生的代码、姓名、历史成绩和地理成绩，并在单击按钮时显示总分和百分比。总分将是历史成绩和地理成绩的总和。假设最高分为 100，计算百分比的公式为：总分/200 * 100。

用户输入的代码和姓名将分配给名为`Student`的类的类成员。历史和地理成绩将分配给名为`Marks`的另一个类的类成员。

为了访问代码和姓名以及历史和地理成绩，`Marks`类将继承`Student`类。

使用这种多层继承，`Marks`类的实例将访问`Student`类的代码和名称。为了计算总分和百分比，还使用了一个名为`Result`的类。`Result`类将继承`Marks`类。因此，`Result`类的实例可以访问`Marks`类的类成员，以及`Student`类的成员。`Result`类有两个类成员，`totalMarks`和`percentage`。`totalMarks`类成员将被分配为`Marks`类的`historyMarks`和`geographyMarks`成员的总和。百分比成员将根据历史和地理成绩获得的百分比进行分配。

# 如何做到...

总之，有三个类，名为`Student`，`Marks`和`Result`，其中`Result`类将继承`Marks`类，而`Marks`类将继承`Student`类。因此，`Result`类的成员可以访问`Marks`类的类成员以及`Student`类的成员。以下是创建此应用程序的逐步过程：

1.  启动 Qt Designer 并基于无按钮模板创建一个新应用程序。

1.  将六个 Label 小部件、六个 Line Edit 小部件和一个 Push Button 小部件拖放到表单上。

1.  将六个 Label 小部件的文本属性设置为`Student Code`、`Student Name`、`History Marks`、`Geography Marks`、`Total`和`Percentage`。

1.  将 Push Button 小部件的文本属性设置为`Click`。

1.  将六个 Line Edit 小部件的对象名称属性设置为`lineEditCode`、`lineEditName`、`lineEditHistoryMarks`、`lineEditGeographyMarks`、`lineEditTotal`和`lineEditPercentage`。

1.  将 Push Button 小部件的对象名称属性设置为`ButtonClickMe`。

1.  通过取消选中属性编辑器窗口中的启用属性，禁用`lineEditTotal`和`lineEditPercentage`框。`lineEditTotal`和`lineEditPercentage`小部件被禁用，因为这些框中的值将通过代码分配，我们不希望用户更改它们的值。

1.  使用名称`demoMultilevelInheritance.ui`保存应用程序。应用程序将显示如下截图所示：

![](img/6e59be73-0f7c-455e-a947-bfb9c7883a3a.png)

用户界面文件`demoMultilevelInheritance.ui`是一个 XML 文件，并通过使用`pyuic5`实用程序将其转换为 Python 代码。您可以在本书的源代码包中看到生成的 Python 脚本`demoMultilevelInheritance.py`。`demoMultilevelInheritance.py`文件将用作头文件，并将在另一个 Python 脚本文件中导入，该文件将使用在`demoMultilevelInheritance.py`中创建的 GUI。

1.  创建另一个名为`callMultilevelInheritance.pyw`的 Python 文件，并将`demoMultilevelInheritance.py`代码导入其中。Python 脚本`callMultilevelInheritance.pyw`中的代码如下所示：

```py
import sys
from PyQt5.QtWidgets import QDialog, QApplication
from demoMultilevelInheritance import *
class Student:
    name = ""
    code = ""
    def __init__(self, code, name):
        self.code = code
        self.name = name
    def getCode(self):
        return self.code
    def getName(self):
        return self.name
class Marks(Student):
    historyMarks = 0
    geographyMarks = 0
    def __init__(self, code, name, historyMarks, 
    geographyMarks):
        Student.__init__(self,code,name)
        self.historyMarks = historyMarks
        self.geographyMarks = geographyMarks
    def getHistoryMarks(self):
        return self.historyMarks
    def getGeographyMarks(self):
        return self.geographyMarks
class Result(Marks):
    totalMarks = 0
    percentage = 0
    def __init__(self, code, name, historyMarks, 
    geographyMarks):
        Marks.__init__(self, code, name, historyMarks, 
        geographyMarks)
        self.totalMarks = historyMarks + geographyMarks
        self.percentage = (historyMarks + 
        geographyMarks) / 200 * 100
    def getTotalMarks(self):
        return self.totalMarks
    def getPercentage(self):
        return self.percentage
class MyForm(QDialog):
    def __init__(self):
        super().__init__()
        self.ui = Ui_Dialog()
        self.ui.setupUi(self)
        self.ui.ButtonClickMe.clicked.connect(self.dispmessage)
        self.show()
    def dispmessage(self):
        resultObj=Result(self.ui.lineEditCode.text(),                      
        self.ui.lineEditName.text(),           
        int(self.ui.lineEditHistoryMarks.text()),      
        int(self.ui.lineEditGeographyMarks.text()))
        self.ui.lineEditTotal.setText(str(resultObj.
        getTotalMarks()))
        self.ui.lineEditPercentage.setText(str(resultObj.
        getPercentage()))
if __name__=="__main__":
    app = QApplication(sys.argv)
    w = MyForm()
    w.show()
    sys.exit(app.exec_())
```

# 工作原理...

在上述代码中，在`callMultilevelInheritance.pyw`文件中，您可以看到定义了一个名为`Student`的类。`Student`类包括两个名为`name`和`code`的类变量，以及以下三种方法：

+   `__init__()`: 这是一个构造函数，它接受强制的`self`参数和两个参数，`code`和`name`，用于初始化两个类变量`code`和`name`

+   `getCode()`: 此方法简单地返回`code`类变量中的值

+   `getName()`: 此方法简单地返回`name`类变量中的值

`Marks`类继承`Student`类。因此，`Marks`类的实例不仅能够访问自己的成员，还能够访问`Student`类的成员。

`Marks`类包括两个名为`historyMarks`和`geographyMarks`的类变量，以及以下三种方法：

+   `__init__()`: 这是一个构造函数，它接受强制的`self`参数和四个参数，`code`、`name`、`historyMarks`和`geographyMarks`。从这个构造函数中，将调用`Student`类的构造函数，并将`code`和`name`参数传递给该构造函数。`historyMarks`和`geographyMarks`参数将用于初始化`historyMarks`和`geographyMarks`类成员。

+   `getHistoryMarks()`: 此方法简单地返回`historyMarks`类变量中的值。

+   `getGeographyMarks()`: 此方法简单地返回`geographyMarks`类变量中的值。

`Result`类继承了`Marks`类。`Result`类的实例不仅能够访问自己的成员，还能访问`Marks`类和`Student`类的成员。

`Result`类包括两个类变量，称为`totalMarks`和`percentage`，以及以下三个方法：

+   `__init__()`: 这是一个构造函数，它接受强制的`self`参数和四个参数，`code`、`name`、`historyMarks`和`geographyMarks`。从这个构造函数中，将调用`Marks`类的构造函数，并将`code`、`name`、`historyMarks`和`geographyMarks`参数传递给该构造函数。`historyMarks`和`geographyMarks`的总和将被赋给`totalMarks`类变量。假设每个的最高分为 100，将计算历史和地理成绩的百分比，并将其分配给百分比类变量。

+   `getTotalMarks()`: 此方法简单地返回`historyMarks`和`geographyMarks`类变量的总和。

+   `getPercentage()`: 此方法简单地返回历史和地理成绩的百分比。

按钮小部件的`clicked()`事件连接到`dispmessage()`方法。在行编辑小部件中输入代码、姓名、历史成绩和地理成绩后，用户单击按钮，将调用`dispmessage()`方法。`dispmessage()`方法通过姓名`resultObj`定义`Result`类的对象，并将用户在行编辑小部件中输入的代码、姓名、历史和地理成绩作为参数传递。`Result`类的构造函数`__init__()`将被调用，并将用户输入的代码、姓名、历史成绩和地理成绩传递给它。从`Result`类的构造函数中，将调用`Marks`类的构造函数，并将代码、姓名、历史成绩和地理成绩传递给该构造函数。从`Marks`类的构造函数中，将调用`Student`类的构造函数，并将`code`和`name`参数传递给它。在`Student`类的构造函数中，`code`和`name`参数将分配给类变量`code`和`name`。类似地，历史和地理成绩将分配给`Marks`类的`historyMarks`和`geographyMarks`类变量。

`historyMarks`和`geographyMarks`的总和将被赋给`totalMarks`类变量。此外，历史和地理成绩的百分比将被计算并赋给`percentage`类变量。

之后，称为`lineEditTotal`的行编辑小部件被设置为通过`resultObj`调用`getTotalMarks`方法来显示总分，即历史和地理成绩的总和。同样，称为`lineEditPercentage`的行编辑小部件被设置为通过`resultObj`调用`getPercentage`方法来显示百分比。

因此，单击按钮后，称为`lineEditTotal`和`lineEditPercentage`的行编辑小部件将显示用户输入的历史和地理成绩的总分和百分比，如下截图所示：

![](img/d449d2e5-b4df-4d4b-83de-7453ccab8863.png)

# 使用多重继承

多重继承是指一个类继承了两个或更多个类，如下图所示：

![](img/18fd865e-1ed7-4668-8579-ecb61a333042.png)

类**C**同时继承类**A**和类**B**。

以下语句定义了多级继承，其中`Result`类继承了`Marks`类，而`Marks`类又继承了`Student`类：

```py
class Student:
    class Marks:
        class Result(Student, Marks):
```

在前面的语句中，`Student`和`Marks`是基类，`Result`类继承了`Student`类和`Marks`类。因此，`Result`类的实例可以访问`Marks`和`Student`类的方法和变量。

# 准备就绪

为了实际理解多级继承的概念，让我们创建一个应用程序，提示用户输入学生的代码、姓名、历史成绩和地理成绩，并在单击按钮时显示总分和百分比。总分将是历史成绩和地理成绩的总和。假设每个的最高分为 100，计算百分比的公式为：总分/200 * 100。

用户输入的代码和姓名将分配给一个名为`Student`的类的类成员。历史和地理成绩将分配给另一个名为`Marks`的类的类成员。

为了访问代码和姓名，以及历史和地理成绩，`Result`类将同时继承`Student`类和`Marks`类。使用这种多重继承，`Result`类的实例可以访问`Student`类的代码和姓名，以及`Marks`类的`historyMarks`和`geographyMarks`类变量。换句话说，使用多重继承，`Result`类的实例可以访问`Marks`类的类成员，以及`Student`类的类成员。`Result`类有两个类成员，`totalMarks`和`percentage`。`totalMarks`类成员将被分配为`Marks`类的`historyMarks`和`geographyMarks`成员的总和。百分比成员将根据历史和地理成绩的基础上获得的百分比进行分配。

# 如何做...

让我们通过逐步过程来了解多级继承如何应用于三个类，`Student`，`Marks`和`Result`。`Result`类将同时继承`Student`和`Marks`两个类。这些步骤解释了`Result`类的成员如何通过多级继承访问`Student`和`Marks`类的类成员：

1.  启动 Qt Designer，并基于无按钮的对话框模板创建一个新应用程序。

1.  在应用程序中，将六个标签小部件、六个行编辑小部件和一个按钮小部件拖放到表单上。

1.  将六个标签小部件的文本属性设置为`学生代码`，`学生姓名`，`历史成绩`，`地理成绩`，`总分`和`百分比`。

1.  将按钮小部件的文本属性设置为`点击`。

1.  将六个行编辑小部件的 objectName 属性设置为`lineEditCode`，`lineEditName`，`lineEditHistoryMarks`，`lineEditGeographyMarks`，`lineEditTotal`和`lineEditPercentage`。

1.  将按钮小部件的 objectName 属性设置为`ButtonClickMe`。

1.  通过取消选中属性编辑器窗口中的启用属性，禁用`lineEditTotal`和`lineEditPercentage`框。`lineEditTotal`和`lineEditPercentage`框被禁用，因为这些框中的值将通过代码分配，并且我们不希望用户更改它们的值。

1.  使用名称`demoMultipleInheritance.ui`保存应用程序。应用程序将显示如下截图所示：

![](img/a27d6894-3d5e-4a3d-a2af-f12757b4dc3e.png)

用户界面文件`demoMultipleInheritance .ui`是一个 XML 文件，并使用`pyuic5`实用程序转换为 Python 代码。您可以在本书的源代码包中找到生成的 Python 代码`demoMultipleInheritance.py`。`demoMultipleInheritance.py`文件将被用作头文件，并将在另一个 Python 脚本文件中导入，该文件将调用在`demoMultipleInheritance.py`文件中创建的 GUI。

1.  创建另一个名为`callMultipleInheritance.pyw`的 Python 文件，并将`demoMultipleInheritance.py`代码导入其中：

```py
import sys
from PyQt5.QtWidgets import QDialog, QApplication
from demoMultipleInheritance import *
class Student:
    name = ""
    code = ""
    def __init__(self, code, name):
        self.code = code
        self.name = name
    def getCode(self):
        return self.code
    def getName(self):
        return self.name
class Marks:
    historyMarks = 0
    geographyMarks = 0
    def __init__(self, historyMarks, geographyMarks):
        self.historyMarks = historyMarks
        self.geographyMarks = geographyMarks
    def getHistoryMarks(self):
        return self.historyMarks
    def getGeographyMarks(self):
        return self.geographyMarks
class Result(Student, Marks):
    totalMarks = 0
    percentage = 0
    def __init__(self, code, name, historyMarks, 
    geographyMarks):
        Student.__init__(self, code, name)
        Marks.__init__(self, historyMarks, geographyMarks)
        self.totalMarks = historyMarks + geographyMarks
        self.percentage = (historyMarks + 
        geographyMarks) / 200 * 100
    def getTotalMarks(self):
        return self.totalMarks
    def getPercentage(self):
        return self.percentage
class MyForm(QDialog):
    def __init__(self):
        super().__init__()
        self.ui = Ui_Dialog()
        self.ui.setupUi(self)
        self.ui.ButtonClickMe.clicked.connect(self.dispmessage)
        self.show()
    def dispmessage(self):
        resultObj=Result(self.ui.lineEditCode.text(),         
        self.ui.lineEditName.text(),
        int(self.ui.lineEditHistoryMarks.text()),  
        int(self.ui.lineEditGeographyMarks.text()))
        self.ui.lineEditTotal.setText(str(resultObj.
        getTotalMarks()))
        self.ui.lineEditPercentage.setText(str(resultObj.
        getPercentage()))
if __name__=="__main__":
    app = QApplication(sys.argv)
    w = MyForm()
    w.show()
    sys.exit(app.exec_())
```

# 工作原理...

在这段代码中，您可以看到定义了一个名为`Student`的类。`Student`类包括两个名为`name`和`code`的类变量，以及以下三种方法：

+   `__init__()`: 这是一个构造函数，它接受强制的`self`参数和两个参数，`code`和`name`，这些参数将用于初始化两个类变量`code`和`name`。

+   `getCode()`: 此方法简单地返回`code`类变量中的值

+   `getName()`: 此方法简单地返回`name`类变量中的值

`Marks`类包括两个类变量，名为`historyMarks`和`geographyMarks`，以及以下三种方法：

+   `__init__()`: 这是一个构造函数，它接受强制的`self`参数和两个参数，`historyMarks`和`geographyMarks`。`historyMarks`和`geographyMarks`参数将用于初始化`historyMarks`和`geographyMarks`类成员。

+   `getHistoryMarks()`: 此方法简单地返回`historyMarks`类变量中的值。

+   `getGeographyMarks()`: 此方法简单地返回`geographyMarks`类变量中的值。

`Result`类继承了`Student`类以及`Marks`类。`Result`类的实例不仅能够访问自己的成员，还能够访问`Marks`类和`Student`类的成员。

`Result`类包括两个名为`totalMarks`和`percentage`的类变量，以及以下三种方法：

+   `__init__()`: 这是一个构造函数，它接受强制的`self`参数和四个参数，`code`、`name`、`historyMarks`和`geographyMarks`。从这个构造函数中，将调用`Student`类的构造函数，并将`code`和`name`参数传递给该构造函数。同样，从这个构造函数中，将调用`Marks`类的构造函数，并将`historyMarks`和`geographyMarks`参数传递给该构造函数。`historyMarks`和`geographyMarks`的总和将被分配给`totalMarks`类变量。假设每个的最高分数为 100，将计算历史和地理成绩的百分比，并将其分配给`percentage`类变量。

+   `getTotalMarks()`: 此方法简单地返回`historyMarks`和`geography`类变量的总和。

+   `getPercentage()`: 此方法简单地返回历史和地理成绩的百分比。

按钮小部件的`clicked()`事件连接到`dispmessage()`方法。在 LineEdit 小部件中输入代码、名称、历史成绩和地理成绩后，当用户单击按钮时，将调用`dispmessage()`方法。`dispmessage()`方法通过名称定义了`Result`类的对象，`resultObj`，并将用户在 LineEdit 小部件中输入的代码、名称、历史成绩和地理成绩作为参数传递。将调用`Result`类的构造函数`__init__()`，并将用户输入的代码、名称、历史成绩和地理成绩传递给它。从`Result`类的构造函数中，将调用`Student`类的构造函数和`Marks`类的构造函数。代码和名称将传递给`Student`类的构造函数，历史和地理成绩将传递给`Marks`类的构造函数。

在`Student`类构造函数中，代码和名称将分配给`code`和`name`类变量。同样，在`Marks`类构造函数中，历史和地理成绩将分配给`Marks`类的`historyMarks`和`geographyMarks`类变量。

`historyMarks`和`geographyMarks`的总和将分配给`totalMarks`类变量。此外，历史和地理成绩的百分比将计算并分配给`percentage`类变量。

之后，LineEdit 小部件称为`lineEditTotal`被设置为通过`resultObj`调用`getTotalMarks`方法来显示总分，即历史和地理成绩的总和。同样，LineEdit 小部件称为`lineEditPercentage`被设置为通过`resultObj`调用`getPercentage`方法来显示百分比。

因此，单击按钮后，LineEdit 小部件称为`lineEditTotal`和`lineEditPercentage`将显示用户输入的历史和地理成绩的总分和百分比，如下面的屏幕截图所示：

![](img/e3d7ca1c-8089-43bc-b7ee-b4391dce7d38.png)
