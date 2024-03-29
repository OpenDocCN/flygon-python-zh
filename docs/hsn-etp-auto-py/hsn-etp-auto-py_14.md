# 第十四章：创建和管理 VMware 虚拟机

很长一段时间以来，虚拟化一直是 IT 行业中的重要技术，因为它为硬件资源提供了高效的方式，并允许我们轻松地管理**虚拟机**（**VM**）内的应用程序生命周期。2001 年，VMware 发布了 ESXi 的第一个版本，可以直接在**现成的商用服务器**（**COTS**）上运行，并将其转换为可以被多个独立虚拟机使用的资源。在本章中，我们将探索许多可用于通过 Python 和 Ansible 自动构建虚拟机的选项。

本章将涵盖以下主题：

+   设置实验室环境

+   使用 Jinja2 生成 VMX 文件

+   VMware Python 客户端

+   使用 Ansible Playbooks 管理实例

# 设置环境

在本章中，我们将在 Cisco UCS 服务器上安装 VMware ESXi 5.5，并托管一些虚拟机。我们需要在 ESXi 服务器中启用一些功能，以便将一些外部端口暴露给外部世界：

1.  首先要启用 ESXi 控制台的 Shell 和 SSH 访问。基本上，ESXi 允许您使用 vSphere 客户端来管理它（基于 C#的 5.5.x 版本之前和基于 HTML 的 6 及更高版本）。一旦我们启用了 Shell 和 SSH 访问，这将使我们能够使用 CLI 来管理虚拟基础架构，并执行诸如创建、删除和自定义虚拟机等任务。

1.  访问 ESXi vSphere 客户端，转到“配置”，然后从左侧选项卡中选择“安全配置文件”，最后点击“属性”：

![](img/00171.jpeg)

将打开一个弹出窗口，其中包含服务、状态和各种可以应用的选项：

1.  选择 SSH 服务，然后点击“选项”。将打开另一个弹出窗口。

1.  在启动策略下选择第一个选项，即如果有任何端口打开则自动启动，并在所有端口关闭时停止。

1.  此外，点击“服务命令”下的“启动”，然后点击“确定”：

![](img/00172.jpeg)

再次为 ESXi Shell 服务重复相同的步骤。这将确保一旦 ESXi 服务器启动，两个服务都将启动，并且将打开并准备好接受连接。您可以测试两个服务，通过 SSH 连接到 ESXi IP 地址，并提供 root 凭据：

![](img/00173.jpeg)

# 使用 Jinja2 生成 VMX 文件

虚拟机（有时称为客户机）的基本单元是 VMX 文件。该文件包含构建虚拟机所需的所有设置，包括计算资源、分配的内存、硬盘和网络。此外，它定义了在机器上运行的操作系统，因此 VMware 可以安装一些工具来管理 VM 的电源。

还需要一个额外的文件：VMDK。该文件存储 VM 的实际内容，并充当 VM 分区的硬盘：

![](img/00174.jpeg)

这些文件（VMX 和 VMDK）应存储在 ESXi Shell 中的`/vmfs/volumes/datastore1`目录下，并且应该位于以虚拟机名称命名的目录中。

# 构建 VMX 模板

现在我们将创建模板文件，用于在 Python 中构建虚拟机。以下是我们需要使用 Python 和 Jinja2 生成的最终运行的 VMX 文件的示例：

```py
.encoding = "UTF-8" vhv.enable = "TRUE" config.version = "8" virtualHW.version = "8"   vmci0.present = "TRUE" hpet0.present = "TRUE" displayName = "test_jinja2"   # Specs memSize = "4096" numvcpus = "1" cpuid.coresPerSocket = "1"     # HDD scsi0.present = "TRUE" scsi0.virtualDev = "lsilogic" scsi0:0.deviceType = "scsi-hardDisk" scsi0:0.fileName = "test_jinja2.vmdk" scsi0:0.present = "TRUE"   # Floppy floppy0.present = "false"   #  CDRom ide1:0.present = "TRUE" ide1:0.deviceType = "cdrom-image" ide1:0.fileName = "/vmfs/volumes/datastore1/ISO Room/CentOS-7-x86_64-Minimal-1708.iso"   #  Networking ethernet0.virtualDev = "e1000" ethernet0.networkName = "network1" ethernet0.addressType = "generated" ethernet0.present = "TRUE"   # VM Type guestOS = "ubuntu-64"   # VMware Tools toolScripts.afterPowerOn = "TRUE" toolScripts.afterResume = "TRUE" toolScripts.beforeSuspend = "TRUE" toolScripts.beforePowerOff = "TRUE" tools.remindInstall = "TRUE" tools.syncTime = "FALSE"
```

我在文件中添加了一些注释，以说明每个块的功能。但是，在实际文件中，您看不到这些注释。

让我们分析文件并理解一些字段的含义：

+   `vhv.enable`：当设置为`True`时，ESXi 服务器将向客户机 CPU 公开 CPU 主机标志，从而允许在客户机内运行 VM（称为嵌套虚拟化）。

+   `displayName`：在 ESXi 中注册的名称，并在 vSphere 客户端中显示的名称。

+   `memsize`：定义分配给 VM 的 RAM，应以兆字节为单位提供。

+   `numvcpus`：这定义了分配给 VM 的物理 CPU 数量。此标志与`cpuid.coresPerSocket`一起使用，因此可以定义分配的 vCPU 总数。

+   `scsi0.virtualDev`：虚拟硬盘的 SCSI 控制器类型。它可以是四个值之一：BusLogic，LSI Logic parallel，LSI Logic SAS 或 VMware paravirtual。

+   `scsi0:0.fileName`：这定义了将存储实际虚拟机设置的`vmdk`（在同一目录中）的名称。

+   `ide1:0.fileName`：包含以 ISO 格式打包的安装二进制文件的镜像路径。这将使 ESXi 连接到镜像 CD-ROM（IDE 设备）中的 ISO 镜像。

+   `ethernet0.networkName`：这是 ESXi 中应连接到 VM NIC 的虚拟交换机的名称。您可以添加此参数的其他实例，以反映其他网络接口。

现在我们将构建 Jinja2 模板；您可以查看第六章，*使用 Python 和 Jinja2 进行配置生成*，了解使用 Jinja2 语言进行模板化的基础知识：

```py
.encoding = "UTF-8" vhv.enable = "TRUE" config.version = "8" virtualHW.version = "8"   vmci0.present = "TRUE" hpet0.present = "TRUE" displayName = "{{vm_name}}"   # Specs memSize = "{{ vm_memory_size }}" numvcpus = "{{ vm_cpu }}" cpuid.coresPerSocket = "{{cpu_per_socket}}"     # HDD scsi0.present = "TRUE" scsi0.virtualDev = "lsilogic" scsi0:0.deviceType = "scsi-hardDisk" scsi0:0.fileName = "{{vm_name}}.vmdk" scsi0:0.present = "TRUE"   # Floppy floppy0.present = "false"     # CDRom ide1:0.present = "TRUE" ide1:0.deviceType = "cdrom-image" ide1:0.fileName = "/vmfs/volumes/datastore1/ISO Room/{{vm_image}}"     # Networking ethernet0.virtualDev = "e1000" ethernet0.networkName = "{{vm_network1}}" ethernet0.addressType = "generated" ethernet0.present = "TRUE"   # VM Type guestOS = "{{vm_guest_os}}" #centos-64 or ubuntu-64   # VMware Tools toolScripts.afterPowerOn = "TRUE" toolScripts.afterResume = "TRUE" toolScripts.beforeSuspend = "TRUE" toolScripts.beforePowerOff = "TRUE" tools.remindInstall = "TRUE" tools.syncTime = "FALSE"
```

请注意，我们已经删除了相关字段的静态值，比如`diplayName`，`memsize`等，并用双大括号替换为变量名。在 Python 模板渲染期间，这些字段将被实际值替换，以构建有效的 VMX 文件。

现在，让我们构建渲染文件的 Python 脚本。通常，我们会将 YAML 数据序列化与 Jinja2 结合使用，以填充模板的数据。但是，由于我们已经在[第六章](https://cdp.packtpub.com/hands_on_enterprise_automation_with_python/wp-admin/post.php?post=295&action=edit#post_33)中解释了 YAML 概念，*使用 Python 和 Jinja2 进行配置生成*，我们将从另一个数据源，Microsoft Excel 中获取数据：

![](img/00175.jpeg)

# 处理 Microsoft Excel 数据

Python 有一些出色的库，可以处理 Excel 表中的数据。在第四章中，我们已经使用了 Excel 表，*使用 Python 管理网络设备*，当我们需要自动化`netmiko`配置并读取描述 Excel 文件基础设施的数据时。现在，我们将开始在自动化服务器中安装 Python `xlrd`库。

使用以下命令安装`xlrd`：

```py
pip install xlrd
```

![](img/00176.jpeg)

按照以下步骤进行：

1.  XLRD 模块可以打开 Microsoft 工作簿，并使用`open_workbook()`方法解析内容。

1.  然后，您可以通过将工作表索引或工作表名称提供给`sheet_by_index()`或`sheet_by_name()`方法来选择包含数据的工作表。

1.  最后，您可以通过将行号提供给`row()`函数来访问行数据，该函数将行数据转换为 Python 列表：

![](img/00177.jpeg)

请注意，`nrows`和`ncols`是特殊变量，一旦打开计算工作表中的行数和列数的工作表，它们将被填充。您可以使用`for`循环进行迭代。编号始终从开始。

回到虚拟机示例。在 Excel 表中，我们将有以下数据，反映虚拟机设置：

![](img/00178.jpeg)

为了将数据读入 Python，我们将使用以下脚本：

```py
import xlrd
workbook = xlrd.open_workbook(r"/media/bassim/DATA/GoogleDrive/Packt/EnterpriseAutomationProject/Chapter14_Creating_and_managing_VMware_virtual_machines/vm_inventory.xlsx") sheet = workbook.sheet_by_index(0) print(sheet.nrows) print(sheet.ncols)   print(int(sheet.row(1)[1].value))   for row in range(1,sheet.nrows):
  vm_name = sheet.row(row)[0].value
    vm_memory_size = int(sheet.row(row)[1].value)
  vm_cpu = int(sheet.row(row)[2].value)
  cpu_per_socket = int(sheet.row(row)[3].value)
  vm_hdd_size = int(sheet.row(row)[4].value)
  vm_guest_os = sheet.row(row)[5].value
    vm_network1 = sheet.row(row)[6].value
```

在前面的脚本中，我们做了以下事情：

1.  我们导入了`xlrd`模块，并将 Excel 文件提供给`open_workbook()`方法以读取 Excel 工作表，并将其保存到`workbook`变量中。

1.  然后，我们使用`sheet_by_index()`方法访问了第一个工作表，并将引用保存到`sheet`变量中。

1.  现在，我们将遍历打开的表格，并使用`row()`方法获取每个字段。这将允许我们将行转换为 Python 列表。由于我们只需要行内的一个值，我们将使用列表切片来访问索引。请记住，列表索引始终从零开始。我们将把该值存储到变量中，并在下一部分中使用该变量来填充 Jinja2 模板。

# 生成 VMX 文件

最后一部分是从 Jinja2 模板生成 VMX 文件。我们将从 Excel 表中读取数据，并将其添加到空字典`vmx_data`中。稍后将该字典传递给 Jinja2 模板中的`render()`函数。Python 字典键将是模板变量名，而值将是应该在文件中替换的值。脚本的最后一部分是在`vmx_files`目录中以写入模式打开文件，并为每个 VMX 文件写入数据：

```py
  from jinja2 import FileSystemLoader, Environment
import os
import xlrd

print("The script working directory is {}" .format(os.path.dirname(__file__))) script_dir = os.path.dirname(__file__)   vmx_env = Environment(
  loader=FileSystemLoader(script_dir),
  trim_blocks=True,
  lstrip_blocks= True )     workbook = xlrd.open_workbook(os.path.join(script_dir,"vm_inventory.xlsx")) sheet = workbook.sheet_by_index(0) print("The number of rows inside the Excel sheet is {}" .format(sheet.nrows)) print("The number of columns inside the Excel sheet is {}" .format(sheet.ncols))     vmx_data = {}   for row in range(1,sheet.nrows):
  vm_name = sheet.row(row)[0].value
    vm_memory_size = int(sheet.row(row)[1].value)
  vm_cpu = int(sheet.row(row)[2].value)
  cpu_per_socket = int(sheet.row(row)[3].value)
  vm_hdd_size = int(sheet.row(row)[4].value)
  vm_guest_os = sheet.row(row)[5].value
    vm_network1 = sheet.row(row)[6].value

    vmx_data["vm_name"] = vm_name
    vmx_data["vm_memory_size"] = vm_memory_size
    vmx_data["vm_cpu"] = vm_cpu
    vmx_data["cpu_per_socket"] = cpu_per_socket
    vmx_data["vm_hdd_size"] = vm_hdd_size
    vmx_data["vm_guest_os"] = vm_guest_os
    if vm_guest_os == "ubuntu-64":
  vmx_data["vm_image"] = "ubuntu-16.04.4-server-amd64.iso"    elif vm_guest_os == "centos-64":
  vmx_data["vm_image"] = "CentOS-7-x86_64-Minimal-1708.iso"    elif vm_guest_os == "windows7-64":
  vmx_data["vm_image"] = "windows_7_ultimate_sp1_ x86-x64_bg-en_IE10_ April_2013.iso"    vmx_data["vm_network1"] = vm_network1

    vmx_data = vmx_env.get_template("vmx_template.j2").render(vmx_data)
  with open(os.path.join(script_dir,"vmx_files/{}.vmx".format(vm_name)), "w") as f:
  print("Writing Data of {} into directory".format(vm_name))
  f.write(vmx_data)
  vmx_data = {} 
```

脚本输出如下：

![](img/00179.jpeg)

文件存储在`vmx_files`下，每个文件都包含在 Excel 表中配置的虚拟机的特定信息：

![](img/00180.jpeg)

现在，我们将使用`paramiko`和`scp`库连接到 ESXi Shell，并将这些文件上传到`/vmfs/volumes/datastore1`下。为了实现这一点，我们将首先创建一个名为`upload_and_create_directory()`的函数，该函数接受`vm name`、`hard disk size`和 VMX`source file`。`paramiko`将连接到 ESXi 服务器并执行所需的命令，这将在`/vmfs/volumes/datastore1`下创建目录和 VMDK。最后，我们将使用`scp`模块中的`SCPClient`将源文件上传到先前创建的目录，并运行注册命令将机器添加到 vSphere 客户端：

```py
#!/usr/bin/python __author__ = "Bassim Aly" __EMAIL__ = "basim.alyy@gmail.com"   import paramiko
from scp import SCPClient
import time

def upload_and_create_directory(vm_name, hdd_size, source_file):    commands = ["mkdir /vmfs/volumes/datastore1/{0}".format(vm_name),
  "vmkfstools -c {0}g -a lsilogic -d zeroedthick /vmfs/volumes/datastore1/{1}/{1}.vmdk".format(hdd_size,
  vm_name),]
  register_command = "vim-cmd solo/registervm /vmfs/volumes/datastore1/{0}/{0}.vmx".format(vm_name)
  ipaddr = "10.10.10.115"
  username = "root"
  password = "access123"    ssh = paramiko.SSHClient()
  ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())    ssh.connect(ipaddr, username=username, password=password, look_for_keys=False, allow_agent=False)    for cmd in commands:
  try:
  stdin, stdout, stderr = ssh.exec_command(cmd)
  print " DEBUG: ... Executing the command on ESXi server".format(str(stdout.readlines()))    except Exception as e:
  print e
            pass
 print " DEBUG: **ERR....unable to execute command"
  time.sleep(2)
  with SCPClient(ssh.get_transport()) as scp:
  scp.put(source_file, remote_path='/vmfs/volumes/datastore1/{0}'.format(vm_name))
  ssh.exec_command(register_command)
  ssh.close()
```

我们需要在运行 Jinja2 模板并生成 VMX 之前定义这个函数*之前*，并在将文件保存到`vmx_files`目录并传递所需的参数后调用该函数。

最终代码应该如下所示：

```py
#!/usr/bin/python __author__ = "Bassim Aly" __EMAIL__ = "basim.alyy@gmail.com"   import paramiko
from scp import SCPClient
import time
from jinja2 import FileSystemLoader, Environment
import os
import xlrd

def upload_and_create_directory(vm_name, hdd_size, source_file):    commands = ["mkdir /vmfs/volumes/datastore1/{0}".format(vm_name),
  "vmkfstools -c {0}g -a lsilogic -d zeroedthick /vmfs/volumes/datastore1/{1}/{1}.vmdk".format(hdd_size,
  vm_name),]
  register_command = "vim-cmd solo/registervm /vmfs/volumes/datastore1/{0}/{0}.vmx".format(vm_name)    ipaddr = "10.10.10.115"
  username = "root"
  password = "access123"    ssh = paramiko.SSHClient()
  ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())    ssh.connect(ipaddr, username=username, password=password, look_for_keys=False, allow_agent=False)    for cmd in commands:
  try:
  stdin, stdout, stderr = ssh.exec_command(cmd)
  print " DEBUG: ... Executing the command on ESXi server".format(str(stdout.readlines()))    except Exception as e:
  print e
            pass
 print " DEBUG: **ERR....unable to execute command"
  time.sleep(2)
  with SCPClient(ssh.get_transport()) as scp:
  print(" DEBUG: ... Uploading file to the datastore")
  scp.put(source_file, remote_path='/vmfs/volumes/datastore1/{0}'.format(vm_name))
  print(" DEBUG: ... Register the virtual machine {}".format(vm_name))
  ssh.exec_command(register_command)    ssh.close()   print("The script working directory is {}" .format(os.path.dirname(__file__))) script_dir = os.path.dirname(__file__)   vmx_env = Environment(
  loader=FileSystemLoader(script_dir),
  trim_blocks=True,
  lstrip_blocks= True )   workbook = xlrd.open_workbook(os.path.join(script_dir,"vm_inventory.xlsx")) sheet = workbook.sheet_by_index(0) print("The number of rows inside the Excel sheet is {}" .format(sheet.nrows)) print("The number of columns inside the Excel sheet is {}" .format(sheet.ncols))     vmx_data = {}   for row in range(1,sheet.nrows):
  vm_name = sheet.row(row)[0].value
    vm_memory_size = int(sheet.row(row)[1].value)
  vm_cpu = int(sheet.row(row)[2].value)
  cpu_per_socket = int(sheet.row(row)[3].value)
  vm_hdd_size = int(sheet.row(row)[4].value)
  vm_guest_os = sheet.row(row)[5].value
    vm_network1 = sheet.row(row)[6].value

    vmx_data["vm_name"] = vm_name
    vmx_data["vm_memory_size"] = vm_memory_size
    vmx_data["vm_cpu"] = vm_cpu
    vmx_data["cpu_per_socket"] = cpu_per_socket
    vmx_data["vm_hdd_size"] = vm_hdd_size
    vmx_data["vm_guest_os"] = vm_guest_os
    if vm_guest_os == "ubuntu-64":
  vmx_data["vm_image"] = "ubuntu-16.04.4-server-amd64.iso"    elif vm_guest_os == "centos-64":
  vmx_data["vm_image"] = "CentOS-7-x86_64-Minimal-1708.iso"    elif vm_guest_os == "windows7-64":
  vmx_data["vm_image"] = "windows_7_ultimate_sp1_ x86-x64_bg-en_IE10_ April_2013.iso"    vmx_data["vm_network1"] = vm_network1

    vmx_data = vmx_env.get_template("vmx_template.j2").render(vmx_data)
  with open(os.path.join(script_dir,"vmx_files/{}.vmx".format(vm_name)), "w") as f:
  print("Writing Data of {} into directory".format(vm_name))
  f.write(vmx_data)
  print(" DEBUG:Communicating with ESXi server to upload and register the VM")
  upload_and_create_directory(vm_name,
  vm_hdd_size,
  os.path.join(script_dir,"vmx_files","{}.vmx".format(vm_name)))
  vmx_data = {}
```

脚本输出如下：

![](img/00181.jpeg)

如果在运行脚本后检查 vSphere 客户端，您会发现已经创建了四台机器，这些机器的名称是在 Excel 表中提供的：

![](img/00182.jpeg)

此外，您会发现虚拟机已经定制了诸如 CPU、内存和连接的 ISO 室等设置：

![](img/00183.jpeg)您可以通过将创建的虚拟机连接到 Cobbler 来在 VMware 中完成自动化工作流程。我们在第八章中介绍了这一点，*准备系统实验环境*。Cobbler 将自动化操作系统安装和定制，无论是 Windows、CentOS 还是 Ubuntu。之后，您可以使用我们在第十三章中介绍的 Ansible，*系统管理的 Ansible*，来准备系统的安全性、配置和已安装的软件包，然后部署您的应用程序。这是一个全栈自动化，涵盖了虚拟机创建和应用程序的运行。

# VMware Python 客户端

VMware 产品（用于管理 ESXi 的 ESXi 和 vCenter）支持通过 Web 服务接收外部 API 请求。您可以执行与 vSphere 客户端上相同的管理任务，例如创建新的虚拟机、创建新的 vSwitch，甚至控制`vm`状态，但这次是通过支持的 API 进行的，该 API 具有许多语言的绑定，例如 Python、Ruby 和 Go。

vSphere 具有库存的特殊模型，其中的每个对象都具有特定的值。您可以访问此模型，并通过**托管对象浏览器**（**MoB**）查看基础设施的实际值。我们将使用 VMware 的官方 Python 绑定（`pyvmomi`）与此模型进行交互，并更改库存中的值（或创建它们）。

值得注意的是，可以通过浏览器访问 MoB，方法是转到`http://<ESXi_server_ip_or_domain>/mob`，这将要求您提供根用户名和密码：

！[](../images/00184.jpeg)

您可以单击任何超链接以查看更多详细信息并访问每个树或上下文中的每个*叶*。例如，单击 Content.about 以查看有关服务器的完整详细信息，例如确切的版本、构建和完整名称：

！[](../images/00185.jpeg)

注意表的结构。第一列包含属性名称，第二列是该属性的数据类型，最后，第三列是实际运行值。

# 安装 PyVmomi

PyVmomi 可以通过 Python `pip`或不同仓库的系统包进行下载。

对于 Python 安装，请使用以下命令：

```py
pip install -U pyvmomi
```

！[](../images/00186.jpeg)

请注意，从`pip`下载的版本是`6.5.2017.5-1`，与 vSphere 发布的 VMware vSphere 6.5 相对应，但这并不意味着它不能与旧版本的 ESXi 一起使用。例如，我有 VMware vSphere 5.5，它可以与最新的`pyvmomi`版本完美配合使用。

系统安装：

```py
yum install pyvmomi -y
```

Pyvmomi 库使用动态类型，这意味着 IDE 中的智能感知和自动完成功能等功能无法与其一起使用。您必须依赖文档和 MoB 来发现需要哪些类或方法来完成工作，但是一旦您发现它的工作原理，就会很容易使用。

# 使用 pyvmomi 的第一步

首先，您需要通过提供用户名、密码和主机 IP 连接到 ESXi MoB，并开始导航到 MoB 以获取所需的数据。这可以通过使用`SmartConnectNoSSL()`方法来完成：

```py
from pyVim.connect import SmartConnect, Disconnect,SmartConnectNoSSL  ESXi_connection = SmartConnectNoSSL(host="10.10.10.115", user="root", pwd='access123')
```

请注意，还有另一种称为`SmartConnect()`的方法，当建立连接时必须向其提供 SSL 上下文，否则连接将失败。但是，您可以使用以下代码片段请求 SSL 不验证证书，并将此上下文传递给`SmartConnect()`的`sslCContext`参数：

```py
import ssl import requests certificate = ssl.SSLContext(ssl.PROTOCOL_TLSv1) certificate.verify_mode = ssl.CERT_NONE
requests.packages.urllib3.disable_warnings()    
```

为了严谨性和保持代码简洁，我们将使用内置的`SmartConnectNoSSL()`。

接下来，我们将开始探索 MoB 并在`about`对象中获取服务器的完整名称和版本。请记住，它位于`content`对象下，所以我们也需要访问它：

```py
#!/usr/bin/python __author__ = "Bassim Aly" __EMAIL__ = "basim.alyy@gmail.com"   from pyVim.connect import SmartConnect, Disconnect,SmartConnectNoSSL
ESXi_connection = SmartConnectNoSSL(host="10.10.10.115", user="root", pwd='access123')   full_name = ESXi_connection.content.about.fullName
version = ESXi_connection.content.about.version
print("Server Full name is {}".format(full_name)) print("ESXi version is {}".format(version))
Disconnect(ESXi_connection) 
```

输出如下：

**！[](../images/00187.jpeg)**

现在我们了解了 API 的工作原理。让我们进入一些严肃的脚本，并检索有关我们 ESXi 中部署的虚拟机的一些详细信息。

脚本如下：

```py
#!/usr/bin/python __author__ = "Bassim Aly" __EMAIL__ = "basim.alyy@gmail.com"   from pyVim.connect import SmartConnect, Disconnect,SmartConnectNoSSL

ESXi_connection = SmartConnectNoSSL(host="10.10.10.115", user="root", pwd='access123')   datacenter = ESXi_connection.content.rootFolder.childEntity[0] #First Datacenter in the ESXi\   virtual_machines = datacenter.vmFolder.childEntity #Access the child inside the vmFolder   print virtual_machines

for machine in virtual_machines:
  print(machine.name)
  try:
  guest_vcpu = machine.summary.config.numCpu
        print("  The Guest vCPU is {}" .format(guest_vcpu))    guest_os = machine.summary.config.guestFullName
        print("  The Guest Operating System is {}" .format(guest_os))    guest_mem = machine.summary.config.memorySizeMB
        print("  The Guest Memory is {}" .format(guest_mem))    ipadd = machine.summary.guest.ipAddress
        print("  The Guest IP Address is {}" .format(ipadd))
  print "================================="
  except:
  print("  Can't get the summary")
```

在前面的示例中，我们做了以下事情：

1.  我们再次通过向`SmartConnectNoSSL`方法提供 ESXi/vCenter 凭据来建立 API 连接到 MoB。

1.  然后，我们通过访问`content`然后`rootFolder`对象和最后`childEntity`来访问数据中心对象。返回的对象是可迭代的，因此我们访问了第一个元素（实验室中只有一个 ESXi 的第一个数据中心）。您可以遍历所有数据中心以获取所有注册数据中心中所有虚拟机的列表。

1.  虚拟机可以通过`vmFolder`和`childEntity`访问。同样，请记住返回的输出是可迭代的，并表示存储在`virtual_machines`变量中的虚拟机列表：

！[](../images/00188.jpeg)

1.  我们遍历了`virtual_machines`对象，并查询了每个元素（每个虚拟机）的 CPU、内存、全名和 IP 地址。这些元素位于`summary`和`config`叶子下的每个虚拟机树中。以下是我们的`AutomationServer`设置的示例：

![](img/00189.jpeg)

脚本输出如下：

请注意，在本章的开头我们创建的`python-vm`虚拟机在最后一个截图中显示出来。您可以使用 PyVmomi 作为验证工具，与您的自动化工作流集成，验证虚拟机是否正在运行，并根据返回的输出做出决策。

# 更改虚拟机状态

这次我们将使用`pyvmomi`绑定来更改虚拟机状态。这将通过检查虚拟机名称来完成；然后，我们将导航到 MoB 中的另一个树，并获取运行时状态。最后，我们将根据其当前状态在机器上应用`PowerOn()`或`PowerOff()`函数。这将把机器状态从`On`切换到`Off`，反之亦然。

脚本如下：

```py
#!/usr/bin/python __author__ = "Bassim Aly" __EMAIL__ = "basim.alyy@gmail.com"   from pyVim.connect import SmartConnect, Disconnect,SmartConnectNoSSL

ESXi_connection = SmartConnectNoSSL(host="10.10.10.115", user="root", pwd='access123')   datacenter = ESXi_connection.content.rootFolder.childEntity[0] #First Datacenter in the ESXi\   virtual_machines = datacenter.vmFolder.childEntity #Access the child inside the vmFolder   for machine in virtual_machines:
  try:
  powerstate = machine.summary.runtime.powerState
        if "python-vm" in machine.name and powerstate == "poweredOff":
  print(machine.name)
  print("     The Guest Power state is {}".format(powerstate))
  machine.PowerOn()
  print("**Powered On the virtual machine**")    elif "python-vm" in machine.name and powerstate == "poweredOn":
  print(machine.name)
  print("     The Guest Power state is {}".format(powerstate))
  machine.PowerOff()
  print("**Powered Off the virtual machine**")
  except:
  print("  Can't execute the task")   Disconnect(ESXi_connection)
```

脚本输出如下：

![](img/00191.jpeg)

此外，您还可以从 vSphere 客户端验证虚拟机状态，并检查以`python-vm*`开头的主机，将它们的电源状态从`poweredOff`更改为`poweredOn`：

![](img/00192.gif)

# 还有更多

您可以在 GitHub 的官方 VMware 存储库中找到基于`pyvmomi`绑定的许多有用的脚本（使用不同的语言）（[`github.com/vmware/pyvmomi-community-samples/tree/master/samples`](https://github.com/vmware/pyvmomi-community-samples/tree/master/samples)）。这些脚本由许多使用工具并在日常基础上测试它们的贡献者提供。大多数脚本提供了输入您的配置（如 ESXi IP 地址和凭据）的空间，而无需通过提供它作为参数修改脚本源代码。

# 使用 Ansible Playbook 管理实例

在 VMware 自动化的最后部分，我们将利用 Ansible 工具来管理 VMware 基础架构。Ansible 附带了 20 多个 VMware 模块（[`docs.ansible.com/ansible/latest/modules/list_of_cloud_modules.html#vmware`](http://docs.ansible.com/ansible/latest/modules/list_of_cloud_modules.html#vmware)），可以执行许多任务，如管理数据中心、集群和虚拟机。在较旧的 Ansible 版本中，Ansible 使用`pysphere`模块（这不是官方的；模块的作者自 2013 年以来就没有维护过它）来自动化任务。然而，新版本现在支持`pyvmomi`绑定。

Ansible 还支持 VMware SDN 产品（NSX）。Ansible Tower 可以从**VMware vRealize Automation**（**vRA**）访问，允许在不同工具之间实现完整的工作流集成。

以下是 Ansible Playbook：

```py
- name: Provision New VM
  hosts: localhost
  connection: local
  vars:
  - VM_NAME: DevOps
  - ESXi_HOST: 10.10.10.115
  - USERNAME: root
  - PASSWORD: access123
  tasks:   - name: current time
  command: date +%D
  register: current_time
  - name: Check for vSphere access parameters
  fail: msg="Must set vsphere_login and vsphere_password in a Vault"
  when: (USERNAME is not defined) or (PASSWORD is not defined)
  - name: debug vCenter hostname
  debug: msg="vcenter_hostname = '{{ ESXi_HOST }}'"
  - name: debug the time
  debug: msg="Time is = '{{ current_time }}'"    - name: "Provision the VM"
  vmware_guest:
 hostname: "{{ ESXi_HOST }}"
  username: "{{ USERNAME }}"
  password: "{{ PASSWORD }}"
  datacenter: ha-datacenter
  validate_certs: False
  name: "{{ VM_NAME }}"
  folder: /
  guest_id: centos64Guest
  state: poweredon
  force: yes
  disk:
  - size_gb: 100
  type: thin
  datastore: datastore1    networks:
  - name: network1
  device_type: e1000 #            mac: ba:ba:ba:ba:01:02 #            wake_on_lan: True    - name: network2
  device_type: e1000    hardware:
 memory_mb: 4096
  num_cpus: 4
  num_cpu_cores_per_socket: 2
  hotadd_cpu: True
  hotremove_cpu: True
  hotadd_memory: True
  scsi: lsilogic
  cdrom:
 type: "iso"
  iso_path: "[datastore1] ISO Room/CentOS-7-x86_64-Minimal-1708.iso"
  register: result 
```

在前面的 playbook 中，我们可以看到以下内容：

+   playbook 的第一部分是在`vars`部分中定义 ESXi 主机 IP 和凭据，并在后续任务中使用它们。

+   然后我们编写了一个简单的验证，如果未提供用户名或密码，就会使 playbook 失败。

+   然后，我们使用 ansible 提供的`vmware_guest`模块（[`docs.ansible.com/ansible/2.4/vmware_guest_module.html`](https://docs.ansible.com/ansible/2.4/vmware_guest_module.html)）来提供虚拟机。在这个任务中，我们提供了所需的信息，比如磁盘大小和 CPU 和内存方面的硬件。请注意，我们将虚拟机的状态定义为`poweredon`，因此 ansible 将在创建后启动虚拟机。

+   磁盘、网络、硬件和 CD-ROM 都是`vmware_guest`模块中的关键，用于描述在 VMware ESXi 上生成新 VM 所需的虚拟化硬件规格。

使用以下命令运行 playbook：

```py
# ansible-playbook esxi_create_vm.yml -vv
```

以下是 Playbook 输出的截图：

![](img/00193.jpeg)

您可以在 vSphere 客户端中验证虚拟机的创建和与 CentOS ISO 文件的绑定：

![](img/00194.jpeg)

您还可以通过更改剧本中“状态”中的值来更改现有虚拟机的状态，并从`poweredon`、`poweredoff`、`restarted`、`absent`、`suspended`、`shutdownguest`和`rebootguest`中进行选择。

# 摘要

VMware 产品广泛用于 IT 基础设施中，为运行应用程序和工作负载提供虚拟化环境。与此同时，VMware 还提供了许多语言的 API 绑定，可用于自动化管理任务。在下一章中，我们将探索另一个名为 OpenStack 的虚拟化框架，该框架依赖于红帽公司的 KVM hypervisor。
