# 使用样式和主题改进外观

虽然程序可以完全使用黑色、白色和灰色的纯文本进行功能性操作，但是对颜色、字体和图像的微妙使用可以增强视觉吸引力和可用性，即使是最实用的应用程序也是如此。您的数据输入应用程序也不例外。您的老板和用户已经向您提出了几个问题，似乎需要使用Tkinter的样式功能。您的老板已经告诉您，总部要求所有内部软件突出显示公司标志，而数据输入人员已经提到了应用程序的可读性和整体外观的各种问题。

在本章中，我们将学习一些Tkinter的功能，这些功能将帮助我们解决这些问题：

+   我们将学习如何向我们的Tkinter GUI添加图像

+   我们将学习如何调整Tkinter小部件的字体和颜色，直接和使用标签

+   我们将学习如何使用样式和主题调整Ttk小部件的外观

# 在Tkinter中使用图像

我们将处理的第一个要求是添加公司标志。由于公司政策的结果，您的应用程序应该嵌入公司标志，并且如果可能的话，您已被要求使您的应用程序符合要求。

要将此图像添加到我们的应用程序中，您需要了解Tkinter的`PhotoImage`类。

# Tkinter PhotoImage

包括`Label`和`Button`在内的几个Tkinter小部件可以接受`image`参数，从而允许它们显示图像。在这些情况下，我们不能简单地将图像文件的路径放入其中；相反，我们必须创建一个`PhotoImage`对象。

创建`PhotoImage`对象相当简单：

```py
myimage = tk.PhotoImage(file='my_image.png')
```

通常使用关键字参数`file`调用`PhotoImage`，该参数指向文件路径。或者，您可以使用`data`参数指向包含图像数据的`bytes`对象。

`PhotoImage`可以在接受`image`参数的任何地方使用，例如`Label`：

```py
mylabel = tk.Label(root, image=myimage)
```

必须注意的是，您的应用程序必须保留对`PhotoImage`对象的引用，该引用将在图像显示期间保持在范围内；否则，图像将不会显示。

考虑以下示例：

```py
import tkinter as tk
class App(tk.Tk):
    def __init__(self):
        super().__init__()
        smile = tk.PhotoImage(file='smile.gif')
        tk.Label(self, image=smile).pack()
App().mainloop()
```

如果您运行此示例，您会注意到没有显示任何图像。这是因为`smile`变量在`__init__()`退出时立即被销毁；由于没有对`PhotoImage`对象的引用，图像消失了，即使我们已经将其打包到布局中。要解决此问题，您需要将`image`对象存储在实例变量中，例如`self.smile`，这样在方法返回后它将继续存在。

Tkinter中的图像支持仅限于GIF、PGM、PPM和（从版本8.6开始）PNG文件。要使用其他文件格式，如JPEG或BMP，您需要使用图像处理库（如Pillow）将它们转换为Tkinter可以理解的格式。

在撰写本文时，macOS的Python 3附带Tkinter 8.5。要在macOS上使用PNG，您需要升级到Tkinter 8.6或更高版本，或者使用Pillow。有关Tcl/Tk和macOS的更多信息，请参见[https://www.python.org/download/mac/tcltk/](https://www.python.org/download/mac/tcltk/)。Pillow不在Python标准库中。要安装它，请按照[http://python-pillow.org](http://python-pillow.org)上的说明操作。

# 添加公司标志

凭借我们对`PhotoImage`的了解，将公司标志添加到我们的程序应该很简单；但是，我们必须解决如何确定图像文件的路径的问题。路径可以是绝对路径，也可以是相对于工作目录的路径，但我们不知道另一个系统上的路径是什么。幸运的是，有一种方法可以找出来。

在`abq_data_entry`目录下，创建一个名为`images`的新目录，在其中放置一个适当大小的PNG文件，我们可以在我们的应用程序中使用（图像具有8x5的宽高比，所以在这种情况下，我们使用`32x20`）。要获取图像的绝对路径，我们将依赖Python中一个名为`__file__`的内置变量。在任何Python脚本中，`__file__`变量将包含当前脚本文件的绝对路径，我们可以使用它来定位我们的图像文件。

例如，从我们的`application.py`文件中，我们可以使用这段代码找到我们的图像：

```py
from os import path
image_path = path.join(path.dirname(__file__),
                       'images/abq_logo_32x20.png')
```

在这个例子中，我们首先通过调用`path.dirname(__file__)`找到包含`application.py`文件的目录。这给了我们`abq_data_entry`的绝对路径，从中我们知道图像的相对路径。我们可以连接这两个路径，并获得图像的绝对路径，无论程序安装在文件系统的何处。

这种方法可以正常工作，但考虑到我们可能希望从应用程序的各种模块中访问图像，并且不得不在多个文件中导入`path`并重复这个逻辑，这不是最佳的。一个更干净的方法是将我们的`images`文件夹视为Python包，并在其中创建指向图像路径的常量。

首先，在`images`文件夹内创建一个`__init__.py`文件，并添加以下代码：

```py
from os import path

IMAGE_DIRECTORY = path.dirname(__file__)

ABQ_LOGO_32 = path.join(IMAGE_DIRECTORY, 'abq_logo-32x20.png')
ABQ_LOGO_64 = path.join(IMAGE_DIRECTORY, 'abq_logo-64x40.png')
```

现在，我们的`application.py`模块可以简单地这样做：

```py
from .images import ABQ_LOGO_32
```

`Application.__init__()`然后可以使用`ABQ_LOGO_32`中的路径创建一个`PhotoImage`对象：

```py
        self.logo = tk.PhotoImage(file=ABQ_LOGO_32)
        tk.Label(self, image=self.logo).grid(row=0)
```

创建`PhotoImage`对象后，我们使用`Label`显示它。如果你运行应用程序，你应该会看到标志出现在顶部。

# 设置我们的窗口图标

我们还可以将标志添加为我们的窗口图标，这比保留默认的Tkinter标志更有意义。这样，标志将显示在窗口装饰和操作系统的任务栏中。

作为Tk的子类，我们的`Application`对象有一个名为`iconbitmap`的方法，应该可以根据图标文件的路径设置图标。不幸的是，这个方法对于给定的文件类型非常挑剔，并且在各个平台上工作效果不佳。我们可以使用`PhotoImage`和特殊的Tk `call()`方法来解决这个问题。

`call`方法允许我们直接调用Tcl/Tk命令，并且可以用于访问Tkinter包装不好或根本不包装的Tk功能。

代码如下：

```py
        self.taskbar_icon = tk.PhotoImage(file=ABQ_LOGO_64)
        self.call('wm', 'iconphoto', self._w, self.taskbar_icon)
```

第一行创建了另一个`PhotoImage`对象，引用了一个更大的标志的版本。接下来，我们执行`self.call()`，传入Tcl/Tk命令的各个标记。在这种情况下，我们调用`wm iconphoto`命令。`self._w`返回我们的`Application`对象的Tcl/Tk名称；最后，我们传入我们创建的`PhotoImage`对象。

希望你不需要经常使用`call`，但如果你需要，你可以在这里找到有关Tcl/Tk命令的文档：[https://www.tcl.tk/doc/](https://www.tcl.tk/doc/) 。

运行你的应用程序，注意图标已经改变了。

# 样式化Tkinter小部件

Tkinter基本上有两种样式系统：旧的Tkinter小部件系统和更新的Ttk系统。由于我们仍然需要使用Tkinter和Ttk小部件，我们将不得不查看这两个系统。让我们首先看一下旧的Tkinter系统，并对我们应用程序中的Tkinter小部件应用一些样式。

# 小部件颜色属性

基本的Tkinter小部件允许你更改两种颜色：**前景**，主要是文本和边框，和**背景**，指其余的小部件。这些可以使用`foreground`和`background`参数，或它们的别名`fg`和`bg`来设置。

这个例子展示了在`Label`上使用颜色：

```py
l = tk.Label(text='Hot Dog!', fg='yellow', bg='red')
```

颜色的值可以是颜色名称字符串或CSS样式的RGB十六进制字符串。

例如，这段代码产生了相同的效果：

```py
l2 = tk.Label(text='Also Hot Dog!',
              foreground='#FFFF00',
              background='#FF0000')
```

Tkinter识别了700多种命名颜色，大致对应于Linux和Unix上使用的X11显示服务器或Web设计师使用的CSS命名颜色。有关完整列表，请参见[https://www.tcl.tk/man/tcl8.6/TkCmd/colors.htm](https://www.tcl.tk/man/tcl8.6/TkCmd/colors.htm)。

# 在我们的表单上使用小部件属性

数据输入人员收到的一个请求是增加数据输入表单上各个部分之间的视觉分隔。我们的`LabelFrame`小部件是简单的Tkinter小部件（不是Ttk），因此我们可以通过给各个部分设置有色的背景来相对简单地实现这一点。

经过一番思考和讨论，您决定按以下方式对各个部分进行颜色编码：

+   记录信息将使用`khaki`，表明用于纸质记录的经典马尼拉文件夹

+   环境信息将使用`lightblue`，象征着水和空气

+   植物信息将具有`lightgreen`背景，象征着植物

+   `Notes`已经足够与众不同，因此它将保持不变

打开`views.py`并编辑`DataRecordForm.__init__()`中的`LabelFrame`调用：

```py
        recordinfo = tk.LabelFrame(
            self, text="Record Information",
            bg="khaki", padx=10, pady=10)
...
        environmentinfo = tk.LabelFrame(
            self, text="Environment Data",
            bg='lightblue', padx=10, pady=10)
...
        plantinfo = tk.LabelFrame(
            self, text="Plant Data",
            bg="lightgreen", padx=10, pady=10)
```

请注意，我们在这里添加了一些填充，以使小部件周围的颜色更加明显，并在表单中创建更多的分隔。

我们应该在`Notes`小部件周围添加类似的填充：

```py
   self.inputs['Notes'].grid(sticky="w", row=4, column=0,
                             padx=10, pady=10)
```

在这种情况下，我们在`grid`调用中添加了填充，以便整个`LabelInput`被移动。

至少在Debian Linux上，结果看起来像这样：

![](assets/6133ca48-cf64-43e2-bcb9-19fa095453eb.png)

尽管还不是一个视觉杰作，但我们在表单各个部分之间有了一些分隔和颜色编码。

# 使用标签

前景和背景对于按钮等简单小部件已经足够了，但是像`Text`小部件或Ttk `Treeview`这样的更复杂的Tkinter小部件依赖于一种**标签**系统。在Tkinter中，标签是可以应用颜色和字体设置的小部件内容的命名区域。为了看看这是如何工作的，让我们构建一个粗糙但漂亮的Python shell。

我们将首先创建一个`Text`小部件：

```py
import tkinter as tk
text = tk.Text(width=50, height=20, bg='black', fg='lightgreen')
text.pack()
```

在这里，我们使用`fg`和`bg`参数设置了一个程序员喜欢的黑底绿字的主题。但是，我们不仅仅要绿色的文本，让我们为我们的提示和解释器输出配置不同的颜色。

为此，我们将定义一些标签：

```py
text.tag_configure('prompt', foreground='magenta')
text.tag_configure('output', foreground='yellow')
```

`tag_configure`方法允许我们在`Text`小部件上创建和配置标签。我们为我们的shell提示创建了一个名为`'prompt'`的标签，另一个名为`'output'`的标签。

要使用给定标签插入文本，我们需要执行以下操作：

```py
text.insert('end', '>>> ', ('prompt',))
```

正如您可能记得的那样，`Text.insert`方法将索引和字符串作为其前两个参数。请注意第三个参数：这是我们想要标记插入的文本的标签的元组。这个值必须是一个元组，即使您只使用一个标签。

如果您将`text.mainloop()`添加到代码的末尾并运行它，您会看到我们有一个黑色的文本输入窗口，带有品红色的提示，但如果您输入文本，它将显示为绿色。到目前为止一切顺利；现在，让我们让它执行一些Python。

在`mainloop()`调用之前创建一个函数：

```py
def on_return(*args):
    cmd = text.get('prompt.last', 'end').strip()
```

当从`Text`小部件中检索文本时，我们需要为我们想要检索的文本提供起始和结束索引。请注意，我们在索引中使用了我们的标签名称。`prompt.last`告诉Tkinter从标记为`prompt`的区域的结束之后开始获取文本。

接下来，让我们执行`cmd`：

```py
     if cmd:
        try:
            output = str(eval(cmd))
        except Exception as e:
            output = str(e)
```

如果我们的`cmd`实际上包含任何内容，我们将尝试使用`eval`执行它，然后将响应值的字符串存储为`output`。如果它抛出异常，我们将获得我们的异常作为字符串，并将其设置为`output`。

然后，我们只需显示我们的`output`：

```py
   text.insert('end', '\n' + output, ('output',))
```

我们插入我们的`output`文本，前面加上一个换行符，并标记为`output`。

我们将通过给用户返回一个`prompt`来完成该函数：

```py
    text.insert('end', '\n>>> ', ('prompt',))
    return 'break'
```

我们还在这里返回字符串`break`，告诉Tkinter忽略触发回调的原始事件。因为我们将从`Return`/`Enter`按键触发这个操作，所以我们希望在完成后忽略该按键。如果不这样做，按键将在我们的函数返回后执行，用户将在提示下的下一行。

最后，我们需要将我们的函数绑定到`Return`键：

```py
text.bind('<Return>', on_return)
```

请注意，`Enter`/`Return`键的事件始终是`<Return>`，即使在非苹果硬件上（该按键更常被标记为`Enter`）也是如此。

你的应用程序应该看起来像这样：

![](assets/db7df434-b620-4c9e-b45c-b34a34475830.png)

虽然这个shell不会很快取代IDLE，但它看起来确实很好看，你觉得呢？

# 使用标签样式化我们的记录列表

虽然`Treeview`是一个Ttk小部件，但它使用标签来控制单独行的样式。我们可以利用这一点来回答数据输入人员提出的另一个要求：他们希望记录列表突出显示在当前会话期间更新和插入的记录。

首先，我们需要做的是让我们的`Application`对象在会话期间跟踪哪些行已被更新或插入。

在`Application.__init__()`中，我们将创建以下实例变量：

```py
        self.inserted_rows.clear()
        self.updated_rows.clear()
```

当记录保存时，我们需要更新这些列表中的一个或另一个与其行号。我们将在`Application.on_save()`中完成这个操作，在记录保存后但在重新填充记录列表之前。

首先，我们将检查更新的记录：

```py
       if rownum is not None:
           self.updated_rows.append(rownum)
```

更新有`rownum`，没有`None`值，所以如果是这种情况，我们将把它追加到列表中。如果记录不断更新，我们的列表中会有重复，但在我们操作的规模上，这实际上并不重要。

现在，我们需要处理插入：

```py
       else:
           rownum = len(self.data_model.get_all_records()) - 1
           self.inserted_rows.append(rownum)
```

插入的记录会更麻烦一些，因为我们没有一个行号可以记录。不过，我们知道插入总是添加到文件的末尾，所以它应该比文件中的行数少一个。

我们的插入和更新记录将保留到程序会话结束（用户退出程序）时，但在用户选择新文件的情况下，我们需要手动删除它们。

我们可以通过在`on_file_select()`中清除列表来处理这个问题：

```py
       if filename:
            ...
            self.inserted_rows = []
            self.updated_rows = []
```

现在，我们的控制器知道插入和更新的记录。不过，我们的记录列表并不知道；我们需要解决这个问题。

在`Application.__init__()`中找到`RecordList`调用，并将这些变量添加到其参数中：

```py
       self.recordlist = v.RecordList(
            self, self.callbacks,
            self.inserted_rows,
            self.updated_rows)
```

现在，我们需要回到`views.py`，告诉`RecordList`如何处理这些信息。

我们将首先更新其参数列表并将列表保存到实例变量中：

```py
    def __init__(self, parent, callbacks,
                 inserted, updated,
                 *args, **kwargs):
        self.inserted = inserted
        self.updated = updated
```

接下来，我们需要配置适当颜色的标签。我们的数据输入人员认为`lightgreen`是插入记录的合理颜色，`lightblue`是更新记录的颜色。

在`__init__()`中添加以下代码，放在`self.treeview`配置之后：

```py
      self.treeview.tag_configure('inserted', background='lightgreen')
      self.treeview.tag_configure('updated', background='lightblue')
```

就像我们之前对`Text`小部件所做的那样，我们调用`tag_configure`来将背景颜色设置与我们的标签名称连接起来。请注意，这里不仅限于一个配置设置；我们可以在同一个调用中添加`foreground`、`font`或其他配置设置。

要将标签添加到我们的`TreeView`行中，我们需要更新`populate`方法。

在`for`循环中，在插入行之前，我们将添加这段代码：

```py
           if self.inserted and rownum in self.inserted:
                tag = 'inserted'
            elif self.updated and rownum in self.updated:
                tag = 'updated'
            else:
                tag = ''
```

如果`inserted`列表存在且我们的`rownum`在其中，我们希望`tag`等于`'inserted'`；如果`updated`列表存在且我们的`rownum`在其中，我们希望它等于`'updated'`。否则，我们将其留空。

现在，我们的`treeview.insert`调用只需要用这个`tag`值来修改：

```py
            self.treeview.insert('', 'end', iid=str(rownum),
                                 text=str(rownum), values=values,
                                 tag=tag)
```

运行应用程序并尝试插入和更新一些记录。

你应该得到类似这样的东西：

![](assets/b8fb6912-02db-4fc5-a202-10d600d31c28.png)

# Tkinter字体

在Tkinter中，有三种指定小部件字体的方法。

最简单的方法就是使用字符串格式：

```py
tk.Label(text="Direct font format", 
         font="Times 20 italic bold")
```

字符串采用`Font-family` `size` `styles`的格式，其中`styles`可以是任何有效的文本样式关键字的组合。

这些单词包括：

+   `bold` 用于粗体文本，或 `normal` 用于正常字重

+   `italic` 用于斜体文本，或 `roman` 用于常规倾斜

+   `underline` 用于下划线文本

+   `overstrike` 用于删除线文本

除了字体系列之外，其他都是可选的，尽管如果要指定任何样式关键字，您需要指定一个`size`。样式关键字的顺序无关紧要，但是重量和斜体关键字是互斥的（也就是说，您不能同时拥有`bold` `normal`或`italic roman`）。

字符串方法的一个缺点是它无法处理名称中带有空格的字体。

要处理这些，您可以使用字体的元组格式：

```py
tk.Label(
    text="Tuple font format",
    font=('Droid sans', 15, 'overstrike'))
```

这种格式与字符串格式完全相同，只是不同的组件被写成元组中的项目。`size`组件可以是整数或包含数字的字符串，这取决于值来自何处。

这种方法适用于在启动时设置少量字体更改的情况，但是对于需要动态操作字体设置的情况，Tkinter具有一种称为**命名字体**的功能。这种方法使用一个可以分配给小部件然后动态更改的`Font`类。

要使用`Font`，必须从`tkinter.font`模块中导入它：

```py
from tkinter.font import Font
```

现在，我们可以创建一个自定义的`Font`对象并将其分配给小部件：

```py
labelfont = Font(family='Courier', size=30,
                 weight='bold', slant='roman',
                 underline=False, overstrike=False)
tk.Label(text='Using the Font class', font=labelfont).pack()
```

正如您所看到的，`Font`构造函数参数与字符串和元组字体规范中使用的关键字相关。

一旦分配了这个字体，我们就可以在运行时动态地改变它的各个方面：

```py
def toggle_overstrike():
    labelfont['overstrike'] = not labelfont['overstrike']

tk.Button(text='Toggle Overstrike', command=toggle_overstrike).pack()
```

在这个例子中，我们提供了一个`Button`，它将切换`overstrike`属性的开和关。

Tk已经配置了几个命名字体；我们可以使用`tkinter.font`模块的`nametofont`函数从中创建Python `Font`对象。

这个表格显示了Tkinter中包含的一些命名字体：

| **字体名称** | **默认为** | **用于** |
| --- | --- | --- |
| `TkCaptionFont` | 系统标题字体 | 窗口和对话框标题栏 |
| `TkDefaultFont` | 系统默认字体 | 未另行指定的项目 |
| `TkFixedFont` | 系统等宽字体 | 无 |
| `TkHeadingFont` | 系统标题字体 | 列表和表格中的列标题 |
| `TkIconFont` | 系统图标字体 | 图标标题 |
| `TkMenuFont` | 系统菜单字体 | 菜单标签 |
| `TkSmallCaptionFont` | 系统标题 | 子窗口，工具对话框 |
| `TkTextFont` | 系统输入字体 | 输入小部件：Entry，Spinbox等 |
| `TkTooltipFont` | 系统工具提示字体 | 工具提示 |

如果您想知道Tkinter在您的操作系统上使用的字体，可以使用`tkinter.font.names()`函数检索它们的列表。

要改变应用程序的整体外观，我们可以覆盖这些命名字体，更改将应用于所有未另行设置字体的小部件。

例如：

```py
import tkinter as tk
from tkinter.font import nametofont

default_font = nametofont('TkDefaultFont')
default_font.config(family='Helvetica', size=32)

tk.Label(text='Feeling Groovy').pack()
```

在这个例子中，我们使用`nametofont`函数检索`TkDefaultFont`的对象，Tkinter应用程序的默认命名字体类。检索后，我们可以设置它的字体`family`和`size`，为使用`TkDefaultFont`的所有小部件更改这些值。

`Label`然后显示这个调整的结果：

![](assets/12b670be-7fa5-4774-acb0-5cae16efab46.png)

# 给用户提供字体选项

我们的一些数据输入用户抱怨应用程序的字体有点太小，不容易阅读，但其他人不喜欢您增加它，因为这样会使应用程序对屏幕来说太大。为了满足所有用户，我们可以添加一个配置选项，允许他们设置首选字体大小。

我们需要首先向我们的设置模型添加一个`'font size'`选项。

打开`models.py`并将`SettingsModel.variables`字典追加如下：

```py
    variables = {
        ...
        'font size': {'type': 'int', 'value': 9}
```

接下来，我们将在选项菜单中添加一组单选按钮，以便用户可以设置值。

打开`views.py`，让我们在将选项菜单添加到主菜单之前开始创建一个菜单：

```py
        font_size_menu = tk.Menu(self, tearoff=False)
        for size in range(6, 17, 1):
            font_size_menu.add_radiobutton(
                label=size, value=size,
                variable=settings['font size'])
        options_menu.add_cascade(label='Font size', 
                                 menu=font_size_menu)
```

这应该看起来很熟悉，因为我们在学习Tkinter `Menu`小部件时创建了一个几乎相同的字体大小菜单。我们允许字体大小从`6`到`16`，这应该为我们的用户提供足够的范围。

在`Application`类中，让我们创建一个方法，将应用程序的字体设置应用到我们的应用程序字体中：

```py
    def set_font(self, *args):
```

我们包括`*args`，因为`set_font`将作为`trace`回调调用，所以我们需要捕获发送的任何参数，尽管我们不会使用它们。

接下来，我们将获取当前的`'font size'`值：

```py
   font_size = self.settings['font size'].get()
```

我们需要更改几个命名字体，不仅仅是`TkDefaultFont`。对于我们的应用程序，`TkDefaultFont`、`TkTextFont`和`TkMenuFont`应该足够了。

我们只需循环遍历这些，检索类并在每个类上设置大小：

```py
        font_names = ('TkDefaultFont', 'TkMenuFont', 'TkTextFont')
        for font_name in font_names:
            tk_font = nametofont(font_name)
            tk_font.config(size=font_size)
```

我们需要做的最后一件事是确保调用此回调。

在`Application.__init__()`中的`load_settings()`调用之后，添加以下内容：

```py
        self.set_font()
        self.settings['font size'].trace('w', self.set_font)
```

我们调用`set_font()`一次以激活任何保存的`字体大小`设置，然后设置一个`trace`，以便在更改值时运行它。

运行应用程序并尝试使用字体菜单。它应该看起来像这样：

![](assets/8fb2114c-d0ab-4d97-96b0-4523e44ddb24.png)

# Ttk小部件的样式

Ttk小部件在功能上代表了对标准Tkinter小部件的重大改进。这种灵活性使得Ttk小部件能够在各个平台上模仿本机UI控件，但代价是：Ttk样式令人困惑，文档不完善，有时不一致。

要了解Ttk样式，让我们从最基本的元素到最复杂的元素开始使用一些词汇：

+   Ttk从**元素**开始。元素是小部件的一部分，例如边框、箭头或可以输入文本的字段。

+   元素使用**布局**组成一个完整的小部件（例如`Combobox`或`Treeview`）。

+   **样式**是定义颜色和字体设置的属性集合：

+   每个样式都有一个名称，通常是T，加上小部件的名称，例如`TButton`或`TEntry`。也有一些例外。

+   布局中的每个元素都引用一个或多个样式属性来定义其外观。

+   小部件有许多**状态**，这些状态是可以打开或关闭的标志：

+   样式可以通过**映射**进行配置，将属性值连接到状态或状态的组合

+   一组样式称为**主题**。Tkinter在不同平台上提供了不同的主题。：

+   一个主题可能不仅定义不同的样式，还定义不同的布局。例如，默认macOS主题上的`ttk.Button`可能包含不同的元素集，与在Windows中使用默认主题的`ttk.Button`相比，应用样式设置的方式也不同。

如果你现在感到困惑，没关系。让我们深入了解`ttk.Combobox`的解剖，以更好地了解这些概念。

# 探索Ttk小部件

为了更好地了解Ttk小部件是如何构建的，请在IDLE中打开一个shell并导入`tkinter`、`ttk`和`pprint`：

```py
>>> import tkinter as tk
>>> from tkinter import ttk
>>> from pprint import pprint
```

现在，创建一个根窗口、`Combobox`和`Style`对象：

```py
>>> root = tk.Tk()
>>> cb = ttk.Combobox(root)
>>> cb.pack()
>>> style = ttk.Style()
```

`Style`对象可能命名有点不准确；它并不指向单个样式，而是给了我们一个处理当前主题的样式、布局和映射的句柄。

为了检查我们的`Combobox`，我们首先使用`winfo_class()`方法获取它的`stylename`：

```py
>>> cb_stylename = cb.winfo_class()
>>> print(cb_stylename)
TCombobox
```

然后，我们可以使用`Style.layout()`方法检查`Combobox`的布局：

```py
>>> cb_layout = style.layout(cb_stylename)
>>> pprint(cb_layout)
[('Combobox.field',
  {'children':  [('Combobox.downarrow', 
                {'side': 'right', 'sticky': 'ns'}),
                ('Combobox.padding',
                {'children': [('Combobox.textarea', 
                {'sticky': 'nswe'})],
                'expand': '1',
                'sticky': 'nswe'})],
                'sticky': 'nswe'})]
```

通过将样式名称（在本例中为`TCombobox`）传递给`style.layout()`方法，我们得到一个布局规范，显示用于构建此小部件的元素的层次结构。

在这种情况下，元素是`"Combobox.field"`、`"Combobox.downarrow"`、`"Combobox.padding"`和`"Combobox.textarea"`。正如您所看到的，每个元素都有与`pack()`中传递的定位属性类似的关联定位属性。

`layout`方法也可以用于通过传入新的布局规范来替换样式的布局。不幸的是，这需要替换整个布局规范，您不能只是在原地调整或替换单个元素。

要查看样式如何连接到元素，我们可以使用`style.element_options()`方法。该方法接受一个元素名称，并返回一个可以用于更改它的选项列表。

例如：

```py
>>> pprint(style.element_options('Combobox.downarrow'))
('background', 'relief', 'borderwidth', 'arrowcolor', 'arrowsize')
```

这告诉我们`Combobox`的`downarrow`元素使用这些样式属性来确定其外观。要更改这些属性，我们将需要使用`style.configure()`方法。

让我们将箭头的颜色更改为`red`：

```py
>>> style.configure('TCombobox', arrowcolor='red')
```

您应该看到`arrowcolor`已更改为`red`。这就是我们需要了解的配置小部件进行静态更改的全部内容，但是动态更改呢？

要进行动态更改，我们需要了解小部件的状态。

我们可以使用`state`方法检查或更改我们的`Combobox`的状态：

```py
>>> print(cb.state())
()
>>> cb.state(['active', 'invalid'])
('!active', '!invalid')
>>> print(cb.state())
('active', 'invalid')
```

`Combobox.state()`没有参数时将返回一个包含当前设置的状态标志的元组；当与参数一起使用时（参数必须是字符串序列），它将设置相应的状态标志。

要关闭状态标志，需要在标志名称前加上`!`：

```py
>>> cb.state(['!invalid'])
('invalid',)
>>> print(cb.state())
('active',)
```

当您调用`state()`并带有参数来更改值时，返回值是一个元组，其中包含一组状态，如果应用，则会撤消您刚刚设置的状态更改。这在您想要临时设置小部件的状态，然后将其返回到先前（未知）状态的情况下可能会有用。

您不能只是使用任何字符串来调用`state()`；它们必须是以下之一：

+   `active`

+   `disabled`

+   `focus`

+   `pressed`

+   `selected`

+   `background`

+   `readonly`

+   `alternate`

+   `invalid`

每个小部件如何使用这些状态取决于小部件；并非每个`state()`默认情况下都配置为具有效果。

小部件状态通过映射与小部件样式进行交互。我们使用`style.map()`方法来检查或设置每个样式的映射。

看一下`TCombobox`的默认映射：

```py
>>> pprint(style.map(cb_stylename))
{'arrowcolor': [('disabled', '#a3a3a3')],
 'fieldbackground': [('readonly', '#d9d9d9'), 
                     ('disabled', '#d9d9d9')]}
```

正如您所看到的，`TCombobox`默认情况下具有`arrowcolor`和`fieldbackground`属性的样式映射。每个样式映射都是一个元组列表，每个元组都是一个或多个状态标志，后跟一个设置的值。当所有状态标志与小部件的当前状态匹配时，该值生效。

默认映射在设置`disabled`标志时将箭头颜色更改为浅灰色，并在设置`disabled`或`readonly`标志时将字段背景更改为不同的浅灰色。

我们可以使用相同的方法设置自己的样式映射：

```py
>>> style.map('TCombobox', arrowcolor=[('!invalid',  'blue'), ('invalid', 'focus', 'red')])
{}
>>> pprint(style.map('TCombobox'))
{'arrowcolor': [('!invalid', 'blue'), ('invalid', 'focus', 'red')],
 'fieldbackground': [('readonly', '#d9d9d9'), ('disabled', '#d9d9d9')]}
```

在这里，我们已经配置了`arrowcolor`属性，当`invalid`标志未设置时，它为`blue`，当`invalid`和`focus`标志都设置时，它为`red`。请注意，虽然我们对`map`的调用完全覆盖了`arrowcolor`样式映射，但`fieldbackground`映射未受影响。这意味着您可以单独替换样式映射，但不能简单地追加到给定属性的现有映射中。

到目前为止，我们一直在操作`TCombobox`样式，这是所有`Combobox`小部件的默认样式。我们所做的任何更改都会影响应用程序中的每个`Combobox`。我们还可以通过在现有样式名称前加上名称和点来创建从现有样式派生的自定义样式。

例如：

```py
>>> style.configure('Blue.TCombobox', fieldbackground='blue')
>>> cb.configure(style='Blue.TCombobox')
```

`Blue.TCombobox`继承了`TCombobox`的所有属性（包括我们之前配置的`blue` `downarrow`），但可以添加或覆盖自己的设置，而不会影响`TCombobox`。这使您可以为某些小部件创建自定义样式，而不会影响相同类型的其他小部件。

我们可以通过更改主题来一次性改变所有Ttk小部件的外观。请记住，主题是一组样式，因此通过更改主题，我们将替换所有内置样式和布局。

不同的主题在不同的平台上发行；要查看您平台上可用的主题，使用`theme_names()`方法：

```py
>>> style.theme_names()
('clam', 'alt', 'default', 'classic')
```

（这些是Debian Linux上可用的主题；您的可能不同。）

要查询当前主题，或设置新主题，使用`theme_use()`方法：

```py
>>> style.theme_use()
'default'
>>> style.theme_use('alt')
```

注意当您更改主题时，之前的样式已经消失。但是，如果您切换回默认主题，您会发现您的更改已被保留。

# 为我们的表单标签设置样式

我们可以利用我们对样式的知识来解决的第一件事是我们的表单小部件。由于`LabelInput`小部件保留其默认的沉闷颜色，我们的表单的着色相当丑陋和不完整。我们需要为每个小部件设置样式，以匹配其`LabelInput`的颜色。

在`views.py`文件中，在`DataRecordForm.__init__()`方法的开头添加此内容：

```py
   style = ttk.Style()
```

我们正在创建我们的`Style`对象，以便我们可以开始使用我们的Ttk样式。我们需要哪些样式？

+   我们需要为每个部分的Ttk`Label`小部件设置样式，因为我们需要为`RecordInfo`、`EnvironmentInfo`和`Plant Info`中的小部件设置不同的颜色。

+   我们需要为我们的Ttk`Checkbutton`设置样式，因为它使用自己内置的标签而不是单独的标签小部件。由于现在只有一个，我们只需要一个样式。

让我们创建这些样式：

```py
        style.configure('RecordInfo.TLabel', background='khaki')
        style.configure(
            'EnvironmentInfo.TLabel',
             background='lightblue')
        style.configure(
            'EnvironmentInfo.TCheckbutton',
            background='lightblue')
        style.configure('PlantInfo.TLabel', background='lightgreen')
```

如您所见，我们正在基于`TLabel`创建自定义样式，但这是为每个单独的部分添加前缀。对于每种样式，我们只需适当设置`background`颜色。

现在是将此样式添加到每个小部件的繁琐任务：

```py
       self.inputs['Date'] = w.LabelInput(
            recordinfo, "Date",
            field_spec=fields['Date'],
            label_args={'style': 'RecordInfo.TLabel'})
```

在每个`LabelInput`调用中，您需要添加一个`label_args`参数，将`style`设置为相应部分的`TLabel`样式。为所有小部件执行此操作。

对于`Checkbutton`，您需要以不同的方式进行操作：

```py
       self.inputs['Equipment Fault'] = w.LabelInput(
            environmentinfo, "Equipment Fault",
            field_spec=fields['Equipment Fault'],
            label_args={'style': 'EnvironmentInfo.TLabel'},
            input_args={'style': 'EnvironmentInfo.TCheckbutton'})
```

在这里，我们设置了`input_args`，因为该样式适用于`Checkbutton`而不是标签（保留`label_args`；我们一会儿会用到）。

如果你此时运行程序，你会看到明显的改进，但还不够完美；错误标签仍然是旧的默认颜色。

要解决这个问题，我们只需要编辑我们的`LabelInput`小部件，以便错误标签也使用`label_args`。

在`widgets.py`中，修复`LabelInput.__init__()`中的`self.error_label`赋值：

```py
        self.error_label = ttk.Label(self, textvariable=self.error,
                                     **label_args)
```

现在，您的应用程序应该具有一致的颜色，并且看起来更加吸引人：

![](assets/eabe2216-6517-4675-b812-375b762689ae.png)

# 在错误时为输入小部件设置样式

我们的数据输入人员抱怨说，我们字段中的错误样式并不是非常显眼。目前，我们只是将`foreground`颜色设置为`红色`。

这有几个问题：

+   对于空字段，实际上没有什么可以涂成`红色`

+   我们的色盲用户很难区分`红色`和普通文本颜色

我们将利用我们的样式知识来改进错误样式，并使无效字段更加显眼。

不过，在这之前，您可能需要修复一个小部件的小问题。

# 使我们的Spinbox成为Ttk小部件

如果您使用的是Python 3.6或更早版本，则`Spinbox`小部件仅在`tkinter`中可用，而不在`ttk`中。我们需要修复这个问题，以便我们的错误样式可以保持一致。

在撰写本书时，作者已经提交了一个补丁到Python 3.7中，以包括Ttk`Spinbox`。如果您使用的是Python 3.7或更高版本，您可以直接使用`ttk::spinbox`并跳过此部分。

由于`Spinbox`已经在Tcl/Tk Ttk库中，为其创建一个Python类非常容易。

在`widgets.py`的顶部附近添加此代码：

```py
class TtkSpinbox(ttk.Entry):

    def __init__(self, parent=None, **kwargs):
        super().__init__(parent, 'ttk::spinbox', **kwargs)
```

这就是为这个应用程序创建Ttk `Spinbox`所需的全部内容。我们只是对`ttk.Entry`进行子类化，但在`__init__`语句中更改了使用的Ttk小部件。如果我们需要`Entry`缺少的任何`Spinbox`方法，我们需要提供这些方法；对于这个应用程序，我们不需要其他任何东西。

现在，我们只需要更新我们的`ValidatedSpinbox`类，使其继承`TtkSpinbox`而不是`tk.Spinbox`：

```py
class ValidatedSpinbox(ValidatedMixin, TtkSpinbox):
```

# 更新ValidatedMixin

现在我们正在使用所有的Ttk小部件，我们可以通过一些动态样式来更新我们的`ValidatedMixin`类。

我们将从`__init__()`方法开始，创建一个`Style`对象：

```py
   style = ttk.Style()
```

由于这是一个混合类，我们不知道我们正在混合的小部件的原始样式名称，因此我们将不得不获取它。

记住我们可以用`winfo_class()`来实现这一点：

```py
       widget_class = self.winfo_class()
       validated_style = 'ValidatedInput.' + widget_class
```

在获取小部件类之后，我们通过在其前面添加`ValidatedInput`来创建一个派生样式。为了在错误和非错误外观之间切换我们的输入外观，我们将创建一个样式映射，根据`invalid`状态标志的状态进行切换。

您可以通过调用`style.map()`来实现这一点：

```py
       style.map(
            validated_style,
            foreground=[('invalid', 'white'), ('!invalid', 'black')],
            fieldbackground=[('invalid', 'darkred'), ('!invalid', 'white')]
       )
```

我们仍然使用红色，因为它是一个已建立的“错误颜色”，但这次我们将字段从浅色背景变为深色背景。这应该帮助我们的色盲用户区分错误，即使它们是红色的。

最后，我们需要更新对`self.config`的调用，以包括将小部件的样式设置为我们的新验证样式：

```py
       self.config(
            style=validated_style,
            validate='all',
            ...
```

Ttk小部件会自动设置它们的`invalid`标志作为内置验证系统的一部分。目前，我们有一个名为`_toggle_error()`的方法，每当验证开始或失败时都会调用它，并设置错误状态。我们可以完全删除该方法，以及所有对它的引用。

如果现在尝试应用程序，您会看到带有错误的字段现在会变成深红色，并带有白色文本：

![](assets/aa35c553-7642-47b8-b442-8d30c3a69249.png)

# 设置主题

一般来说，任何给定平台上的默认Ttk主题可能是最好的选择，但外观是主观的，有时我们可能觉得Tkinter做错了。有一种方法可以调整主题可能有助于消除一些粗糙的边缘，并使一些用户更舒适地看待应用程序的外观。

正如我们已经看到的，查询可用主题并设置新主题是相当简单的。让我们创建一个配置选项来更改我们应用程序的主题。

# 构建主题选择器

主题不是用户经常需要更改的东西，正如我们所见，更改主题可能会撤消我们对小部件的样式更改。鉴于此，我们将通过设计我们的主题更改器的方式来确保需要重新启动程序才能实际更改。

我们将首先在我们的`SettingsModel`中添加一个主题选项：

```py
    variables = {
        ...
        'theme': {'type': 'str', 'value': 'default'}
    }
```

每个平台都有一个`theme`别名为`default`，因此这是一个安全和合理的默认值。

接下来，我们的`Application.__init__()`方法将需要检查这个值，并相应地设置`theme`。

在调用`load_settings()`之后添加此代码：

```py
        style = ttk.Style()
        theme = self.settings.get('theme').get()
        if theme in style.theme_names():
            style.theme_use(theme)
```

我们创建一个`Style`对象，查询主题名称的设置，然后（假设保存的`theme`在可用主题中）相应地设置`theme`。

现在剩下的就是创建UI。

在`views.py`文件中，我们将为`options_menu`创建一个新的子菜单：

```py
        style = ttk.Style()
        themes_menu = tk.Menu(self, tearoff=False)
        for theme in style.theme_names():
            themes_menu.add_radiobutton(
                label=theme, value=theme,
                variable=settings['theme']
            )
        options_menu.add_cascade(label='Theme', menu=themes_menu)
```

在这里，我们只是循环遍历可用的主题，并为每个主题添加一个`Radiobutton`，将其绑定到我们的`settings['theme']`变量。

对于用户来说，可能不明显更改主题需要重新启动，因此让我们确保让他们知道。

我们将为变量添加一个`trace`：

```py
        settings['theme'].trace('w', self.on_theme_change)
```

`on_theme_change`方法将显示一个警告对话框，通知用户需要重新启动才能实现更改。

将其添加到`MainMenu`类的末尾：

```py
    def on_theme_change(self, *args):
        """Popup a message about theme changes"""
        message = "Change requires restart"
        detail = (
            "Theme changes do not take effect"
            " until application restart")
            messagebox.showwarning(
            title='Warning',
            message=message,
            detail=detail)
```

现在，您可以运行应用程序并尝试更改`theme`。哪个主题在您的平台上看起来最好？

你可能会发现，平台上的一些主题会破坏表单中的小部件样式。请记住，主题不仅仅改变默认颜色和字体，它们还改变小部件元素本身的布局和内容。有时，由于属性名称的更改，样式设置在不同主题之间无法传递。

# 总结

在本章中，我们为了美观和可用性改进彻底改变了应用程序的外观和感觉。您学会了如何处理Tkinter小部件的颜色和字体设置，以及Ttk样式的复杂世界。
