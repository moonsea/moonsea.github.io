---
layout: post
title: 恶意代码分类数据篇
category: opinion
description: malcode classification dataset part
---

## [VX Heaven][]

`VX Heaven`在很多恶意代码分类的文章中都有提到，应该算是一个不错的数据集，一开始进入这个网站并没有找到想要的数据集。通过这两天的分析查找，发现了两个有意思的链接，一个是`source code of computer viruses`，一个是`computer virus collection`，通过名字就可以知道这两个链接的意思。链接如下:

- source: [http://vxheaven.org/src.php?show=all][1]
- collection: [http://vxheaven.org/vl.php][2]

### Virus Source

> [http://vxheaven.org/src.php?show=all][1]

该链接提供的`virus`总共有`375`个,基本都是`virus`的汇编文件，可以直接提取`opcode`.文件数量虽然不是很多，但是一个一个点击下载还是比较麻烦，而且链接点击进去之后是关于恶意程序的具体介绍，也无法使用迅雷下载页面全部链接，考虑使用爬虫获取页面所有链接，并进一步下载所有文件和恶意程序的具体描述信息。下面对关键步骤进行记录一下，完整代码可以参考Github上的[vxheaven.py][].

使用正则匹配，解析[virus source][1]文件列表

```
def GetMalcode(url):
    soup = GetPage(url)

    results = soup.findAll('a', {'href': re.compile(r'/src.php\?info=(.+)')})
    for result in results:
        tmp = result.get('href')
        src_url = ''.join(['http://vxheaven.org', tmp])
        GetMalInfo(src_url)
```

解析`virus`具体信息,包括`title`, `description`, `author`, `upload time`, `file name`.因为并不是所有页面都包含这些信息，所以要对作简单的判断，不然程序会抛出异常

```
tmp = result.find('h2')
    if tmp:
        malTitle = tmp.renderContents()

    malDesc = ''
    malAuthor = ''
    malDate = ''

    tmp = result.findAll('p')
    lenth = len(tmp)

    if (lenth > 0):
        malDesc = tmp[0].renderContents()

        if (lenth > 1):
            malAuthor = tmp[1].renderContents()

            if (lenth > 2):
                malDate = tmp[2].renderContents()
```

解析`virus`下载地址.页面中下载文件是通过`post`提交`form`来获取文件下载链接，通过模拟操作发现会有一步人机验证，导致响应`403 forbidden`的问题.通过分析下载链接，发现可以通过`base64`解码提交参数，拼凑出文件下载链接.

```
tmp = result.find('input', {'type': 'hidden'}, {
                      'name': 'file'}).get('value')
    tmp = tmp.replace('@', '=')
    malFile = base64.b64decode(tmp)
    malSrc = ''.join(['http://vxheaven.org/dl/', malFile])
```

### [Virus collection][2]

该链接提供了`virus`的可执行程序集合，页面列表根据`Kaspersky`的检测结果进行命名.点击进入链接一直到下载文件页面会得到提示
![vx warnning](../../images/malware/mal-3.jpg)

根据提示可以下载到两个`virus collection`的`subset`，其中

* Virus.Dos文件中包含`16862`个文件
* Virus.Win文件中包含`3251`个文件

虽然提示可以通过`torrent`下载全部`collection`，但是页面中并没有给出具体的下载拦截.通过查找`vx heaven`的`FAQ`页面以及`forum`，找到`http://vxheaven.org/forum/viewtopic.php?id=141`，可以通过该页面提供的地址下载`viruses-2010-05-18.tar.bz2.torrent`.总共含有`270K samples, 45Gb, 62Gb unpacked， virus list`

另外，推荐一个下载学术资源的网站[Academic Torrents][]，提供了很多数据，最初我下载的`virus collection`([VX Heaven Virus Collection 2010-05-18][3])也是在上面找到的.

备注： 在接下来的恶意代码检测，以及恶意代码反汇编提取操作码（OpCode）的实验中，皆使用`virus collection subset`数据集。

## [ZeuS Tracker][]

这也是一个提供了恶意代码资源的网站，除了有恶意代码资源，同时还有一些IP的blocklist，都可以用作参考。因为要做恶意代码分类的研究，所以重点关注恶意代码资源的下载.

该网站资源链接比较容易找到，如下

> [https://zeustracker.abuse.ch/monitor.php?browse=binaries][4]

该页面提供了`66`个恶意代码资源，包含`virus`的`url`, `hash`, `size` 和 `virus total`的检测结果等信息.

虽然数据量不大，但是手动下载也是挺麻烦，所以还是通过python脚本来实现下载。关键解析代码如下，完整资源参考Github上的[zeustracker.py][]

```
results = soup.findAll('a', {'title': 'download file'}, {'href': re.compile(r'monitor.php?show=(.+)')})
for result in results:
    tmp = result.get('href')

    index_show = tmp.index('show=')
    index_hash = tmp.index('&hash=')
    index_down = tmp.index('&download')
    file_suffix = tmp[index_show + 5: index_hash]
    file_name = tmp[index_hash + 6: index_down]
    file = '.'.join([file_name, file_suffix])

    src_url = ''.join(['https://zeustracker.abuse.ch/', tmp])

    GetFile(src_url, file)
```

## Virus Detection

在得到数据之后，首先需要对数据进行检测，确定数据是否是恶意样本。对恶意样本检测可以通过杀软进行检测，如Kaspersky，或者在线进行检测，通过检测结果可以确定数据是否是恶意样本，同时也能够得到恶意样本的类型，为接下来的研究做好基础准备。

以下是常用的恶意代码在线检测网站

* [Virus Total][]

提供Public Api可以对文件进行检测

* [Viruscan][]

* [Malwr][]

## Virus Disassembly

对数据检测之后，确定数据为恶意样本之后，接下来对恶意样本分析。因为要提取恶意样本的操作码(OpCode)，那么就需要恶意样本是二进制可执行文件。通过对数据集文件类型进行分析，发现数据集中不仅包含可执行文件（加壳，不加壳），同时也含有大量文本文件，如HTML、XML等，对于该部分文件，没有操作码（OpCode）的概念，所以在本研究中暂时不作考虑。也正是基于此原因，需要首先对数据的文件类型进行分析。

### 文件类型

在Windows系统中，通常根据文件后缀名来判断文件类型，暂不考虑在类Unix系统中没有后缀名这种概念，单纯在Windows系统中，根据文件后缀来判断文件类型也存在很大问题，是一种很不准确的方法。通常来说，识别文件类型一般是通过识别文件头信息，也叫做魔数（magic number），比如常见的`exe`文件头为`4D 5A 50`。在类Unix系统中，通常使用`file`命令来查看文件类型，而`file`命令的本质也是根据`magic number`进行判断。

本实验中，通过分析`file`命令的源码，定位出识别文件类型的核心代码[python-magic][],安装之后就可以引入`magic`模块对文件类型进行识别

```
def filetype(filename):

    ms = magic.open(magic.NONE)
    ms.load()

    file_type = ms.file(filename)

    ms.close()

    return file_type
```

### 反汇编工具

挑选出可执行二进制文件之后，接下来就是要对这些文件进行反汇编。在进行反汇编恶意样本之前，需要首先选定进行反汇编的工具，因为不同的反汇编工具采用不同的反汇编算法，不同的反汇编算法得到的结果也不尽相同。

常用的反汇编算法可以分为以下几种：

* 梯度下降法

梯度下降法钱强调控制流的概念，根据一条指令是否被另一条指令引用来决定是否对其进行反汇编

代表工具 `IDA Pro`

* 线性扫描反汇编

线性扫描反汇编算法采用一种非常简单的方法来确定需要反汇编的指令位置：指令开始和结束的位置。从代码段的第一个字节开始，以线性模式扫描整个代码段，逐条反汇编每条指令，直到完成整个代码段

代表工具 `Windbg`、`Ndisasm`

通过对几个恶意样本的以及上一步对恶意样本的分类结果进行分析，可以发现数据集中恶意样本种类繁多，而且`16`位、`32`位恶意程序都有，如果使用`ndisasm`进行反汇编，需要人工分析不同种类样本的入口地址，以确定代码段起始位置，否则反汇编得到的结果不准确，而且`ndisasm`工具不提供自动分析功能，想比较而言，`IDA Pro`虽然比较繁琐，分析比较慢，但是结果比较准确，而且可以自动选择合适的处理器型号、文件类型等参数，对恶意样本进行自动分析，并将结果保存到`idb`数据库，可以使用`IDA Pro`直接打开继续上一次分析。

选定反汇编工具`IDA Pro`之后，接下来对恶意样本进行反汇编处理。

### 批量反汇编

虽然`IDA Pro`是一款非常好用的静态反汇编工具，但是并不能一次性打开多个文件进行自动分析，所以考虑使用脚本调用`IDA Pro`的接口进行分析，并将分析结果，及反汇编代码进行保存。`IDAPython`是`IDA Pro`的一种扩展脚本，可以实现很多`IDA Pro`本身不具有的功能。在这种思路下， 很自然想到使用`IDAPython`编写脚本来对恶意样本逐一进行分析，并保存结果。

原本是按照编写`IDAPython`脚本的思路对恶意样本进行分析，偶然发现看雪论坛上一篇文章[IDA批量模式][]，发现`IDA Pro`还可以命令行模式运行(`idal -A -Sscript.idc input_file`)，而且功能和GUI界面相同，也能够根据参数对文件分析，此外还可以加载后期处理脚本，这无疑是一种处理恶意样本更好的方法。

> 这里要说明一下，查阅相关资料都是`idag`命令，但是我使用的版本中没有`idag`，只有`idal`，这应该是不同版本的问题，使用过程中和相关资料中`idag`功能相同，所以没有深究原因，感兴趣的可以分析一下  
> `Windows`平台使用`idaw`启动，`Mac`系统使用`idal`命令启动

下面对分析过程中使用到的参数进行简单说明：

* `-A` 让ida自动运行，不需要人工干预。也就是在处理的过程中不会弹出交互窗口，但是如果从来没有使用过ida那么许可协议的窗口无论你是否使用这个参数都将会显示。
* `-c` 参数会删除所有与参数中指定的文件相关的数据库，并且生成一个新的数据库。
* `-S` 参数用于指定ida在分析完数据之后执行的idc脚本，该选项和参数之间没有空格，并且搜索目录为ida目录下的idc文件夹。
* `-B` 参数指定批量模式，等效于-A –c  –Sanylysis.idc.在分析完成后会自动生成相关的数据库和asm代码。并且在最后关闭ida，以保存新的数据库。
* `-h` 显示ida的帮助文档

默认情况下，`IDA`分析完文件之后，会自动在文件目录下生成一个`idb`数据库文件，数据库文件以输入进行分析文件去掉后缀之后的文件名进行命名。但是因为本次实验中使用的数据集很多文件只是后缀有区别，前面文件名相同，所以要以完整文件名保存相应的分析结果。这种情况下，自定义`idc`脚本来完成分析后期工作比较方便。

下面是使用到的`analysis_fullname.idc`脚本，保存在ida目录下的idc文件夹：

```
#include <idc.idc>

static main()
{
  // turn on coagulation of data in the final pass of analysis
  SetShortPrm(INF_AF2, GetShortPrm(INF_AF2) | AF2_DODATA);

  Message("Waiting for the end of the auto analysis...\n");
  Wait();
  Message("\n\n------ Creating the output file.... --------\n");
  auto file = GetInputFilePath();
  auto asmfile = file + ".asm";
  auto idbfile = file + ".idb";
  WriteTxt(asmfile, 0, BADADDR);           // create the assembler file
  SaveBase(idbfile, 0);                   // save the idb database
  Message("All done, exiting...\n");
  Exit(0);                              // exit to OS, error code 0 - success
}
```

接下来就可以通过python脚本遍历数据集的恶意样本，调用`idal`来对样本进行分析，并保存反汇编结果了。完整代码可以参考[wingenasm.py][],核心代码如下：

```
idalPath = "//usr//local//src//ida-pro-6.4//idal"
idcPath = "//usr//local//src//ida-pro-6.4//idc//analysis_fullname.idc"


def traveseFile(path):
    for parent, dirnames, filenames in os.walk(path):

        for filename in filenames:
            filepath = os.path.join(parent, filename)

            filepath = filepath.replace(' ', '\ ').replace('(', '\(').replace(')', '\)')

            genAsm(filepath)


def genAsm(filepath):
    ExecStr = idalPath + " -c -A -S" + idcPath + " " + filepath
    os.system(ExecStr)
```

## References

\[1\] [IDA批量模式][]

[1]: http://vxheaven.org/src.php?show=all
[2]: http://vxheaven.org/vl.php
[vxheaven.py]: https://github.com/moonsea/malcode/blob/master/vxheaven.py
[vx heaven]: http://vxheaven.org/
[Academic Torrents]: http://academictorrents.com/
[3]: http://academictorrents.com/details/34ebe49a48aa532deb9c0dd08a08a017aa04d810/collections
[ZeuS Tracker]: https://zeustracker.abuse.ch/
[4]: https://zeustracker.abuse.ch/monitor.php?browse=binaries
[zeustracker.py]: https://github.com/moonsea/malcode/blob/master/zeustracker.py
[Virus Total]: https://www.virustotal.com/en/
[Viruscan]: http://www.virscan.org/
[Malwr]: https://malwr.com/
[python-magic]: https://github.com/moonsea/python-magic
[IDA批量模式]: http://bbs.pediy.com/showthread.php?t=147777
[wingenasm.py]: https://github.com/moonsea/malcode/blob/master/wingenasm.py
