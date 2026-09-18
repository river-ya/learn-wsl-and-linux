# WSL基本指令

WSL（Windows Subsystem for Linux）

下面的 WSL 命令以 PowerShell 或 Windows 命令提示符支持的格式列出。 要从 Bash/Linux 发行版的命令行运行这些命令，必须将 ``wsl`` 替换为 ``wsl.exe``。 完整的命令列表，请运行 `wsl --help`。 

## CMD

CMD（Command Prompt）

- 基于古老的 **MS-DOS**（Microsoft Disk Operator System），是 Windows 的传统命令行解释器。
- 主要用于执行简单的批处理（`.bat` 或 `.cmd` 文件）和基础系统命令。
- 功能有限，依赖外部程序（如 `findstr`、`xcopy`）完成复杂任务。

## PowerShell

- 由 Microsoft 设计，是一种现代化的 **脚本语言和 shell**（2006 年推出）。
- 基于 **.NET Framework/.NET Core**，支持面向对象和自动化管理。
- 定位为系统管理和自动化工具，尤其适合 IT 运维和开发。

## 安装WSL

```powershell
wsl --install
```

安装 WSL 和 Linux 的默认 Ubuntu 分发版

还可以使用此命令通过运行 `wsl --install <Distribution Name>`来安装其他 Linux 分发版。 对于有效的发行版名称列表，请运行 `wsl --list --online`。

```powershell
wsl --list --online
```

<img src="./wsl_distribution.png" alt="wsl有效分发版" width="50%">

## 列出已安装的Linux分发版

```powershell
wsl --list --verbose
# 等价于 wsl -l -v
```

可用于列表命令的其他选项包括： `--all` 列出所有通讯组、 `--running` 仅列出当前正在运行的分发版或 `--quiet` 仅显示分发名称

(或-q)。

```powershell
wsl -l --all
wsl -l --running
wsl -l --quiet
```

  - 双横线 --：后面接完整名称，例如 --distribution，容易理解。
  - 单横线 -：后面接简写，例如 -d，输入更方便。

  比如下面两条命令意思一样：

```powershell
wsl --install --distribution Ubuntu
wsl --install -d Ubuntu
```

## 在用户主目录中启动WSL

```powershell
wsl ~
```

~`可与 wsl 一起使用，以在用户的主目录中启动。 若要从 WSL 命令提示符内从任何目录跳转回主页，可以使用以下命令： `

```powershell
cd ~
```

## 从PowerShell或CMD运行特定的Linux分发版

```powershell
wsl --distribution <Distribution Name> --user <User Name>
```

若要使用特定用户运行特定 Linux 发行版，请将 `<Distribution Name>` 替换为您首选的 Linux 发行版的名称（即 Debian），并将 `<User Name>` 替换为现有用户的名称（即 root）。 如果 WSL 分发中不存在用户，将收到错误。 若要打印当前用户名，请使用一下命令 

```powershell
whoami
```

## 更新WSL

```powershell
wsl --update
```

将 WSL 版本更新为最新版本。 选项包括：

- `--web-download`：从 GitHub 下载最新更新，而不是 Microsoft 商店。

## 检查WSL状态

```powershell
wsl --status
```

请参阅有关 WSL 配置的常规信息，例如默认分发类型、默认分发和内核版本。

## 检查WSL版本

```powershell
wsl --version
```

## Help命令

```powershell
wsl --help
```

请参阅 WSL 可用的选项和命令列表。

## 以特定用户身份运行

```powershell
wsl --user <Username>
```

## 更改分发版的默认用户

```powershell
<DistributionName> config --default-user <Username>
```

更改分发版登录的默认用户。 用户必须已存在于分发中，才能成为默认用户。

## 关机

```powershell
wsl --shutdown
```

停止所有正在运行的分发版，同时关闭 WSL 2 的后台虚拟机。

## 终止

```powershell
wsl --terminate <DistributionName>
```

只停止指定分发版，其他分发版可以继续运行。

## 使用WSL访问网络应用程序

默认情况下，WSL 使用 基于 NAT 的体系结构，建议尝试新的 镜像网络模式 来获取最新的功能和改进。

### NAT

 NAT（网络地址转换 Network Address Translation）就是：数据经过网络出口时，把其中的 IP 地址转换一下，让内部设备能够通过出口访问外部网络。

  以 WSL 的 NAT 网络模式为例，WSL 中的 Linux 有自己的内部 IP。它访问外网时，Windows 会替它转发数据，并转换地址：

  WSL 中的 Ubuntu（内部 IP）
          ↓
  Windows（转换地址、转发数据）
          ↓
  外部网络

  可以把它理解为公司的总机：

  - WSL 是内部员工，有自己的分机号。
  - Windows 是总机，帮内部员工接通外部电话。
  - 回应到达后，总机根据记录，把回应交回对应的分机。

### 标记IP地址

确定用于通过 WSL 运行的 Linux 分发的 IP 地址时，需要考虑两种情况：

#### Linux分发版IP

**方案一：** 从 Windows 主机的角度来看，你想要查询通过 WSL2 运行的 Linux 分发版 IP 地址，以便 Windows 主机上的程序可以连接到分发（实例）中运行的服务器程序。

Windows 主机可以使用命令： 

```powershell
wsl.exe --distribution Ubuntu hostname -I
```

在 PowerShell 中，通常可以省略可执行文件的 .exe 后缀，两种写法调用的是同一个程序

```powershell
# 查看主机名	
hostname
# 查看主机的IP地址(带参数的是Linux指令，Windows中用ipconfig)
hostname -I
```

* 小写 -i    查询主机名解析得到的 IP，可能是回环地址

* 大写 -I    列出网络接口上的 IP 地址，排除回环地址

回环地址就是“指向自己这台机器”的特殊 IP 地址。 访问它时，数据在本机内部处理，不会发到外部网络。

  最常见的是：

  - IPv4：127.0.0.1
  - IPv6：::1
  - localhost：通常解析到回环地址的主机名。

 IPv4 和 IPv6 是互联网协议（IP）的两个版本，都用来给网络中的设备分配地址，让数据能找到目的地。

联网设备越来越多，IPv4 的地址空间不够用，所以设计了地址空间大得多的 IPv6。可以把它理解为：原来的“门牌号码”数量有限，换一套能容纳更多号码的规则。

  IPv6 地址中的 :: 是省略连续全零分组的写法。例如：

  完整：0000:0000:0000:0000:0000:0000:0000:0001
  简写：::1

  一台电脑可以同时拥有 IPv4 和 IPv6 地址，两者并存很常见。另外，IPv6 中的“6”代表协议版本，不代表网速是 IPv4 的 1.5 倍。

#### Windows主机IP

**方案二：** 通过 WSL2（实例）在 Linux 分发中运行的程序想要知道 Windows 主机的 IP 地址，以便 Linux 程序可以连接到 Windows 主机服务器程序。

WSL2 Linux 用户可以使用命令：

```powershell
ip route show | grep -i default | awk '{ print $3}'
```

 这条命令的作用是：从 Linux 路由表中找出默认路由，提取它的网关 IP 地址。 常用于查看系统通过哪个网关访问其他网络。

  它分为三步，| 是管道符，把左边命令的输出交给右边命令处理：

① ip route show：显示路由表

  路由表告诉系统：“发往某个地址的数据，应该走哪条路。”

  示例输出：

  default via 172.28.112.1 dev eth0
  172.28.112.0/20 dev eth0 proto kernel scope link src 172.28.123.45

| default          | 默认路由：没有更具体的匹配路线时，就走这里 |
| ---------------- | ------------------------------------------ |
| via 172.28.112.1 | 通过网关 172.28.112.1 转发                 |
| dev eth0         | 使用名为 eth0 的网络接口                   |

② grep -i default：筛选包含 default 的行

 - grep：按文本模式筛选行。
  - -i：忽略大小写，例如 default、DEFAULT 都能匹配。
  - default：要查找的文本。

 处理后只剩下：

  default via 172.28.112.1 dev eth0

③ awk '{ print $3}'：输出每行的第三列

awk 是文本处理工具，默认按照连续空格、制表符等空白来划分字段：

| default | via  | 172.28.112.1 | dev  | eth0 |
| ------- | ---- | ------------ | ---- | ---- |
|   $1      |  $2    |    $3           |  $4     |   $5    |

 - 单引号 '...'：让 shell 把里面的内容原样交给 awk，避免提前解释 $3。
  - { ... }：awk 要执行的动作。
  - print：输出。
  - $3：当前行的第三个字段，不是固定代表 IP；这条命令依赖它恰好位于第三列。

#### awk

awk 是一个按行处理文本的小语言。

基本写法是

```powershell
awk '条件{ 动作 }'
```

 awk 会**逐行**读取输入：满足条件就执行动作。省略条件，表示每行都执行。

在 awk 中，$ 用来取当前行的字段（可以理解为第几列）。默认用空格或制表符分隔字段，连续空白通常算一个分隔。

$0 表示整行 

$1 表示第一个字段

$NF 表示最后一个字段

NF是当前行的字段数量,NR:到目前为止读到的总行数，BEGIN：读取任何输入前执行，END：输入处理结束后执行	

```powershell
awk '{print $1, $3}' data.txt
```

表示读取 data.txt，每行输出第一列和第三列。**逗号会让输出字段之间默认加一个空格**。

注意：$ 不是所有变量都要加的前缀。 普通变量直接写名字

| 写法                    | 用途                             | 示例                               |
| ----------------------- | -------------------------------- | ---------------------------------- |
| if ... else    条件判断 | 条件判断                         | if (\$2 >= 18) print $1            |
| printf                  | 按指定格式输出，不自动换行       | printf "%s：%d岁\n", \$1, \$2      |
| for                     | 循环                             | for (i = 1; i <= NF; i++) print $i |
| next                    | 跳过当前行的剩余处理，读取下一行 | if (NF == 0) next                  |
| exit                    | 停止读取输入，之后仍执行END动作  | if(NF > 10) exit                   |
| length                  | 获取字符串长度                   | length($1)                         |
| sub()                   | 替换第一个匹配内容               | sub(/old/, "new", $0)              |
| gsub()                  | 替换所有匹配内容                 | gsub(/old/, "new", $0)             |

如果字段用逗号分隔，可以用 -F 指定分隔符：

```powershell
awk -F ',' '{ print $1 }' data.txt
```

这适合简单的逗号分隔文本；带引号、字段内含逗号的复杂 CSV 不能只靠这种方式正确解析。

## 默认网络模式：NAT

默认情况下，WSL 使用基于 NAT（网络地址转换）的体系结构进行网络。 使用基于 NAT 的网络体系结构时，请记住以下注意事项：

### 从 Windows 访问 Linux 网络应用（localhost）

如果要在 Linux 分发版中构建网络应用（例如，在 NodeJS 或 SQL Server 上运行的应用），则可以使用 `localhost` Windows 应用（如 Edge 或 Chrome Internet 浏览器）访问它（就像平时一样）。

例如，你在 Ubuntu 里运行了一个网站程序，它显示：

  服务已启动，端口：3000

  那么打开 Windows 上的 Edge 或 Chrome，在地址栏输入：

```txt
http://localhost:3000
```

  就能访问这个网站。

  这里：

  - localhost：表示本机。
  - 3000：网站程序监听的端口号，需要与你启动的程序一致。

  访问过程可以理解为：

  Windows 浏览器访问 localhost:3000
               ↓
  WSL 的本机端口转发机制
               ↓
  Ubuntu 中监听 3000 端口的网站程序

  虽然 Windows 和 WSL 中的 Linux 是不同的运行环境，但 WSL 提供了这种转发能力，因此这种情况下通常不用先查询 Ubuntu 的 IP。

## 导出分发

```powershell
wsl --export <Distribution>
```

将指定分发的快照导出为新的分发文件。 默认为 tar 格式。 文件名可以是`-`，用于标准输入。 选项包括：

- `--vhd`：指定导出分发应为 .vhdx 文件而不是 tar 文件（仅使用 WSL 2 支持）

.vhdx文件是虚拟硬盘镜像文件（Virtual Hard Disk）

**一个单独的 `.vhdx` 文件就相当于一整块硬盘**，里面可以包含分区、文件系统、操作系统、所有数据

## 导入发行版

```powershell
wsl --import <Distribution Name> <InstallLocation> <Filename>
```

将指定的 tar 文件导入为新的分发版。 文件名可以是`-`，用于标准输入。 选项包括：

- `--vhd`：指定导入分发应为 .vhdx 文件而不是 tar 文件（仅使用 WSL 2 支持）
- `--version <1/2>`：指定是否将分发导入为 WSL 1 还是 WSL 2

把一个 Linux 系统文件导入 WSL，创建一个新的分发版实例。 常用于恢复备份、迁移系统，或者导入自定义 Linux 系统。

## 就地导入分发包

```powershell
wsl --import-in-place <Distribution Name> <Filename>
```

将指定的 .vhdx 文件导入为新的分发版。 虚拟硬盘必须在 ext4 文件系统类型中格式化。

## 注销或卸载分发版

若要注销和卸载 WSL 分发版，

```powershell
wsl --unregister <Distribution Name>
```

将 `<DistributionName>` 替换为您目标的 Linux 发行版名称，会使该发行版从 WSL 中注销，以便您可以重新安装或清理它。 **谨慎：** 注销后，与该分发关联的所有数据、设置和软件都将永久丢失。 从应用商店重新安装将安装分发版的干净副本。 例如， `wsl --unregister Ubuntu` 将从 WSL 中可用的分发中删除 Ubuntu。 运行 `wsl --list` 将显示它不再列出。

还可以像任何其他应用商店应用程序一样卸载 Windows 计算机上的 Linux 分发应用。 若要重新安装，请在 Microsoft 应用商店中找到分发版，然后选择“启动”。

## 装载磁盘或设备

 装载磁盘（也叫“挂载”，mount），就是把磁盘里的文件系统接到系统中的某个位置，让你能通过这个位置访问文件。 它不是格式化，也不是复制文件。

  在 Windows 中，你通常通过盘符访问磁盘：

  E:\照片\图片.jpg

  Linux 通常把文件系统挂到一个目录下，这个目录叫“挂载点”：

  /mnt/data/照片/图片.jpg

  这里 /mnt/data 就相当于进入这块磁盘的入口。挂载之后，访问这个目录，就是访问磁盘中的文件。

   wsl --mount 可以理解为两个步骤：

  1. 附加磁盘：把磁盘交给 WSL 2，让 Linux 能识别这个磁盘设备。
  2. 挂载文件系统：把磁盘或分区里的文件系统接到一个目录，让你能浏览其中的文件。

```powershell
wsl --mount <DiskPath>
```

> 注意：将磁盘附加到 WSL 2 需要管理员访问权限。 WSL 2 `wsl --mount` 命令不支持装载当前正在使用的磁盘（或属于该磁盘的分区）。 即使只请求一个分区，`wsl --mount` 也始终会附加整个磁盘。 无法装载 Windows 安装磁盘。
>
> `wsl --mount` 命令目前不支持 USB/闪存驱动器/SD 读卡器

为 Windows 设置格式的外部驱动器通常使用 NTFS 文件系统格式。 为 Linux 设置格式的外部驱动器通常使用 Ext4 文件系统格式。

“为 Windows 设置格式”其实就是“将硬盘格式化为适合 Windows 使用的文件系统”，并不是说硬盘硬件分成 Windows 专用和 Linux 专用。同一块硬盘可以采用不同的文件系统，甚至不同分区使用不同格式。

  结合前面的挂载概念：

  - 格式化：在分区上建立文件系统，通常会清除原有文件系统，使已有数据无法正常访问。
  - 挂载：把已有文件系统接入系统，让你能访问里面的文件。

  所以，一块已经存有文件的 ext4 硬盘，挂载后就能读取，不需要再格式化。

如果在 Windows 文件系统上装载了 NTFS 格式的驱动器，则可以使用 WSL 从 Linux 分发版访问该驱动器，方法是创建一个装载的目录（`sudo mkdir /mnt/d`替换为 `d` 要使用的驱动器号），然后使用 `drvfs` 文件系统互作插件和命令：

```powershell
sudo mount -t drvfs D: /mnt/d
```

在 WSL 默认开启自动挂载、且 Windows 有 C 盘和 D 盘的情况下，通常不需要手动挂载

特别注意：通过 Linux 的 rm 删除，通常不会进入 Windows 回收站。 所以操作这些目录时要格外留意。

如果你有 Ext4 格式的驱动器，则无法将其装载到 Windows 文件系统上。 若要使用 WSL 在 Linux 分发版中装载 Ext4 格式的驱动器，可以按照下面的说明使用 `wsl --mount` 命令。

## 分离磁盘

若要从 WSL 2 分离磁盘，请运行

```powershell
wsl --unmount [DiskPath]
```

如果省略 `Diskpath`，则将卸载和分离所有附加的磁盘。

# WSL开发

适用于 Linux 的 Windows 子系统随 Windows 操作系统一起提供，但必须先启用它并安装 Linux 发行版，然后才能开始使用它。

打开 PowerShell（或 Windows 命令提示符）并输入：

```powershell
wsl --install
```

该 `--install` 命令执行以下作：

- 启用可选的 WSL 和虚拟机平台组件
- 下载并安装最新 Linux 内核
- 将 WSL 2 设置为默认值
- 下载并安装 Ubuntu Linux 发行版（可能需要重新启动）

在此安装过程中，你将需要重启计算机。

# WSL中使用Codex CLI

## 进入Ubuntu终端

下载 Codex 安装脚本，然后交给 sh 执行。

```bash
curl -fsSL https://chatgpt.com/codex/install.sh | sh
```

 - curl：从网址下载内容。
  - -f：服务器返回错误时，让命令失败。
  - -s：隐藏下载进度。
  - -S：出错时仍显示错误信息。
  - -L：网址发生重定向时，跟着跳转。
  - |：管道，把左边下载的脚本内容传给右边。
  - sh：执行收到的脚本。

安装后重新打开 WSL 终端，检查：

```bash
codex --version
```

如果提示没有 curl，Ubuntu 中先执行：

```bash
codex login
```

  按提示在浏览器中完成 ChatGPT 登录，然后检查：

```bash
codex login status
```

 Windows 上的项目也可以访问，例如 D:\projects\my-app 对应：

```bash
cd /mnt/d/projects/my-app
codex
```

  不过，长期开发建议把项目放在 WSL 的 ~/code/ 下，文件读写通常更快，也能减少权限和符号链接问题。

```bash
mkdir -p ~/code && cd ~/code
git clone https://github.com/your/repo.git
cd repo
```

## bubblewrap

提示：

```bash
⚠ Codex could not find bubblewrap on PATH. Install bubblewrap with your OS package manager. See the sandbox prerequisites: https://developers.openai.com/codex/concepts/sandboxing#prerequisites. Codex will use the bundled bubblewrap in the meantime.
```

这是提示：**系统里没找到 `bubblewrap`，Codex 暂时使用自带的版本。** 它是用于 Linux 沙箱隔离的工具，**这条提示本身不代表 Codex 无法使用**。

如果你用的是 **Ubuntu / WSL Ubuntu**，在终端执行：

```bash
sudo apt update
sudo apt install bubblewrap
```

* apt 是 Ubuntu、Debian 等 Linux 系统的软件包管理工具，全称是 Advanced Package Tool，用来安装、更新和卸载软件。

- update：刷新软件列表，获取软件源中有哪些包、最新版本是什么。

安装后退出并重新启动 Codex 即可。也可以先检查是否安装成功：

```bash
bwrap --version
```

