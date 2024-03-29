# 第三章：在 Twitter 上投票

在上一章中，我们实现了一个终端应用程序，作为流行音乐服务 Spotify 的远程控制器。在这个应用程序中，我们可以搜索艺术家，浏览专辑，以及浏览每张专辑中的曲目。最后，我们甚至可以请求在用户的活动设备上播放曲目。

这一次，我们将开发一个将与 Twitter 集成的应用程序，利用其 REST API。 Twitter 是一个自 2006 年以来就存在的社交网络，拥有超过 3 亿活跃用户。私人用户、公司、艺术家、足球俱乐部，你几乎可以在 Twitter 上找到任何东西。但我认为让 Twitter 如此受欢迎的是它的简单性。

与博客文章不同，Twitter 的帖子或*推文*必须简短并直奔主题，而且准备发布的时间也不需要太长。另一个使 Twitter 如此受欢迎的原因是该服务是一个很好的新闻来源。如果你想要了解世界上正在发生的事情，政治、体育、科技等等，Twitter 就是你要去的地方。

除此之外，Twitter 对于开发者来说有一个相当不错的 API，为了利用这一点，我们将开发一个应用程序，用户可以使用标签投票。在我们的应用程序中，我们将配置要监视的标签，并且它将自动定期获取与该标签匹配的最新推文，对它们进行计数，并在用户界面中显示它们。

在本章中，您将学习以下内容：

+   创建一个推文应用程序

+   使用`OAuth`库并实现三步验证流程

+   使用 Twitter API 搜索最新的推文

+   使用`Tkinter`构建一个简单的用户界面

+   学习多进程和响应式编程的基础知识

# 设置环境

首先，我们要做的事情通常是设置我们的开发环境，第一步是为我们的应用程序创建一个虚拟环境。我们的应用程序将被称为`twittervotes`，所以让我们继续创建一个名为`twittervotes`的虚拟环境：

![](img/9cf0dbeb-8a22-4726-930f-c0869b9b0dbf.png)

当`virtualenv`环境创建好后，您可以使用以下命令激活它：

```py
. twittervotes/bin/activate
```

太好了！现在让我们设置项目的目录结构。它应该如下所示：

```py
twittervotes
├── core
│   ├── models
│   └── twitter
└── templates
```

让我们深入了解一下结构：

| `twittervotes` | 应用程序的根目录。在这里，我们将创建应用程序的入口点，以及一个小的辅助应用程序来执行 Twitter 身份验证。 |
| --- | --- |
| `twittervotes/core` | 这将包含我们项目的所有核心功能。它将包含身份验证代码、读取配置文件、向 Twitter API 发送请求等等。 |
| `twittervotes/core/models` | 用于保存应用程序数据模型的目录。 |
| `twittervotes/core/twitter` | 在`twitter`目录中，我们将保留与 Twitter API 交互的`helper`函数。 |
| `twittervotes/templates` | 在这里，我们将保存我们应用程序将使用的所有 HTML 模板。 |

接下来，是时候添加我们项目的依赖关系了。继续在`twittervotes`目录中创建一个名为`requirements.txt`的文件，内容如下：

```py
Flask==0.12.2
oauth2==1.9.0.post1
PyYAML==3.12
requests==2.18.4
Rx==1.6.0
```

以下表格解释了前面的依赖关系的含义：

| `Flask` | 我们将在这里使用 Flask 创建一个简单的 Web 应用程序，以便与 Twitter 进行身份验证。 |
| --- | --- |
| `oauth2` | 这是一个很棒的包，它将在执行`OAuth`身份验证时抽象出很多复杂性。 |
| `PyYAML` | 我们将使用这个包来创建和读取 YAML 格式的配置文件。 |
| `Requests` | 允许我们通过 HTTP 访问 Twitter API。 |
| `Rx` | 最后，我们将使用 Python 的 Reactive Extensions，以便在新的推文计数到达时，可以对我们的 UI 进行响应式更新。 |

文件创建后，运行命令`pip install -r requirements.txt`，您应该会看到类似以下的输出：

![](img/40df8c49-f4b0-4320-bef0-833905edb509.png)如果运行命令`pip freeze`，您将获得以 pip 格式列出的依赖项列表，并且您将注意到输出列出了比我们实际添加到`requirements`文件中的依赖项更多的依赖项。 原因是我们的项目需要的软件包也有依赖项，并且它们也将被安装。 因此，如果您安装的软件包比您在`requirements`文件中指定的要多，请不要担心。

现在我们的环境已经设置好，我们可以开始创建我们的 Twitter 应用程序。 通常，在开始编码之前，请确保您的代码已经在 Git 等源代码控制系统下； 有很多在线服务可以免费托管您的存储库。

通过这种方式，您可以回滚项目的不同版本，如果您的计算机出现问题，也不会丢失工作。 话虽如此，让我们创建我们的 Twitter 应用程序。

# 创建 Twitter 应用程序

在本节中，我们将创建我们的第一个 Twitter 应用程序，以便可以使用 Twitter REST API。 如果您还没有帐户，则需要创建一个帐户。 如果您不使用 Twitter，我强烈建议您使用； 这是一个了解所有新闻和开发世界正在发生的事情的好方法，也是在 Python 社区中结交新朋友的好方法。

创建帐户后，转到[`apps.twitter.com/`](https://apps.twitter.com/)，使用您的登录凭据登录，您将进入一个页面，您可以在该页面上看到您已经创建的应用程序的列表（第一次，您可能会有一个空的应用程序列表），并且在同一页上，您将有可能创建新的应用程序。 单击右上角的“创建新应用程序”按钮，它将打开以下页面：

![](img/4599e1a6-0846-42c4-9920-11341efa9cec.png)

在此表单中，有三个必填字段-名称，描述和网站：

+   **名称**：这是您的应用程序的名称； 这也是在执行授权时将呈现给您的应用程序用户的名称。 名称不需要遵循任何特定的命名约定，您可以随意命名。

+   **描述**：顾名思义，这是您的应用程序的描述。 这个字段也将呈现给您的应用程序用户，因此最好有描述您的应用程序的好文本。 在这种情况下，我们不需要太多文本。 让我们添加`用于在 Twitter 上使用标签投票的应用程序`。

+   **网站**：指定您的应用程序的网站； 它也将在授权期间呈现给用户，并且是用户可以下载或获取有关您的应用程序的更多信息的网站。 由于我们处于开发阶段，我们可以添加一个占位符，例如[`www.example.com`](http://www.example.com)。

+   **回调 URL**：这与上一章中的 Spotify 终端应用程序中的回调 URL 的工作方式相同。 这是 Twitter 将调用以发送授权代码的 URL。 这不是必需的字段，但我们需要它，所以让我们继续添加；`http://localhost:3000/callback`。

填写所有字段后，您只需要勾选 Twitter 开发者协议并单击“创建 Twitter 应用程序”按钮。

如果一切顺利，您将被引导到另一个页面，您可以在该页面上看到您新创建的应用程序的更多详细信息。 在应用程序名称下方，您将看到一个带有选项卡的区域，显示有关应用程序的设置和不同信息的选项卡：

![](img/469924a4-645a-46fc-837d-bd1517bc4733.png)

在第一个选项卡“详细信息”中，我们要复制所有我们将用于执行身份验证的 URL。滚动到“应用程序设置”，并复制“请求令牌 URL”、“授权 URL”和“访问令牌 URL”：

![](img/e709f442-79ec-4474-8cfc-d18c21a0b195.png)

太好了！现在让我们转到“密钥和访问令牌”选项卡，复制“消费者密钥”和“消费者密钥”*：*

![](img/30c5220e-277f-4cc8-b2b4-db7c7bb37429.png)

现在我们已经复制了所有必要的信息，我们可以创建一个将被我们的应用程序使用的配置文件。将所有这些内容保存在配置文件中是一种良好的做法，这样我们就不需要在代码中硬编码这些 URL。

我们将*消费者密钥*和*消费者密钥*添加到我们项目中的配置文件；正如名称所示，这个密钥是*秘密*的，所以如果您计划在 GitHub 等服务中为您的代码创建存储库，请确保将配置文件添加到`.gitignore`文件中，以便密钥不被推送到云存储库。永远不要与任何人分享这些密钥；如果您怀疑有人拥有这些密钥，您可以在 Twitter 应用的网站上为您的应用生成新的密钥。

# 添加配置文件

在这一部分，我们将为我们的应用程序创建配置文件；配置文件将采用 YAML 格式。如果您想了解有关 YAML 的更多信息，可以查看网站[`yaml.org/`](http://yaml.org/)，在那里您将找到示例、规范，以及可以用于操作 YAML 文件的不同编程语言的库列表。

对于我们的应用程序，我们将使用 PyYAML，它将允许我们以非常简单的方式读取和写入 YAML 文件。我们的配置文件非常简单，所以我们不需要使用库的任何高级功能，我们只想读取内容并写入，我们要添加的数据非常平坦；我们不会有任何嵌套对象或任何类型的列表。

让我们获取我们从 Twitter 获取的信息，并将其添加到配置文件中。在应用程序的`twittervotes`目录中创建一个名为`config.yaml`的文件，内容如下：

```py
consumer_key: '<replace with your consumer_key>'
consumer_secret: '<replace with your consumer secret>'
request_token_url: 'https://api.twitter.com/oauth/request_token'
authorize_url: 'https://api.twitter.com/oauth/authorize'
access_token_url: 'https://api.twitter.com/oauth/access_token'
api_version: '1.1'
search_endpoint: 'https://api.twitter.com/1.1/search/tweets.json'
```

太好了！现在我们将在我们的项目中创建第一个 Python 代码。如果您已经阅读了前几章，那么读取配置文件的函数对您来说将是熟悉的。这个想法很简单：我们将读取配置文件，解析它，并创建一个我们可以轻松使用来访问我们添加到配置中的数据的模型。首先，我们需要创建配置模型。

在`twittervotes/core/models/`中创建一个名为`models.py`的文件，内容如下：

```py
from collections import namedtuple

Config = namedtuple('Config', ['consumer_key',
                               'consumer_secret',
                               'request_token_url',
                               'access_token_url',
                               'authorize_url',
                               'api_version',
                               'search_endpoint', ])
```

在上一章中对`namedtuple`进行了更详细的介绍，所以我不会再详细介绍它；如果您还没有阅读第二章，只需知道`namedtuple`是一种类，这段代码将使用第二个参数中指定的字段定义一个名为`Config`的`namedtuple`。

太好了，现在让我们在`twittervotes/core/models`中创建另一个名为`__init__.py`的文件，并导入我们刚刚创建的`namedtuple`：

```py
from .models import Config
```

现在是时候创建读取 YAML 文件并将其返回给我们的函数了。在`twittervotes/core/`中创建一个名为`config.py`的文件。让我们开始添加导入语句：

```py
import os
import yaml

from .models import Config
```

我们将使用`os`包轻松获取用户当前目录并操作路径。我们还导入 PyYAML，以便读取 YAML 文件，最后，从`models`模块中导入我们刚刚创建的`Config`模型。

然后我们定义两个函数，首先是`_read_yaml_file`函数。这个函数有两个参数——`filename`，是我们要读取的配置文件的名称，还有`cls`，可以是我们用来存储配置数据的`class`或`namedtuple`。

在这种情况下，我们将传递`Config`——`namedtuple`，它具有我们将要读取的 YAML 配置文件相同的属性：

```py
def _read_yaml_file(filename, cls):
    core_dir = os.path.dirname(os.path.abspath(__file__))
    file_path = os.path.join(core_dir, '..', filename)

    with open(file_path, mode='r', encoding='UTF-8') as file:
        config = yaml.load(file)
        return cls(**config)
```

首先，我们使用`os.path.abspath`函数，将特殊变量`__file__`作为参数传递。当一个模块被加载时，变量`__file__`将被设置为与模块同名。这将使我们能够轻松找到加载配置文件的位置。因此，以下代码段将返回核心模块的路径。

`/projects/twittervotes/core`：

```py
core_dir = os.path.dirname(os.path.abspath(__file__)) will return
```

我们知道配置文件将位于`/projects/twittervotes/`，所以我们需要将`..`与路径连接起来，以在目录结构中向上移动一级，以便读取文件。这就是我们构建完整配置文件路径的原因。

`file_path = os.path.join(core_dir, '..', filename)`

这将使我们能够从系统中的任何位置运行此代码。

我们以 UTF-8 编码以读取模式打开文件，并将其传递给`yaml.load`函数，将结果赋给`config`变量。`config`变量将是一个包含配置文件中所有数据的字典。

这个函数的最后一行是有趣的部分：如果你还记得，`cls`参数是一个`class`或者`namedtuple`，所以我们将配置字典的值作为参数展开。在这里，我们将使用`Config`——`namedtuple`，所以`cls(**config)`等同于`Config`，`(**config)`，使用`**`传递参数将等同于逐个传递所有参数：

```py
Config(
    consumer_key: ''
    consumer_secret: ''
    app_only_auth: 'https://api.twitter.com/oauth2/token'
    request_token_url: 'https://api.twitter.com/oauth/request_token'
    authorize_url: 'https://api.twitter.com/oauth/authorize'
    access_token_url: 'https://api.twitter.com/oauth/access_token'
    api_version: '1.1'
    search_endpoint: '')
```

现在我们要添加我们需要的第二个函数，`read_config`函数：

```py
def read_config():
    try:
        return _read_yaml_file('config.yaml', Config)
    except IOError as e:
        print(""" Error: couldn\'t file the configuration file 
        `config.yaml`
        'on your current directory.

        Default format is:',

        consumer_key: 'your_consumer_key'
        consumer_secret: 'your_consumer_secret'
        request_token_url: 
        'https://api.twitter.com/oauth/request_token'
        access_token_url:  
        'https://api.twitter.com/oauth/access_token'
        authorize_url: 'https://api.twitter.com/oauth/authorize'
        api_version: '1.1'
        search_endpoint: ''
        """)
        raise
```

这个函数非常简单；它只是利用我们刚刚创建的`_read_yaml_file`函数，将`config.yaml`文件作为第一个参数传递，并将`Config`、`namedtuple`作为第二个参数传递。

我们捕获`IOError`异常，如果文件在应用程序目录中不存在，则会抛出该异常；在这种情况下，我们会抛出一个帮助消息，向您的应用程序用户显示配置文件应该如何结构化。

最后一步是将其导入到`twittervotes/core`目录中的`__init__.py`中：

```py
from .config import read_config
```

让我们在 Python REPL 中尝试一下：

![](img/614acd5c-35c0-47b3-93aa-ad1c894123e2.png)

太棒了，它的工作原理就像我们想要的那样！在下一节中，我们可以开始创建执行认证的代码。

# 执行认证

在本节中，我们将创建一个程序，该程序将为我们执行认证，以便我们可以使用 Twitter API。我们将使用一个简单的 Flask 应用程序来实现这一点，该应用程序将公开两个路由。第一个是根路径`/`，它将加载和呈现一个简单的 HTML 模板，其中包含一个按钮，该按钮将重定向我们到 Twitter 认证对话框。

我们要创建的第二个路由是`/callback`。还记得我们在 Twitter 应用程序配置中指定的回调 URL 吗？这是在我们授权应用程序后将被调用的路由。它将返回一个授权令牌，该令牌将用于向 Twitter API 发出请求。所以让我们开始吧！

在我们开始实现 Flask 应用程序之前，我们需要在我们的模型模块中添加另一个模型。这个模型将代表请求授权数据。打开`twittervotes/core/models`中的`models.py`文件，并添加以下代码：

```py
RequestToken = namedtuple('RequestToken', ['oauth_token',
                                         'oauth_token_secret',
                                        'oauth_callback_confirmed'])
```

这将创建一个名为`RequestToken`的`namedtuple`，包含字段`oauth_token`、`oauth_token_secret`和`outh_callback_confirmed`；这些数据对我们执行认证的第二步是必要的。

最后，在`twittervotes/core/models`目录中打开`__init__.py`文件，并导入我们刚刚创建的`RequestToken` `namedtuple`，如下所示：

```py
from .models import RequestToken
```

既然我们已经有了模型，让我们开始创建 Flask 应用程序。让我们添加一个非常简单的模板，显示一个按钮，该按钮将启动认证过程。

在`twittervotes`目录中创建一个名为`templates`的新目录，并创建一个名为`index.html`的文件，内容如下：

```py
<html>
    <head>
    </head>
    <body>
       <a href="{{link}}"> Click here to authorize </a>
    </body>
</html>
```

# 创建 Flask 应用程序

完美，现在让我们在`twittervotes`目录中添加一个名为`twitter_auth.py`的文件。我们将在其中创建三个函数，但首先让我们添加一些导入：

```py
from urllib.parse import parse_qsl

import yaml

from flask import Flask
from flask import render_template
from flask import request

import oauth2 as oauth

from core import read_config
from core.models import RequestToken
```

首先，我们从`urllib.parse`模块中导入`parser_qls`来解析返回的查询字符串，以及`yaml`模块，这样我们就可以读取和写入`YAML`配置文件。然后我们导入构建 Flask 应用程序所需的一切。我们在这里要导入的最后一个第三方模块是`oauth2`模块，它将帮助我们执行`OAuth`认证。

最后，我们导入我们的函数`read_config`和我们刚刚创建的`RequestToken` `namedtuple`。

在这里，我们创建了我们的 Flask 应用程序和一些全局变量，这些变量将保存客户端、消费者和`RequestToken`实例的值：

```py
app = Flask(__name__)

client = None
consumer = None
req_token = None
```

我们要创建的第一个函数是一个名为`get_req_token`的函数，内容如下：

```py
def get_oauth_token(config):

    global consumer
    global client
    global req_token

    consumer = oauth.Consumer(config.consumer_key, 
     config.consumer_secret)
    client = oauth.Client(consumer)

    resp, content = client.request(config.request_token_url, 'GET')

    if resp['status'] != '200':
        raise Exception("Invalid response 
        {}".format(resp['status']))

    request_token = dict(parse_qsl(content.decode('utf-8')))

    req_token = RequestToken(**request_token)
```

这个函数的参数是一个配置实例，全局语句告诉解释器函数中使用的`req_token`将引用全局变量。

我们使用在创建 Twitter 应用程序时获得的消费者密钥和消费者密钥创建一个消费者对象。当消费者创建后，我们可以将其传递给客户端函数来创建客户端，然后我们调用请求函数，这个函数将执行请求到 Twitter，传递请求令牌 URL。

当请求完成时，响应和内容将被存储在变量`resp`和`content`中。紧接着，我们测试响应状态是否不是`200`或`HTTP.OK`；在这种情况下，我们会引发一个异常，否则我们解析查询字符串以获取发送回来的值，并创建一个`RequestToken`实例。

# 创建应用程序路由

现在我们可以开始创建路由了。首先，我们要添加根路由：

```py
@app.route('/')
def home():

    config = read_config()

    get_oauth_token(config)

    url = f'{config.authorize_url}?oauth_token=
    {req_token.oauth_token}'

    return render_template('index.html', link=url)
```

我们读取配置文件并将其传递给`get_oauth_token`函数。这个函数将用`oauth_token`的值填充全局变量`req_token`；我们需要这个令牌来开始授权过程。然后我们使用从配置文件中获取的`authorize_url`值和`OAuth`请求令牌构建授权 URL。

最后，我们使用`render_template`来渲染我们创建的`index.html`模板，并且还向函数传递了第二个参数，即上下文。在这种情况下，我们创建了一个名为`link`的项目，其值设置为`url`。如果你还记得`index.html`模板，那里有一个`"{{url}}"`的占位符。这个占位符将被我们在`render_template`函数中分配给`link`的值所替换。

默认情况下，Flask 使用 Jinja2 作为模板引擎，但可以更改为您喜欢的引擎；我们不会在本书中详细介绍如何做到这一点，因为这超出了我们的范围。

我们要添加的最后一个路由是`/callback`路由，这将是 Twitter 在授权后调用的路由：

```py
@app.route('/callback')
def callback():

    global req_token
    global consumer

    config = read_config()

    oauth_verifier = request.args.get('oauth_verifier', '')

    token = oauth.Token(req_token.oauth_token,
                        req_token.oauth_token_secret)

    token.set_verifier(oauth_verifier)

    client = oauth.Client(consumer, token)

    resp, content = client.request(config.access_token_url, 'POST')
    access_token = dict(parse_qsl(content.decode('utf-8')))

    with open('.twitterauth', 'w') as req_auth:
        file_content = yaml.dump(access_token, 
        default_flow_style=False)
        req_auth.write(file_content)

    return 'All set! You can close the browser window and stop the 
    server.'
```

回调路由的实现从使用全局语句开始，这样我们就可以使用全局变量`req_token`和`consumer`。

现在我们来到了有趣的部分。在授权后，Twitter 会返回一个`outh_verifier`，所以我们从请求参数中获取它并将其设置为变量`oauth_verifier`；我们使用在授权过程的第一部分中获得的`oauth_token`和`oauth_token_secret`创建一个`Token`实例。

然后我们在`Token`对象中设置`oauth_verifier`，最后创建一个新的客户端，我们将使用它来执行一个新的请求。

我们解码从请求接收到的数据，并将其添加到访问令牌变量中，最后，我们将`access_token`的内容写入`twittervotes`目录中的`.twitterauth`文件。这个文件也是 YAML 格式，所以我们将在`config.py`文件中添加另一个模型和一个新的函数来读取新的设置。

请注意，这个过程只需要做一次。这就是我们将数据存储在`.twitterauth`文件中的原因。进一步的请求只需要使用这个文件中包含的数据。

如果您检查`.twitterauth`文件的内容，您应该有类似以下的内容：

```py
oauth_token: 31******95-**************************rt*****io
oauth_token_secret: NZH***************************************ze8v
screen_name: the8bitcoder
user_id: '31******95'
x_auth_expires: '0'
```

要完成 Flask 应用程序，我们需要在文件末尾添加以下代码：

```py
if __name__ == '__main__':
    app.run(host='localhost', port=3000)
```

让我们在`twittervotes/core/models/`中的`models.py`文件中添加一个新的模型，内容如下：

```py
RequestAuth = namedtuple('RequestAuth', ['oauth_token',
                                         'oauth_token_secret',
                                         'user_id',
                                         'screen_name',
                                         'x_auth_expires', ])
```

太棒了！还有一件事——我们需要在`twittervotes/core/models`目录中的`__init__.py`文件中导入新的模型：

```py
from .models import RequestAuth
```

另外，让我们在`twittervotes/core`中的`config.py`文件中添加一个函数来读取`.twittervotes`文件。首先，我们需要导入我们刚刚创建的`RequestAuth`——`namedtuple`：

```py
from .models import RequestAuth
```

然后我们创建一个名为`read_reqauth`的函数，如下所示：

```py
def read_reqauth():
    try:
        return _read_yaml_file('.twitterauth', RequestAuth)
    except IOError as e:
        print(('It seems like you have not authorized the  
        application.\n'
               'In order to use your twitter data, please run the '
               'auth.py first.'))
```

这个函数非常简单：我们只是调用`_read_yaml_file`，将`.twitterauth`文件和我们刚刚创建的新的`namedtuple`，`RequestAuth`作为参数传递进去。同样，如果发生错误，我们会引发异常并显示帮助消息。

现在我们可以尝试进行身份验证。在`twittervotes`目录中，执行脚本`twitter_auth.py`。您应该会看到以下输出：

![](img/a1052ea8-4738-468c-8caf-a568a927ddb4.png)

太棒了！服务器已经启动，所以我们可以打开浏览器，转到`http://localhost:3000`。您应该会看到一个非常简单的页面，上面有一个链接可以进行身份验证：

![](img/f28c4eee-25af-45b5-9e14-5393bcf250dd.png)

如果您使用浏览器开发工具检查链接，您将看到链接指向授权端点，并传递了我们创建的`oauth_token`：

![](img/a23ce439-f6f8-4e4d-ac05-2552bf452cf7.png)

继续点击链接，您将被发送到授权页面：

![](img/8d73d3dc-f1c8-409d-b33a-f7833b594ba1.png)

如果您点击“授权应用”按钮，您将被重定向回本地主机，并显示成功消息：

![](img/ce6bc11b-7601-4b60-847b-4bdbb247c79a.png)

如果您注意到 Twitter 发送给我们的 URL，您会发现一些信息。这里的重点是`oauth_verifier`，我们将其设置为请求令牌，然后我们执行最后一个请求以获取访问令牌。现在您可以关闭浏览器，停止 Flask 应用程序，并在`twittervotes`目录中的`.twitterauth`文件中查看结果：

```py
oauth_token: 31*******5-KNAbN***********************K40
oauth_token_secret: d**************************************Y3
screen_name: the8bitcoder
user_id: '31******95'
x_auth_expires: '0'
```

现在，我们在这里实现的所有功能对于其他用户使用我们的应用程序非常有用；然而，如果您正在授权自己的 Twitter 应用程序，有一种更简单的方法可以获取访问令牌。让我们看看如何做到这一点。

返回到[`apps.twitter.com/`](https://apps.twitter.com/)中的 Twitter 应用程序设置；选择 Keys and Access Tokens 选项卡并滚动到最底部。如果您已经授权了这个应用程序，您将在`.twitterauth`文件中看到与我们现在相同的信息，但如果您还没有授权该应用程序，您将看到一个看起来像下面这样的 Your Access Token 部分：

![](img/a155fb51-8b1d-443a-bb29-9eebada375ce.png)

如果您点击“创建我的访问令牌”，Twitter 将为您生成访问令牌：

![](img/48983a7f-97bd-4851-a5a1-45ddc1151e89.png)

访问令牌创建后，您只需将数据复制到`.twitterauth`文件中。

# 构建 Twitter 投票应用程序

现在我们的环境已经设置好，我们已经看到了如何在 Twitter 上创建一个应用程序并执行三条腿的身份验证，现在是时候开始构建实际的应用程序来计算 Twitter 投票了。

我们首先创建一个模型类来表示一个标签。在`twittervotes/core/twitter`目录中创建一个名为`hashtag.py`的文件，内容如下：

```py
class Hashtag:
    def __init__(self, name):
        self.name = name
        self.total = 0
  self.refresh_url = None
```

这是一个非常简单的类。我们可以将一个名称作为参数传递给初始化程序；名称是没有井号(`#`)的标签。在初始化程序中，我们定义了一些属性：名称，将设置为我们传递给初始化程序的参数，然后是一个名为`total`的属性，它将为我们保留标签的使用次数。

最后，我们设置`refresh_url`。`refresh_url`将用于执行对 Twitter API 的查询，这里有趣的部分是`refresh_url`已经包含了最新返回的 tweet 的`id`，因此我们可以使用它来仅获取我们尚未获取的 tweet，以避免多次计数相同的 tweet。

`refresh_url`看起来像下面这样：

```py
refresh_url': '?since_id=963341767532834817&q=%23python&result_type=mixed&include_entities=1
```

现在我们可以打开`twittervotes/core/twitter`目录中的`__init__.py`文件，并导入我们刚刚创建的类，如下所示：

```py
from .hashtag import Hashtag
```

太棒了！现在继续在`twittervotes/core/`目录中创建一个名为`request.py`的文件。

像往常一样，我们开始添加一些导入：

```py
import oauth2 as oauth
import time
from urllib.parse import parse_qsl
import json

import requests

from .config import read_config
from .config import read_reqauth
```

首先，我们导入`oauth2`包，我们将使用它来执行身份验证；我们准备请求，并用`SHA1`密钥对其进行签名。我们还导入`time`来设置`OAuth`时间戳设置。我们导入函数`parse_qsl`，我们将使用它来解析查询字符串，以便我们可以准备一个新的请求来搜索最新的 tweets，以及`json`模块，这样我们就可以反序列化 Twitter API 发送给我们的 JSON 数据。

然后，我们导入我们自己的函数`read_config`和`read_req_auth`，这样我们就可以读取两个配置文件。最后，我们导入`json`包来解析结果和`requests`包来执行对 Twitter 搜索端点的实际请求：

```py
def prepare_request(url, url_params):
    reqconfig = read_reqauth()
    config = read_config()

    token = oauth.Token(
        key=reqconfig.oauth_token,
        secret=reqconfig.oauth_token_secret)

    consumer = oauth.Consumer(
        key=config.consumer_key,
        secret=config.consumer_secret)

    params = {
        'oauth_version': "1.0",
        'oauth_nonce': oauth.generate_nonce(),
        'oauth_timestamp': str(int(time.time()))
    }

    params['oauth_token'] = token.key
    params['oauth_consumer_key'] = consumer.key

    params.update(url_params)

    req = oauth.Request(method="GET", url=url, parameters=params)

    signature_method = oauth.SignatureMethod_HMAC_SHA1()
    req.sign_request(signature_method, consumer, token)

    return req.to_url()
```

这个函数将读取两个配置文件——`config.org`配置文件包含我们需要的所有端点 URL，以及消费者密钥。`.twitterauth`文件包含我们将用于创建`Token`对象的`oauth_token`和`oauth_token_secret`。

之后，我们定义一些参数。根据 Twitter API 文档，`oauth_version`应该始终设置为`1.0`。我们还发送`oauth_nonce`，这是我们必须为每个请求生成的唯一令牌，最后是`oauth_timestamp`，这是请求创建的时间。Twitter 将拒绝在发送请求之前太长时间创建的请求。

我们附加到参数的最后一件事是`oauth_token`，它是存储在`.twitterath`文件中的令牌，以及消费者密钥，它是存储在`config.yaml`文件中的密钥。

我们执行一个请求来获取授权，如果一切顺利，我们用 SHA1 密钥对请求进行签名，并返回请求的 URL。

现在我们要添加一个函数，该函数将执行一个请求来搜索特定的标签，并将结果返回给我们。让我们继续添加另一个名为`execute_request`的函数：

```py
def execute_request(hashtag):
    config = read_config()

 if hashtag.refresh_url:
        refresh_url = hashtag.refresh_url[1:]
        url_params = dict(parse_qsl(refresh_url))
 else:
        url_params = {
            'q': f'#{hashtag.name}',
            'result_type': 'mixed'
  }

    url = prepare_request(config.search_endpoint, url_params)

    data = requests.get(url)

    results = json.loads(data.text)

    return (hashtag, results, )
```

这个函数将以`Hashtag`对象作为参数，并且在这个函数中我们要做的第一件事是读取配置文件。然后我们检查`Hashtag`对象的`refresh_url`属性是否有值；如果有，我们将删除`refresh_url`字符串前面的`?`符号。

之后，我们使用函数`parse_qsl`来解析查询字符串，并返回一个元组列表，其中元组中的第一项是参数的名称，第二项是其值。例如，假设我们有一个看起来像这样的查询字符串：

```py
'param1=1&param2=2&param3=3'
```

如果我们使用`parse_qsl`，将这个查询字符串作为参数传递，我们将得到以下列表：

```py
[('param1', '1'), ('param2', '2'), ('param3', '3')]
```

然后，如果我们将这个结果传递给`dict`函数，我们将得到一个像这样的字典：

```py
{'param1': '1', 'param2': '2', 'param3': '3'}
```

如我之前所示，`refresh_url`的格式如下：

```py
refresh_url': '?since_id=963341767532834817&q=%23python&result_type=mixed&include_entities=1
```

在解析和转换为字典之后，我们可以使用它来获取底层标签的刷新数据。

如果`Hashtag`对象没有设置`refresh_url`属性，那么我们只需定义一个字典，其中`q`是标签名称，结果类型设置为`mixed`，告诉 Twitter API 它应该返回热门、最新和实时的推文。

在定义了搜索参数之后，我们使用上面创建的`prepare_request`函数来授权请求并对其进行签名；当我们得到 URL 后，我们使用从`prepare_request`函数得到的 URL 执行请求。

我们使用`json.loads`函数来解析 JSON 数据，并返回一个包含第一项，即标签本身的元组；第二项将是我们从请求中得到的结果。

最后一步，像往常一样，在核心模块的`__init__.py`文件中导入`execute_request`函数：

```py
from .request import execute_request
```

让我们看看这在 Python REPL 中是如何工作的：

![](img/4df954cf-1c1b-40f6-951d-3c6d7acaa55d.png)

上面的输出比这个要大得多，但其中很多都被省略了；我只是想演示一下这个函数是如何工作的。

# 增强我们的代码

我们还希望为我们的用户提供良好的体验，因此我们将添加一个命令行解析器，这样我们的应用程序的用户可以在开始投票过程之前指定一些参数。我们将只实现一个参数，即`--hashtags`，用户可以传递一个以空格分隔的标签列表。

说到这一点，我们将为这些参数定义一些规则。首先，我们将限制我们要监视的标签的最大数量，因此我们将添加一个规则，即不能使用超过四个标签。

如果用户指定了超过四个标签，我们将简单地在终端上显示一个警告，并选择前四个标签。我们还希望删除重复的标签。

在显示我们谈论过的这些警告消息时，我们可以简单地在终端上打印它们，这肯定会起作用；然而，我们想要让事情变得更有趣，所以我们将使用日志包来做这件事。除此之外，实现适当的日志记录将使我们对我们想要拥有的日志类型以及如何向用户呈现它有更多的控制。

在我们开始实现命令行解析器之前，让我们添加日志记录器。在`twittervotes/core`目录中创建一个名为`app_logger.py`的文件，内容如下：

```py
import os
import logging
from logging.config import fileConfig

def get_logger():
    core_dir = os.path.dirname(os.path.abspath(__file__))
    file_path = os.path.join(core_dir, '..', 'logconfig.ini')
    fileConfig(file_path)
    return logging.getLogger('twitterVotesLogger')
```

这个函数并没有做太多事情，但首先我们导入`os`模块，然后导入日志包，最后导入`fileConfig`函数，它从配置文件中读取日志配置。这个配置文件必须是`configparser`格式的，你可以在[`docs.python.org/3.6/library/logging.config.html#logging-config-fileformat`](https://docs.python.org/3.6/library/logging.config.html#logging-config-fileformat)获取有关这种格式的更多信息。

在我们读取配置文件之后，我们只返回一个名为`twitterVotesLogger`的记录器。

让我们看看我们的应用程序的配置文件是什么样的。在`twittervotes`目录中创建一个名为`logconfig.ini`的文件，内容如下：

```py
[loggers]
keys=root,twitterVotesLogger

[handlers]
keys=consoleHandler

[formatters]
keys=simpleFormatter

[logger_root]
level=INFO
handlers=consoleHandler

[logger_twitterVotesLogger]
level=INFO
handlers=consoleHandler
qualname=twitterVotesLogger

[handler_consoleHandler]
class=StreamHandler
level=INFO
formatter=simpleFormatter
args=(sys.stdout,)

[formatter_simpleFormatter]
format=[%(levelname)s] %(asctime)s - %(message)s
datefmt=%Y-%m-%d %H:%M:%S
```

因此，我们在这里定义了两个记录器，`root`和`twitterVotesLogger`；记录器负责公开我们可以在运行时使用的记录消息的方法。也是通过记录器，我们可以设置严重程度的级别，例如`INFO`，`DEBUG`等。最后，记录器将日志消息传递给适当的处理程序。

在我们的`twitterVotesLogger`的定义中，我们将严重级别设置为`INFO`，将处理程序设置为`consoleHandler`（我们将很快描述这一点），并设置一个限定名称，以便在需要获取`twitterVotesLogger`时使用。

`twitterVotesLoggers`的最后一个选项是`propagate`。由于`twitterVotesLogger`是子记录器，我们不希望通过`twittersVotesLogger`发送的日志消息传播到其祖先。如果将`propagate`设置为`0`，则由于`twitterVotesLogger`的祖先是`root`记录器，每条日志消息都会显示两次。

日志配置中的下一个组件是处理程序。处理程序是将特定记录器的日志消息发送到目的地的组件。我们定义了一个名为`consoleHandler`的处理程序，类型为`StreamHandler`，这是日志模块的内置处理程序。`StreamHandler`将日志消息发送到诸如`sys.stdout`、`sys.stderr`或文件之类的流。这对我们来说非常完美，因为我们希望将消息发送到终端。

在`consoleHandler`中，我们还将严重级别设置为`INFO`，并设置了格式化程序，该格式化程序设置为`customFormatter`；然后我们将 args 的值设置为`(sys.stdout, )`。Args 指定日志消息将被发送到的位置；在这种情况下，我们只设置了`sys.stdout`，但如果需要，可以添加多个输出流。

此配置的最后一个组件是格式化程序`customFormatter`。格式化程序简单地定义了日志消息应该如何显示。在我们的`customFormatter`中，我们只定义了消息应该如何显示并显示日期格式。

现在我们已经设置好了日志记录，让我们添加解析命令行的函数。在`twittervotes/core`中创建一个名为`cmdline_parser.py`的文件，并添加一些导入：

```py
from argparse import ArgumentParser

from .app_logger import get_logger
```

然后我们需要添加一个函数来验证命令行参数：

```py
def validated_args(args):

    logger = get_logger()

    unique_hashtags = list(set(args.hashtags))

    if len(unique_hashtags) < len(args.hashtags):
        logger.info(('Some hashtags passed as arguments were '
                     'duplicated and are going to be ignored'))

        args.hashtags = unique_hashtags

    if len(args.hashtags) > 4:
        logger.error('Voting app accepts only 4 hashtags at the 
        time')
        args.hashtags = args.hashtags[:4]

    return args
```

`validate_args`函数只有一个参数，即由`ArgumentParser`解析的参数。在此函数中，我们首先获取刚刚创建的记录器，以便向用户发送日志消息，通知可能存在的命令行参数问题。

接下来，我们将标签列表转换为集合，以便删除所有重复的标签，然后将其转换回列表。之后，我们检查唯一标签的数量是否小于在命令行传递的原始标签数量。这意味着我们有重复，并记录一条消息通知用户。

我们进行的最后一个验证是确保我们的应用程序最多监视四个标签。如果标签列表中的项目数大于四，则我们对数组进行切片，仅获取前四个项目，并且我们还记录一条消息，通知用户只会显示四个标签。

让我们添加另一个函数`parse_commandline_args`：

```py
def parse_commandline_args():
    argparser = ArgumentParser(
        prog='twittervoting',
        description='Collect votes using twitter hashtags.')

    required = argparser.add_argument_group('require arguments')

    required.add_argument(
        '-ht', '--hashtags',
        nargs='+',
        required=True,
        dest='hashtags',
        help=('Space separated list specifying the '
 'hashtags that will be used for the voting.\n'
 'Type the hashtags without the hash symbol.'))

    args = argparser.parse_args()

    return validated_args(args)
```

当我们在第一章开发应用程序时，我们看到了`ArgumentParser`的工作原理，即天气应用程序。但是，我们仍然可以了解一下这个函数的作用。

首先，我们定义了一个`ArgumentParser`对象，定义了一个名称和描述，并创建了一个名为`required`的子组，正如其名称所示，它将包含所有必填字段。

请注意，我们实际上不需要创建这个额外的组；但是，我发现这有助于保持代码更有组织性，并且在将来有必要添加新选项时更容易维护。

我们只定义了一个参数`hashtags`。在`hashtags`参数的定义中，有一个名为`nargs`的参数，我们将其设置为`+`；这意味着我可以传递由空格分隔的无限数量的项目，如下所示：

```py
--hashtags item1 item2 item3
```

在这个函数中我们做的最后一件事是使用`parse_args`函数解析参数，并将参数通过之前展示的`validate_args`函数进行验证。

让我们在`twittervotes/core`目录中的`__init__.py`文件中导入`parse_commandline_args`函数：

```py
from .cmdline_parser import parse_commandline_args
```

现在我们需要创建一个类，帮助我们管理标签并执行诸如保持标签的得分计数、在每次请求后更新其值等任务。因此，让我们继续创建一个名为`HashtagStatsManager`的类。在`twittervotes/core/twitter`中创建一个名为`hashtagstats_manager.py`的文件，内容如下：

```py
from .hashtag import Hashtag

class HashtagStatsManager:

    def __init__(self, hashtags):

        if not hashtags:
            raise AttributeError('hashtags must be provided')

        self._hashtags = {hashtag: Hashtag(hashtag) for hashtag in 
         hashtags}

    def update(self, data):

        hashtag, results = data

        metadata = results.get('search_metadata')
        refresh_url = metadata.get('refresh_url')
        statuses = results.get('statuses')

        total = len(statuses)

        if total > 0:
            self._hashtags.get(hashtag.name).total += total
            self._hashtags.get(hashtag.name).refresh_url = 
            refresh_url

    @property
    def hashtags(self):
        return self._hashtags
```

这个类也非常简单：在构造函数中，我们获取一个标签列表并初始化一个属性`_hashtags`，它将是一个字典，其中键是标签的名称，值是`Hashtag`类的实例。

更新方法获取一个包含`Hashtag`对象和 Twitter API 返回结果的元组。首先，我们解包元组值并将其设置为`hashtag`和`results`变量。`results`字典对我们来说有两个有趣的项目。第一个是`search_metadata`；在这个项目中，我们将找到`refresh_url`，而`statuses`包含了使用我们搜索的标签的所有推文的列表。

因此，我们获得了`search_metadata`、`refresh_url`和最后`statuses`的值。然后我们计算`statuses`列表中有多少项。如果`statuses`列表中的项目数大于`0`，我们将更新底层标签的总计数以及其`refresh_url`。

然后我们在`twittervotes/core/twitter`目录中的`__init__.py`文件中导入了我们刚刚创建的`HashtagStatsManager`类：

```py
from .hashtagstats_manager import HashtagStatsManager
```

这个应用程序的核心是`Runner`类。这个类将执行一个函数并将其排入进程池。每个函数将在不同的进程中并行执行，这将使程序比我逐个执行这些函数要快得多。

让我们来看看`Runner`类是如何实现的：

```py
import concurrent.futures

from rx import Observable

class Runner:

    def __init__(self, on_success, on_error, on_complete):
        self._on_success = on_success
        self._on_error = on_error
        self._on_complete = on_complete

    def exec(self, func, items):

        observables = []

        with concurrent.futures.ProcessPoolExecutor() as executor:
            for item in items.values():
                _future = executor.submit(func, item)
                observables.append(Observable.from_future(_future))

        all_observables = Observable.merge(observables)

        all_observables.subscribe(self._on_success,
                                  self._on_error,
                                  self._on_complete)
```

`Runner`类有一个初始化器，接受三个参数；它们都是在执行的不同状态下将被调用的函数。当项目的执行成功时将调用`on_success`，当一个函数的执行由于某种原因失败时将调用`on_error`，最后当队列中的所有函数都执行完毕时将调用`on_complete`。

还有一个名为`exec`的方法，它以一个函数作为第一个参数，这个函数将被执行，第二个参数是一个`Hashtag`实例的列表。

`Runner`类中有一些有趣的东西。首先，我们使用了`concurrent.futures`模块，这是 Python 的一个非常好的补充，自 Python 3.2 以来一直存在；这个模块提供了异步执行可调用对象的方法。

`concurrent.futures`模块还提供了`ThreadPoolExecutor`，它将使用线程执行异步操作，以及`ProcessPollExecutor`，它使用进程。您可以根据自己的需求轻松切换这些执行策略。

经验法则是，如果您的函数是 CPU 绑定的，最好使用`ProcessPollExecutor`，否则，由于 Python 的**全局解释器锁**（**GIL**），您将遇到性能问题。对于 I/O 绑定的操作，我更喜欢使用`ThreadPoolExecutor`。

如果您想了解更多关于 GIL 的信息，可以查看以下维基页面：[`wiki.python.org/moin/GlobalInterpreterLock`](https://wiki.python.org/moin/GlobalInterpreterLock)。

由于我们没有进行任何 I/O 绑定的操作，我们使用`ProcessPoolExecutor`。然后，我们循环遍历项目的值，这是一个包含我们的应用程序正在监视的所有标签的字典。对于每个标签，我们将其传递给`ProcessPollExecutor`的`submit`函数，以及我们要执行的函数；在我们的情况下，它将是我们应用程序的核心模块中定义的`execute_request`函数。

`submit`函数不会返回`execute_request`函数返回的值，而是返回一个`future`对象，它封装了`execute_request`函数的异步执行。`future`对象提供了取消执行、检查执行状态、获取执行结果等方法。

现在，我们希望有一种方法在执行状态改变或完成时得到通知。这就是响应式编程派上用场的地方。

在这里，我们获取`future`对象并创建一个`Observable`。`Observables`是响应式编程的核心。`Observable`是一个可以被观察并在任何给定时间发出事件的对象。当`Observable`发出事件时，所有订阅该`Observable`的观察者都将得到通知并对这些变化做出反应。

这正是我们在这里要实现的：我们有一系列未来的执行，我们希望在这些执行状态改变时得到通知。这些状态将由我们作为`Runner`初始化器参数传递的函数处理——`_on_sucess`、`_on_error`和`_on_complete`。

完美！让我们在`twittervotes/core`目录的`__init__.py`中导入`Runner`类：

```py
from .runner import Runner
```

我们项目的最后一部分是添加应用程序的入口点。我们将使用标准库中的`Tkinter`包添加用户界面。所以让我们开始实现它。在`twittervotes`目录中创建一个名为`app.py`的文件，然后让我们从添加一些导入开始：

```py
from core import parse_commandline_args
from core import execute_request
from core import Runner

from core.twitter import HashtagStatsManager

from tkinter import Tk
from tkinter import Frame
from tkinter import Label
from tkinter import StringVar
from tkinter.ttk import Button
```

在这里，我们导入了我们创建的命令行参数解析器，`execute_request`来执行对 Twitter API 的请求，还有`Runner`类，它将帮助我们并行执行对 Twitter API 的请求。

我们还导入`HashtagStatsManager`来为我们管理标签投票结果。

最后，我们有与`tkinter`相关的所有导入。

在同一个文件中，让我们创建一个名为`Application`的类，如下所示：

```py
class Application(Frame):

    def __init__(self, hashtags=[], master=None):
        super().__init__(master)

        self._manager = HashtagStatsManager(hashtags)

        self._runner = Runner(self._on_success,
                              self._on_error,
                              self._on_complete)

        self._items = {hashtag: StringVar() for hashtag in hashtags}
        self.set_header()
        self.create_labels()
        self.pack()

        self.button = Button(self, style='start.TButton', 
                             text='Update',
                             command=self._fetch_data)
        self.button.pack(side="bottom")
```

因此，在这里，我们创建了一个名为`Application`的类，它继承自`Frame`。初始化器接受两个参数：标签，这些是我们将要监视的标签，以及 master 参数，它是一个`Tk`类型的对象。

然后我们创建一个`HashtagStatsManager`的实例，传递标签列表；我们还创建`Runner`类的一个实例，传递三个参数。这些参数是在一个执行成功时将被调用的函数，执行失败时将被调用的函数，以及所有执行完成时将被调用的函数。

然后我们有一个字典推导式，它将创建一个字典，其中键是标签，值是`Tkinter`的字符串变量，`Tkinter`世界中称为`StringVar`。我们这样做是为了以后更容易更新标签的结果。

我们调用即将实现的`set_header`和`create_labels`方法，最后调用`pack`。`pack`函数将组织小部件，如按钮和标签，并将它们放在父小部件中，本例中是`Application`。

然后我们定义一个按钮，当点击时将执行`_fetch_data`函数，并使用`pack`将按钮放在框架的底部：

```py
def set_header(self):
    title = Label(self,
                  text='Voting for hasthags',
                  font=("Helvetica", 24),
                  height=4)
    title.pack()
```

这是我之前提到的`set_header`方法；它只是创建`Label`对象并将它们放在框架的顶部。

现在我们可以添加`create_labels`方法：

```py
def create_labels(self):
    for key, value in self._items.items():
        label = Label(self,
                      textvariable=value,
                      font=("Helvetica", 20), height=3)
        label.pack()
        self._items[key].set(f'#{key}\nNumber of votes: 0')
```

`create_labels`方法循环遍历`self._items`，如果您记得的话，这是一个字典，其中键是标签的名称，值是一个字符串类型的`Tkinter`变量。

首先，我们创建一个`Label`，有趣的部分是`textvariable`参数；我们将其设置为`value`，这是与特定标签相关的`Tkinter`变量。然后我们将`Label`放在框架中，最后，我们使用`set`函数设置标签的值。

然后我们需要添加一个方法来为我们更新`Labels`：

```py
def _update_label(self, data):
    hashtag, result = data

    total = self._manager.hashtags.get(hashtag.name).total

    self._items[hashtag.name].set(
        f'#{hashtag.name}\nNumber of votes: {total}')
```

`_update_label`，顾名思义，更新特定标签的标签。数据参数是 Twitter API 返回的结果，我们从管理器中获取标签的总数。最后，我们再次使用`set`函数来更新标签。

让我们添加另一个函数，实际上会发送请求到 Twitter API 的工作：

```py
def _fetch_data(self):
    self._runner.exec(execute_request,
                      self._manager.hashtags)
```

这种方法将调用`Runner`的`exec`方法来执行执行请求 Twitter API 的函数。

然后我们需要定义处理`Runner`类中创建的`Observable`发出的事件的方法；我们首先添加处理执行错误的方法：

```py
def _on_error(self, error_message):
    raise Exception(error_message)
```

这是一个`helper`方法，只是为了在请求执行出现问题时引发异常。

然后我们添加另一个处理`Observable`执行成功的方法：

```py
def _on_success(self, data):
    hashtag, _ = data
    self._manager.update(data)
    self._update_label(data)
```

`_on_success`方法将在`Runner`的一个执行成功完成时被调用，它将只是更新管理器的新数据，并在 UI 中更新标签。

最后，我们定义一个处理所有执行完成的方法：

```py
def _on_complete(self):
    pass
```

`_on_complete`将在所有`Runner`的执行完成时被调用。我们不会使用它，所以我们只使用`pass`语句。

现在是时候实现设置应用程序并初始化 UI 的函数`start_app`了：

```py
def start_app(args):
    root = Tk()

    app = Application(hashtags=args.hashtags, master=root)
    app.master.title("Twitter votes")
    app.master.geometry("400x700+100+100")
    app.mainloop()
```

此函数创建根应用程序，设置标题，定义其尺寸，并调用`mainloop`函数，以便应用程序保持运行。

最后一步是定义`main`函数：

```py
def main():
    args = parse_commandline_args()
    start_app(args)

if __name__ == '__main__':
    main()
```

`main`函数非常简单。首先，我们解析命令行参数，然后启动应用程序，并将命令行参数传递给它。

让我们看看应用程序的运行情况！运行以下命令：

```py
python app.py --help
```

您将看到以下输出：

![](img/c3d0e26c-fbeb-405e-9809-f6a8547098ef.png)

假设我们希望投票过程运行 3 分钟，并且它将监视`#debian`，`#ubuntu`和`#arch`这些标签：

```py
python app.py --hashtags debian ubuntu arch
```

然后您应该看到以下 UI：

![](img/0b4368f8-3691-4d0c-b79d-48b2dcfb5bd2.png)

如果您点击更新按钮，每个标签的计数都将被更新。

# 总结

在本章中，我们开发了一个在 Twitter 上投票的应用程序，并学习了 Python 编程语言的不同概念和范式。

通过创建标签投票应用程序，您已经学会了如何创建和配置 Twitter 应用程序，以及如何实现三条腿的`OAuth`身份验证来消费 Twitter API 的数据。

我们还学会了如何使用日志记录模块向我们的应用程序用户显示信息消息。与之前的模块一样，我们还使用标准库中的`ArgumentParser`模块创建了一个命令行解析器。

我们还介绍了使用`Rx`（Python 的响应式扩展）模块进行响应式编程。然后我们使用`concurrent.futures`模块来增强我们应用程序的性能，以并行方式运行多个请求到 Twitter API。

最后，我们使用`Tkinter`模块构建了一个用户界面。

在下一章中，我们将构建一个应用程序，该应用程序将从网站[`fixer.io`](http://fixer.io)获取汇率数据以进行货币转换。
