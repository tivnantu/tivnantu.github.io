---
title: 【笔记】cURL必知必会
description: curl命令行用法大览
index_img: /image/cURL必知必会/cURL必知必会.jpg
banner_img: /image/cURL必知必会/curl.png
tags:
  - 工具
categories: 笔记
abbrlink: 9f5d61d7
date: 2021-12-29 11:24:01
updated: 2021-12-29 11:24:01
---

{% note success %}

秉持着在实践中学习的想法，最近为了研究学习计算机网络哪一堆东西，前前后后学了不少UNIX/Linux环境下一些网络工具（如tcpdump、curl、tshark、nmap等）的用法。

这份笔记摘录于丹尼尔·斯坦伯格的《cURL必知必会》。这本书前前后后详细地介绍了cURL项目的内容（curl命令行工具、libcurl库），我对其中的curl命令行工具的用法较为感兴趣，特此总结出来笔记。

注意，《cURL必知必会》为“手册”类型书籍，书中的描述已经极为精简干练毫不拖泥带水，基本上不需要我再进行修补裁剪。本笔记最大的目的是方便我自己以后检索查找。因此对于描写的已经很ok的原文，我就不班门弄斧进行修改，直接copy过来了，所以你常常可以看出来大段大段的原文句子。

{% endnote %}

## 第一章 curl可以做什么

- curl关心所有与网络协议传输相关的东西，那些与此无关的就留给其他项目和产品去操心吧。

- curl和libcurl试图避免直接处理被传输的数据。例如，它们对HTML或通过HTTP传输的内容一无所知，只知道如何通过HTTP传输这些数据。

- curl的一个常见使用场景是像浏览器那样获取URL，但浏览器做的事情比curl要多得多，curl在终端输出的内容可能与你在浏览器窗口中看到的内容完全不一样。

- curl只会获取你要求它获取的内容，但永远不会解析获取到的内容，也就是服务器发送的数据。

- 浏览器在获取数据后会根据内容类型激活不同的解析器。例如，如果数据是HTML，那么它会进行解析并显示网页，还可能会下载其他子资源，如图像、JavaScript和CSS文件。curl在下载HTML时只会获取单个HTML资源，而在浏览器中，则可能会触发更多下载。如果你希望curl也下载子资源，那么需要将这些URL传给curl并要求它获取这些URL，就像其他URL一样。

## 第二章 命令行基础

- curl是一个可执行的二进制文件，但cURL项目本身不提供二进制文件。curl的二进制文件需要根据不同的操作系统进行构建，而且通常受不同版本系统的约束。

- curl很少会自己决定做什么，它试图在最大程度上让你做自己想做的。你给它什么，它就会处理什么。如果你提供一个拼写错误的选项，那么它可能会做出预想不到的事情。如果你给它一个不合法的URL，那么它仍然会继续处理它。

- 在curl的使用中，curl只处理选项和URL，也就是说，如果不是选项，就一定是URL

### 2.1 命令行选项

- curl支持200多个不同的选项。

- 命令行选项可以将你想要用curl执行的任务的信息传给curl。

#### 2.1.1 短选项

- 短选项是只有一个字母的选项，一般也会提供对应的长选项

- `-v`：让curl切换到详细（verbose）模式
  
  ```bash
  curl -v http://example.com
  ```

- `-L`：使得HTTP重定向
  
  ```bash
  curl -L http://example.com
  ```

- 很多选项属于切换开关，用于启用某些功能或切换两个已知状态.

- 指定对应的选项名称即可使用这些选项。你也可以在减号后面组合使用多个单字母选项。当然，你也可以单独指定短选项。
  
  ```bash
  curl -vL http://example.com
  curl -v -L http://example.com
  ```

- curl的命令行解析器会解析整行命令，你可以将选项放在任意位置，它们甚至可以出现在URL之后。
  
  ```bash
  curl -v http://example.com
  curl http://example.com -v
  ```

#### 2.1.2 长选项

- 短选项使用起来非常方便，因为它们的名字很简短。但因为字母的数量有限，需要用到的选项又比较多，所以并非所有选项都可以使用单个字母来表示，于是就有了长选项。

- 另外，为了方便，也为了让脚本更易于阅读，大多数短选项都有对应的长选项别名。

- 长选项使用两个减号（或者称为破折号），后面跟上选项名，而且每两个减号后面只能跟一个选项名

- `--verbose`：打开详细模式
  
  ```bash
  curl --verbose http://example.com
  ```

- `--location`：使得HTTP重定向
  
  ```bash
  curl --location http://example.com
  ```

#### 2.1.3 选项的参数

- 并非所有选项都只用于启用或禁用某项功能。对于一些选项，你需要向它们传递一些数据，如用户名或文件路径。你需要先指定选项，然后给出参数，中间用空格分隔。

- `-d`或`--data`：可以通过HTTP POST将一个字符串发送给服务器
  
  ```bash
  curl -d hello http://example.com
  curl --data hello http://example.com
  ```

- 如果使用带参数的短选项，可以不使用空格进行分隔
  
  ```bash
  curl -dhello http://example.com
  ```

#### 2.1.4 带空格的参数

- 有时你会想要向选项传递参数，而且参数中包含了一个或多个空格。此时你需要给字符串加上引号。使用的引号因不同的shell或命令提示符而异，但大多数情况下可以使用双引号。

- `-A`：设置HTTP请求的user-agent字段
  
  ```bash
  curl -A "Hello World" http://example.com
  ```

- 如果字符串本身包含了双引号，比如想要向服务器发送JSON字符串（这种情况很常见），你可能需要使用单引号（但在Windows系统上使用单引号可能不行）。
  
  ```bash
  curl -d '{"name": "Darth"}' http://example.com
  ```

- 如果想要避免使用单引号，则可以通过文件将数据传给curl，这样就无须使用额外的引用。

#### 2.1.5 负选项

- 对于开关选项，既可以用它们打开某些功能，也可以通过它们关闭功能。你也可以使用长选项，在选项名前面加上“no-”前缀即可。
  
  ```bash
  # 关闭详细模式
  curl --no-verbose http://example.com
  ```

### 2.2 URL

- URL就是curl的操作对象。

- URL：Uniform Resource Locator，统一资源定位符

- 严格来说，URL是之前使用的名称，URI（Uniform Resource Identifier，统一资源标识符）才是更现代、更正确的叫法。RFC 3986给出了它们的语法定义。

- curl接受“URL”作为输入，但实际上是“URI”。curl支持的大多数协议也有相应的URI语法文档，这些文档描述了这些URI格式的工作原理。

- curl假定你会传给它一个有效的URL，它只对格式进行有限的检查，以提取执行操作所需要的信息。你可能会将包含非法字符的URL传给curl，但curl并不会注意到，也不关心这些，它只会继续执行自己的操作。

- 现代Web浏览器的“地址栏”中一般使用的不是URL或URI。实际上，它们主要使用IRI，也就是URI的超集，以支持国际化（如支持非拉丁符号）。它们还会处理空格、编码地址等，但规范中并没有说明这些事情应该由客户端完成。有时你在浏览器地址栏中看到的内容与传给curl的内容存在很大差别。

#### 2.2.1 scheme

- URL以“scheme”作为开头，scheme是“http://”这部分内容的官方名称，用于告诉curl传入的URL使用了哪个协议。

- scheme必须受当前curl版本支持，否则curl将显示错误消息并退出。此外，scheme既不能以空格开头，也不能包含空格。

- “://”（一个冒号和两个斜杠）将scheme标识符与URL的其余部分分开。有些URL只包含一个斜杠，但curl不支持这种格式

- curl允许一些非法语法，并尝试在内部纠正它们，因此它也可以理解并接受一些带有一个或三个斜杠的URL，即使它们的格式不正确。curl这么做的原因是，浏览器已经开始支持这些URL，进而导致这种URL大量存在。
  
  ```bash
  # file://类型的URL写作file://<hostname>/<path>
  # 但主机名部分只能是localhost、127.0.0.1或空白（什么都没有）
  # 如果在主机名部分使用其他主机名，curl将返回错误
  file://localhost/path/to/file
  file:///path/to/file
  ```

- 为方便起见，curl还允许用户省略URL的scheme部分
  
  - curl会根据主机名的第一部分猜测要使用哪种协议。这是一种非常基本的猜测，因为它只检查主机名的第一部分是否与一组协议中的某个协议匹配，并假定你打算使用的就是这个协议。
  
  - 这主要基于传统的服务器命名方式。可以通过这种方式检测的协议包括FTP、DICT、LDAP、IMAP、SMTP和POP3。没有提供scheme的其他URL将默认使用HTTP。
  
  - 可以通过`--proto-default`选项将默认协议修改为HTTP以外的其他协议。

- `--proto-default`：将默认协议修改为HTTP以外的其他协议。

#### 2.2.2 用户名和密码

- scheme后面可以跟用户名和密码。现在通常不建议使用这种语法，因为这样很容易在脚本或其他地方泄露这些信息。
  
  ```bash
  # 使用给定的用户名和密码列出FTP服务器目录中的内容
  curl ftp://user:password@example.com/
  ```

- 在URL中显示用户名和密码只是可选项，curl还允许在URL之外，即通过正常的命令行选项来提供这些信息。

#### 2.2.3 主机名或地址

- URL的主机名部分只是一个可以解析为数字IP地址的名字，或者是数字IP地址本身。

- 在指定数字IP地址时，可以使用IPv4地址。如果使用的是IPv6地址，则需要将其放在方括号中。
  
  ```bash
  curl http://127.0.0.1/
  curl http://[::1]/
  ```

- 如果使用的是主机名，系统解析器会将主机名转换为IP地址。这通常需要在/etc/hosts文件（或等效文件）中进行本地域名查找。

#### 2.3.4 端口号

- 每个协议都有一个“默认端口”，除非特别指定了端口号。在URL中指定端口号时，先在主机名后面添加一个冒号，然后是十进制的端口号。

#### 2.3.5 路径

- 每个URL都包含一个路径。如果没有指定，则默认使用“/”。路径将被发送给指定的服务器，用于识别要请求的资源。路径的用法取决于具体的协议。

- 对于具有目录概念的协议，可以在URL尾部以一个斜杠表示它是目录，而不是文件。

#### 2.3.6 FTP类型

- 用于标识FTP服务器文件的URL提供了一个特性来告诉客户端（这里是curl）资源的文件类型。这是因为FTP可以改变传输模式，在不同的模式下使用不同的处理方式。注意该特性没有被广泛使用

- 通过在URL中附加“; type=A”，你可以告诉curl当前的FTP资源是ASCII类型。

- curl默认为FTP使用二进制传输模式，但是你也可以在URL中通过type=I来指定二进制类型。

- 如果你传给curl的类型是D，那么就是表明请求的资源是一个目录。这可以作为目录的替代格式，不需要像之前那样在路径尾部添加斜杠。

- 示例：
  
  ```bash
  curl "ftp://example.com/hello; type=A"
  curl "ftp://example.com/hello; type=I"
  curl "ftp://example.com/hello; type=D"
  ```

#### 2.3.7 片段

- URL中还可以包含“片段”，这通常由井号（#）和网页中的特定名字组成。curl可以支持带有片段的URL，但实际上片段并不会被发送出去，因此，无论是否存在，它对curl的操作并没有任何影响。

#### 2.3.8 多个选项和多个URL

- curl支持数百个命令行选项和无限数量的URL。如果你的shell或命令行系统能够支持，那么传给curl的命令行长度实际上是没有限制的。

- curl首先会解析整个命令行，应用给定的命令行选项，然后（按从左到右的顺序）遍历URL并执行相应操作。

- 对于某些选项（如告诉curl将输出内容保存在哪里的-o或-O），你可能希望为每个URL单独指定。

- curl会在处理完最后一个URL后返回一个退出码。想让curl在第一次出现错误时就退出，则可以使用--fail-early选项。

- `--fail-early`：在第一次出现错误时就退出

#### 2.3.9 URL的单独选项

- `-;`或`--next`：用于在一组选项和URL之间插入间隔

- 当命令行解析器遇到--next选项时，它会将后面的选项应用于下一组URL。因此，--next选项其实是一组选项和URL之间的分隔符。使用多少个--next选项取决于实际的需要。
  
  ```bash
  # 向一个URL发起HTTP GET请求
  # 同时向另一个URL发起HTTP POST请求
  # 然后向第三个URL发起HEAD请求
  curl --location http://example.com --next \
       --data hello http://exampl.com --next \
       --head http://example.com
  ```

#### 2.3.10 连接重用

- curl在内部维护着一个连接池，这可以让之前使用过的连接继续存活一段时间，因此后续发给相同主机的请求可以重用这些已经建立的连接。

- 连接池中的连接可以在curl运行期间保持活跃状态，但最好还是在同一个命令行中完成多次传输，而不是单独运行多个curl命令行。

### 2.4 URL通配

- curl提供了“通配”（globbing）的方式来指定一类大致相同小部分不同的URL。不同的部分可能是一组数字或一组名字。

- curl使用保留符号[]和{}进行通配，它们一般不是合法URL的组成部分（IPv6地址除外，但curl可以很好地处理它们）

- `-g`或`--globoff`：禁用通配

- 可以使用[N-M]语法来指定一个数值范围，其中N是起始索引，M是结束索引（包括M在内）。表示数值范围的同时，还可以指定步进（step counter）
  
  ```bash
  curl http://example.com/[1-100].png
  curl http://example.com/[001-100].png
  curl http://example.com/[000-100:2].png
  ```

- curl也可以用以上语法来表示字母范围
  
  ```bash
  curl http://example.com/[a-z].html
  ```

- 有时URL的不同部分不会遵循这些简单的模式，那么你可以指定完整的列表，但要放在花括号，而不是中括号中。
  
  ```bash
  curl http://example.com/{one, two, three}.html
  ```

- curl可以在同一个URL中使用多个通配。
  
  ```bash
  curl http://example.com/{one, two, three}[1-100].jpg    
  ```

- `-O`或`--remote-name`：使用URL中的文件名来保存目标文件

- `-o`或者`--output`：指定下载文件的文件名

- 在下载多个文件时，可以通过curl的输出文件名变量，实现将它们保存到不同的子目录中，或者以不同的名称保存文件

- URL中的每个通配都对应一个单独的变量，可以通过 ’#[num]’ 来引用，即在’#’ 后面跟上与通配对应的数字，从1（对应第一个通配）开始，以最后一个通配结束。
  
  ```bash
  curl http://example.com/{one, two}.html -o "file_#1.html"
  curl http://{site, host}.host[1-5].example.com -o "subdir/#1_#2"
  ```

- `-h`或`--help`：列出所有选项，并提供简要的说明

- `--manual`：输出curl的整个手册页以及常见的用例教程

### 2.5 配置文件

- curl提供了“配置文件”功能。它允许你将命令行选项写在文本文件中，然后告诉curl，除了读取命令行外，还要从这个文件中读取命令行选项。

- `-K`或`--config`：告诉curl从特定文件中读取更多的命令行选项
  
  ```bash
  curl -K cmdline.txt http://example.com
  ```
  
  cmdline.txt示例：
  
  ```textile
  # 注释
  --location
  --head
  ```

- 配置文件可以接受短选项和长选项，就像你在命令行上写的那样。为了便于阅读，它还允许你使用不带破折号的长选项。
  
  ```textile
  -v
  location
  head
  ```

- 配置文件可以接受短选项和长选项，就像你在命令行上写的那样。为了便于阅读，它还允许你使用不带破折号的长选项。
  
  ```bash
  user-agent "Hello World"
  ```

- 为了让配置文件看起来更像真正的配置文件，它还允许你在选项及其参数之间使用’=’或’:'。
  
  ```textile
  user-agent = "Hello World"
  ```

- 选项的参数也可以不使用引号，curl将下一个空格或换行视为当前参数的结尾。不过，如果参数中带有空格，则必须使用双引号。
  
  ```textile
  user-agent = Hello-World
  ```

- 如果想在配置文件中指定URL，则必须使用--url或url，而且不会像在命令行中那样不是选项的所有东西都被视为URL。
  
  ```textile
  url = "http://example.com"
  --url = "http://example.com"
  ```

- 当被调用时，curl会检查是否存在默认配置文件（除非使用了-q），如果存在，则使用这个配置文件。在类Unix系统上，它会查找.curlrc文件，在Windows系统上则查找_curlrc文件。查找顺序：
  
  - 尝试找到“主目录”：它先检查CURL_HOME，然后是HOME环境变量。如果没有找到，它会在类Unix系统上调用getpwuid()（这个函数将返回当前用户的主目录）。在Windows系统上，它会检查APPDATA变量，如果没找到就尝试“%USERPROFILE%\Application Data”。
  
  - 在Windows系统上，如果主目录中没有_curlrc文件，那么它会查找curl可执行文件所在的目录。在类Unix系统上，它只会尝试从主目录中加载．curlrc。

- `-q`：禁用.curlrc

### 2.6  密码和窥探

- `-u`和`--user`：接受一个参数，即用冒号分隔的用户名和密码
  
  ```bash
  curl -u admin:admin http://example.com
  ```

- 首先，我们在命令行输入了密码，而命令行可能对同一系统上的其他用户是可见的（假设你使用的是多用户系统），这会导致信息的泄露。curl会尝试从进程列表中清空密码来降低密码泄露的风险。

- 避免在命令行上指定用户名和密码的一种方法是使用.netrc文件或配置文件。你也可以使用-u选项，但不指定密码，curl会在运行时提示用户输入密码。

### 2.7 进度指示器

- curl有一个内置的进度指示器。当调用curl来传输数据（上传或下载）时，它可以在终端上显示传输的进度，比如当前的传输速率、已经用掉的时间以及还需要多长时间才能完成传输。

- 如果curl需要在终端上输出内容，那么进度指示器就会被禁用，否则进度指示器会干扰输出内容，把要显示的内容弄得一团糟。

- 如果调用curl时没有看到进度指示器，则需要确保输出已经被重定向到终端以外的位置。也就是说，当输出被定向到其他位置的时候，就可以看到对应的进度显示器

- `-s`或`--silent`：禁用进度指示器和错误信息

- curl还提供了一个更简单的进度指示器，可以通过-#或--progress-bar来启用。正如其名字所暗示的那样，它使用进度条的方式来显示传输进度。

- `-#`或`--progress-bar`：启用简单的进度显示器，使用进度条的方式来显示传输进度。

- 有时候，在使用curl传输数据时，它无法确定请求对象的大小，因此进度指示器只包含很少量的细节，并且无法预测传输时间等其他信息。

- 进度指示器显示的是字节数和每秒字节数。对于很大的数据，它还会使用单位后缀，以1024为基础，因此1024是千字节（1K）,2048是2K，并以此类推。显示时间使用的是H:MM:SS格式，分别对应小时、分钟和秒。

## 第三章 使用curl

- curl支持或可以支持（需要进行构建）以下这些协议：DICT、FILE、FTP、FTPS、GOPHER、HTTP、HTTPS、IMAP、IMAPS、LDAP、LDAPS、POP3、POP3S、RTMP、RTSP、SCP、SFTP、SMB、SMTP、SMTPS、TELNET和TFTP。

### 3.1 详细模式

- 如果启用了详细（verbose）模式，curl会显示更多信息，告诉你它正在做什么。它会用前缀'＊’来打印信息。

- 详细信息中的'(#0)’是curl为这个连接分配的内部数字。如果在同一命令行中指定多个URL，那么你就会看到它将使用多个连接或重用已有的连接，因此连接的计数器可能会增加，也可能不会增加，具体取决于curl需要执行的操作。

#### 3.1.1 --trace、--trace-ascii和--trace-time

- 当curl使用HTTPS、FTPS或SFTP协议进行加密文件传输时，其他网络监视工具（如Wireshark或tcpdump）将无法帮你保存完整的消息。为此，curl在-v之外又提供了两个选项。

- `--trace`：--trace [filename]选项可以将完整的跟踪信息保存在指定的文件中，也可以使用’-’（单个减号）代替文件名，将内容打印到stdout。
  
  ```bash
  # 命令执行完后，会生成一个叫作dump的文件，发送和接收的每个字节都以十六进制的数字显示出来。
  curl --trace dump http://example.com
  curl --trace - http://example.com
  ```

- `--trace-ascii`：类似--trace，但是输出的详细内容不是十六进制的数字，而是ascii内容

- `--trace-time`：如果使用了这个选项，则所有的输出信息前面都会被加上高精度的时间戳。它可以与常规的-v和--verbose选项以及--trace和--trace-ascii选项一起使用。格式为小时：分钟：秒，然后是微秒。

#### 3.1.2 --write-out

- `-w`或`--write-out`：在传输任务完成后打印一些信息，并且它还提供了大量可添加到输出内容中的变量，这些变量包含了与传输相关的信息。

- 如果将一个字符串传给这个选项，那么这个字符串就会被打印出来
  
  ```bash
  curl -w "Hello World" http://example.com
  ```

- 如果在字符串前面加上‘@’，那么curl就会从指定文件中读取字符串。如果使用’-’作为文件名，那么curl就会从标准输入（stdin）读取字符串
  
  ```bash
  curl -w @filename http://example.com
  curl -w @- http://example.com
  ```

- 可以使用’\n’输出新行，使用’\r’输出回车，使用’\t’输出制表符。

- 可以在字符串中加入%{variable_name}来访问可用的变量，然后这些变量会被替换成对应的值。如果要输出正常的’%‘，则需要写成’%%’。在Windows命令行中，%是一个特殊符号，在使用这个选项时，所有出现的%必须是成双的。
  
  - %{content_type}显示所请求文档的Content-Type（如果有的话）。
    
    - %{filename_effective}显示curl最终写入内容的文件名。只有使用--remote-name或--output选项写入文件时，这个变量才有意义。它在与--remote-header-name选项结合使用时最有用。
  
  - %{ftp_entry_path}显示连接到远程FTP服务器时的初始路径。
  
  - %{response_code}显示最后一次传输返回的响应码。
  
  - %{http_connect}显示最后一次针对CONNECT请求的响应码（来自代理）。
  
  - %{local_ip}显示最近一次连接的本地IP地址——可以是IPv4或IPv6地址。
  
  - %{local_port}显示最近一次连接的本地端口号。
  
  - %{num_connects}显示最近一次传输建立的新连接的数量。
  
  - %{num_redirects}显示请求的重定向次数。
  
  - %{redirect_url}显示不使用-L选项时的HTTP请求的重定向URL。
  
  - %{remote_ip}显示最近一次连接的远程IP地址——可以是IPv4或IPv6地址
  
  - %{remote_port}显示最近一次连接的远程端口号。
  
  - %{size_download}显示已下载的总字节数。
  
  - %{size_header}显示已下载标头的总字节数。
  
  - %{size_request}显示HTTP请求发送的总字节数。
  
  - %{size_upload}显示已上传的总字节数。
  
  - %{speed_download}显示整个下载的平均速率，以字节/秒为单位。
  
  - %{speed_upload}显示整个上传的平均速率，以字节/秒为单位。
  
  - %{ssl_verify_result}显示请求的SSL对等证书验证结果，0表示验证成功。
    
    - %{time_appconnect}显示从开始到与远程主机建立SSL（或SSH等）连接（或握手）所花费的时间，以秒为单位。
    
    - %{time_connect}显示从开始到与远程主机（或代理）建立TCP连接所花费的时间，以秒为单位。
  
  - %{time_namelookup}显示从开始到完成域名解析所花费的时间，以秒为单位。
    
    - %{time_pretransfer}显示从开始到文件传输即将开始所花费的时间，包括用于所有预传输命令以及与特定协议协的时间，以秒为单位。
    
    - %{time_redirect}显示所有重定向所花费的时间，包括在最终事务启动之前的域名查找、建立连接、预传输和传输以秒为单位。
    
    - %{time_starttransfer}显示从开始到第一个字节即将开始传输所花费的时间，包括time_pretransfer以及服器计算结果所需的时间，以秒为单位。
  
  - %{time_total}显示整个操作持续的总时间，以秒为单位。时间将精确到毫秒。
    
    - %{url_effective}显示最后获取的URL。如果你要curl跟随重定向（使用-L选项），那么这个变量就非常有用。

#### 3.1.3 静默模式

- `-s`或`--silent`：可以打开静默模式，让curl关闭进度指示器，而且不会在发生错误时输出任何错误信息。与详细模式相反的是静默模式。静默模式仍然会输出你请求下载的数据。

- `-S`或`--show-error`：启用静默模式后，可以在发生错误时输出错误信息。

### 3.2 持久连接

- 在建立TCP连接时，curl将保留旧连接一段时间，如果下一次要连接到同一主机，那么就可以重用相同的连接，从而节省大量时间。我们称之为持久连接。curl将始终尝试保留连接，并尽可能重用现有连接。

- 不过，curl命令行工具只能在运行期间保持连接处于活跃状态，因此，只要它退出，就会关闭所有已打开的连接（并且还会释放所有缓存）。我们将这种活跃连接池称为“连接缓存”。

- 如果需要针对同一主机或相同的URL执行N次传输或操作，那么你可以考虑使用尽可能少的curl命令行来提速，而不是每次使用一个URL并重复调用curl。

### 3.3 下载

- 你可以通过URL来指定curl需要下载哪些资源。除非另有说明，否则curl默认下载URL指定的资源。URL被分解为多个部分，curl连接到正确的服务器，然后要求服务器提供特定的资源——通常是一个文件。然后，服务器开始传输数据，或者拒绝传输（客户端请求了错误的数据）。

- 对资源的请求与特定的协议相关，因此FTP:// URL与HTTP:// URL或SFTP:// URL的工作方式不同。

- 对于不包含路径的URL，即只包含主机名（比如前面的“http://example.com”）, curl将在内部为其添加一个斜杠（“/”），然后它就变成了curl要从服务器请求的资源。

- 如果你在命令行中指定了多个URL，那么curl将逐个下载所有URL。在一个传输完成之前，它不会启动下一个（以及后面的）传输。

- curl会避免将二进制数据输出到终端，因为这会严重扰乱终端（有时甚至会导致终端停止运行）。可以使用-o强制让它输出到stdout。

#### 3.3.1 下载文件的文件名

- -O选项从你提供的URL中提取文件名部分作为本地文件名。指定URL后，curl从中选择文件名。即使发生重定向（而且你告诉curl要跟踪重定向）, curl选择的文件名也不会发生变化。

- `-J`或`--remote-header-name`：HTTP服务器可以选择在响应消息中提供Content-Disposition标头。这个标头可能包含服务器建议的文件名，通过该选项可以让curl使用这个名字作为本地文件名。

- 同时使用-O和-J选项，那么curl默认使用URL中的文件名，只有当响应消息中包含有效的Content-Disposition标头时，它才会使用建议的文件名。

- -J选项存在一些用户需要注意的问题和风险：
  
  - 它只会使用建议文件名的最右边部分，因此服务器建议的其他路径或目录都会被忽略。
  
  - 因为文件名完全由服务器提供，所以，如果服务器恰好提供了与本地文件相同的文件名，那么curl将覆盖当前目录中已有的本地文件。
  
  - 文件名编码和字符集问题。curl不会对文件名进行编码，因此你可能会得到一个URL编码的文件名（如果是在浏览器中，那么浏览器会使用合理的字符集来解码URL，将它还原成更易阅读的内容）。

#### 3.3.2 压缩

- curl可以要求HTTP和HTTPS服务器提供压缩过的数据，并在收到数据后自动对其进行解压。

- HTTP压缩可以通过两种机制来实现，一种被认为是“正确的方式”，另一种在实际中使用得更为广泛：
  
  - 压缩HTTP内容的常用方法是使用Content-Encoding标头。
  
  - Transfer-Encoding是一种比较罕见的方法，它是为自动压缩和解压而创建的一个标头，但并没有被广泛采用

- `--compressed`：如果服务器支持压缩，它会传输压缩过的数据，curl会在保存或发送到stdout前对数据进行解压。除了可能会注意到传输变得更快之外，用户并不会真正看到或感觉到压缩过程。--compressed请求服务器使用一种受支持的压缩算法来压缩数据
  
  ```bash
  curl --compressed http://example.com
  ```

- `--tr-encoding`：让curl向服务器请求Transfer-Encoding压缩
  
  ```bash
  curl --tr-cncoding http://example.com
  ```

- 从理论上讲，你可以在同一个命令行中使用两种压缩方式。但在实践中，当被要求以两种方式进行压缩时，一些服务器可能会感到困惑。因此，只选择其中一种通常会更安全。

#### 3.3.3 多个下载

- 当一个命令行中有多个URL的时候，每个URL都需要自己的“存储指令”。如果不提供“存储指令”, curl会默认将数据发送到stdout。

- 如果你要下载两个URL并只为第一个URL提供保存位置，那么第二个URL将被发送到stdout，如下所示：
  
  ```bash
  curl -o one.txt http://example.com/1 http://example.com/2
  ```

- “存储指令”的读取和处理顺序与下载的URL的顺序相同，因此它们不一定要位于URL之后。你可以将所有输出选项放在最前面或最后面，或者与URL交错放置。以下写法是个等效的
  
  ```bash
  curl -o one.txt -o two.txt http://example.com/1 http://example.com/2
  curl -o one.txt http://example.com/1 -o two.txt http://example.com/2
  curl http://example.com/1 http://example.com/2 -o one.txt -o two.txt
  ```

- `--remote-name-all`：让-O成为所有给定URL的默认操作方式。你仍然可以为URL提供单独的“存储指令”，但对于没有提供单独“存储指令”的URL，则默认使用-O选项，而不是输出到stdout。

#### 3.3.4 速率限定

- 在传输数据时，curl会尝试尽快完成任务。它的速度取决于几个因素，包括计算机的硬件配置、网络连接带宽、远程服务器的负载和延迟。

- `--limit-rate`：让curl的速率不超过指定的字节/秒。速率限定的值可以包含一个后缀字母，K表示千字节，M表示兆字节，G表示千兆字节。
  
  ```bash
  curl --limt-rate 200K http://example.com
  ```

- 指定的限定速率是指整个传输过程的最大平均速率，也就是说，curl有可能会在某些很短的时间段内使用高于指定速率的传输速率，但平均速率不会超过指定速率。curl永远不会知道可能的最大速度是多少，它会在允许的范围内尽快完成传输。你可能会知道连接的最大速度，但curl不会。

#### 3.3.5 最大文件

- `--max-filesize`：可以为curl指定可接受的最大下载字节数，如果curl可以在传输开始前确定文件的大小，那么它就会在尝试下载更大的文件之前终止传输。
  
  ```bash
  curl --max-filesize 1024 http://example.com
  ```

- 在很多情况下，curl无法在传输开始前确定文件大小，那么这个选项就不会产生任何影响，即使下载的内容可能大于指定的数量。

#### 3.3.6 Metalink

- Metalink是一种文件描述标准，用于告诉客户端有多个位置保存了相同的内容。然后客户端可以选择从一个或多个位置下载内容。

- `--metalink`：curl支持Metalink格式，你可以通过--metalink选项来指定。指定的URL应该指向一个Metalink文件
  
  ```bash
  curl --metalink http://example.com/example.metalink
  ```

- 如果出现错误（如文件或服务器不可用）, curl将使用Metalink文件中列出的镜像进行故障转移。下载完成后，它还会验证文件的散列。Metalink文件会被下载到内存中，并在内存中进行处理，不会保存在本地文件系统中。

#### 3.3.7 在文件系统中保存元数据

- `--xattr`：告诉curl将某些文件元数据也保存在“扩展文件属性”中。这些扩展属性是保存在文件系统中的标准化的名称和值，前提是文件系统和操作系统支持扩展属性。

- 目前，URL保存在xdg.origin.url属性中，HTTP的Content-Type保存在mime_type属性中。如果指定了这个选项，但文件系统不支持扩展属性，则会发出警告。

#### 3.3.8 --raw

- `--raw`：禁用所有内部的HTTP内容解码或传输编码，取而代之的是传输未经修改的原始数据。

#### 3.3.9 失败重试

- `--retry`：通常curl只会尝试执行一次传输，不成功则返回错误。你可以使用--retry选项让curl重试失败的传输。

- `--retry-delay`：禁用指数退避算法，并设置自己的重试延迟。

- `--retry-max-time`：限制所有重试的总时间。

- `--max-time`：选项用于指定单个传输允许的最长时间。

- 如果尝试执行传输时返回临时错误，那么curl将在放弃之前重试指定的次数。如果将数字设为0（默认值）, curl将不会进行重试。临时错误可能是：超时、FTP 4xx响应码或HTTP 5xx响应码。

- curl在开始重试传输前会先等待一秒，对于后续的重试，等待时间加倍，直到达到10分钟，然后剩余的重试延迟就是10分钟。--retry-delay选项可以禁用这种指数退避算法，并设置自己的重试延迟

#### 3.3.10 恢复下载和下载范围

- 在恢复下载时，curl会先检查本地已存在的文件的大小，然后向服务器请求剩余的内容，并追加到本地文件中。curl还允许指定自定义恢复点，这些恢复点对应的内容可能不存在于本地。

- `-C`或`--continu-at`：可以告诉curl从哪里开始传输，选项的值可以是一个普通的数字字节偏移量，或者使用字符串-让curl根据它所知道的信息自己决定从哪里开始传输。如果使用-，那么curl将基于目标文件确定本地已存在的数据量，并将其作为向服务器请求更多数据的偏移量。
  
  ```bash
  # 从字节偏移量为100的位置开始下载FTP文件
  curl --continue-at 100 ftp://example.com/bigfile
  # 继续之前中断的下载
  curl --continue-at - ftp://example.com/bigfile
  ```

- `--range`：向远程服务器请求特定字节范围的内容
  
  ```bash
  curl --range 100-1000 ftp://example.com/bigfile
  ```

### 3.4 上传

- curl支持一下上传协议：FILE、FTP、FTPS、HTTP、HTTPS、IMAP、IMAPS、SCP、SFTP、SMB、SMBS、SMTP、SMTPS和TFTP。

- `-T`：发送某个需要上传的文件：
  
  ```bash
  curl -T filename http://example.com
  ```

#### 3.4.1 HTTP上传

- HTTP（和HTTPS）提供了几种数据上传方式，而curl也为上传提供了简单的命令行选项，其中有三种常见的方式，后文将逐一介绍。

- 在HTTP中，上传也可以是下载，它们属于同一个操作。事实上，很多下载是通过HTTP POST开始的。

- multipart formpost通常用于涉及文件上传的HTML表单。这种类型的上传也属于HTTP POST，但会根据一些特殊规则发送格式化的数据，因此被称为“multipart”。因为它以完全不同的方式发送格式化数据，所以你无法自己选择使用哪种类型的POST，这完全取决于接收服务器期望和可以处理什么内容。详细内容请查看下一部分

- 上传类型HTTP PUT会发送一个完整的资源，并将其保持原样放在远程站点，甚至可以替换那里已有的资源。目前，PUT是Web使用得最少的一种HTTP上传方式，而且大多数Web服务器并没有启用PUT。

#### 3.4.2 FTP上传

- 在使用FTP时，你可以看到将要访问的远程文件系统。你可以准确地告诉服务器你想要在哪个目录放置上传文件以及要使用哪个文件名。如果你指定的URL尾部是一个斜杠，那么curl会将本地文件名附加到URL后面，这样它就变成了远程的文件名：
  
  ```bash
  curl -T filename ftp://example.com/
  curl -T filename ftp://example.com/hello_filename
  ```

#### 3.4.3 SMTP上传

- 对于curl来说，发送电子邮件其实也是一种“上传”。你将邮件上传到SMTP服务器。在使用SMTP时，你需要在邮件中指定需要的标头（To:、From:、Date:等），因为curl不会自己添加这些东西。
  
  ```bash
  curl -T mail smtp://mail.example.com/ --mail-from user@example.com
  ```

### 3.5 连接和超时

#### 3.5.1 主机名解析

- HTTP客户端一般会通过Host标头告诉HTTP服务器它要连接到哪个服务器，因为通常同一个HTTP服务器实例会有多个名字，也就是有多个A或者CNAME记录，同时也可能会有多个站点。因此，将自定义的Host标头传给curl，就可以让服务器返回目标站点的内容，即使没有使用目标站点的主机名进行连接。
  
  ```bash
  # 请求www.example.com的index页面
  curl -H "Host: www.example.com" http://localhost/
  ```

- `-H`或`--header`：指定HTTP请求的Header。

- 如果设置自定义的Host标头并使用cookie，那么curl将提取自定义名称，并在匹配cookie时将其作为目标主机名。

- 与HTTPS服务器通信时，只使用Host标头是不够的。TLS协议中有一个单独的扩展字段，称作SNI（Server Name Indication，服务器名称指示），客户端用它来告诉服务器它想要与哪台服务器通信。curl只会从指定的URL中提取SNI。

- `--resolve`：为curl提供一个IP地址。将地址插入curl的DNS缓存中，以便curl相信那就是自己解析得到的地址。
  
  ```bash
  # 需要指定域名端口，以及对应的ip地址
  curl --resolve example.com:80:127.0.0.1 http://example.com
  ```

- 如果使用的是HTTPS，那么将发送URL中的SNI，并且curl会验证服务器端的响应，以确保使用的是URL中的名字。

- 可以指定多个--resolve进行多个重定向，如果你的URL使用了HTTP重定向，或者你希望在命令行中使用多个URL，那么这么做会很方便。

- `--connect-to`：--connect-to选项提供了一个与--resolve很相近的小变体。当需要连接特定主机名和端口时，可以通过这个选项为curl指定替换主机名和端口。它将源主机名和源端口重定向到目标主机名和目标端口。
  
  ```bash
  curl --connect-to example.com:80:localhost:9394 http://example.com 
  ```

#### 3.5.2 基于c-ares库的域名解析

- 可以用不同的域名解析器库来构建curl。其中一个库是c-ares，如果用c-ares构建curl，则可以获得一些额外的功能，比如，它能够更具体地指定使用哪些DNS服务器以及DNS流量如何使用网络。

- `--dns-servers`：指定curl使用的DNS服务器，而不是使用默认的那个。

- `--dns-ipv4-addr`和`--dns-ipv6-addr`：让curl将DNS通信的本地端“绑定”到特定的IP地址。

- `--dns-interface`：让curl为DNS请求使用特定的网络接口。

#### 3.5.3 连接超时

- curl通常会与主机建立TCP连接，以作为网络传输的初始化部分。如果网络状况不稳定或远程服务器出现故障，TCP连接可能会失败或速度很慢。要想减少对脚本或其他应用的影响，你可以为curl设置允许尝试连接的最长时间（以秒为单位）

- `--connect-timeoout`：设置curl允许尝试连接的最长时间，如果curl在指定时间内没有建立连接，则返回错误。建立连接前的所有必要步骤都必须在给定时间内完成。如果未能在给定时间内建立连接，curl将抛出超时错误码（28）并退出。连接超时可以是亚秒精度的十进制值。
  
  ```bash
  curl --connect-timeout 2.781 https://example.com
  ```

- 连接超时只能限制curl在建立连接前花费的时间，一旦成功建立TCP连接，则不再受这个时间的限制。如果指定较短的超时时间，则可能会影响curl连接远程服务器、慢服务器或通过不可靠网络访问的服务器。

#### 3.5.4 网络接口和本地端口号

- 在具有多个网络接口并连接到多个网络的计算机上，有时你可以决定使用哪个网络接口来传出网络流量，或者在通信中使用哪个原始IP地址（在你拥有的多个IP地址之外）。

- `--interface`：可以告诉curl你希望将通信的本地端“绑定”到哪个网络接口、哪个IP地址或主机名
  
  ```bash
  curl --interface eth1 http://example.com
  curl --interface 192.168.0.1 http://example.com
  curl --interface machine1 http://example.com
  ```

- TCP连接是在本地的IP地址和端口号与远程的IP地址和端口号之间建立起来的。你可以在URL中指定远程端口号，这样通常有助于识别目标服务。本地端口号通常由网络栈随机分配给TCP连接。在某些情况下，你会发现自己处于网络设备、防火墙或类似设备后面，它们对设置传出连接的源端口号施加了约束。对于这种情况，你可以指定curl使用哪个本地端口来绑定连接

- 你可以指定单个端口号或端口号区间。建议使用端口号区间，因为端口是稀缺资源，你想用的端口可能已经被占用。如果无法获取指定的本地端口号（或区间）, curl将返回错误并退出。此外，在大多数操作系统上，如果没有更高权限（root），就无法绑定1024以下的端口号。如果可以避免，最好不要以root身份运行curl。

- `--local-port`：指定本地端口号
  
  ```bash
  curl --local-port 4000-4200 http://example.com/
  ```

#### 3.5.5 keepalive

- 空闲时，TCP连接可以完全没有任何流量。因此，完全空闲的连接与因网络或服务器问题而过时的连接很难明显区分。现在很多网络设备（如防火墙或NAT）都可以跟踪TCP连接，它们可以转换地址，阻止“错误的”传入数据包，等等。这些设备通常将空闲N分钟的连接视为已死亡，其中N因不同的设备而异，通常是10分钟，甚至更短。

- 避免慢连接（或空闲连接）被视为死亡并被错误杀死的其中一种方法是确保使用了TCP的keepalive。keepalive是TCP协议的一项特性，它会来回发送“ping数据帧”，避免连接进入完全空闲的状态。它帮助空闲连接检测中断（即使在没有流量的情况下），并让中间系统不会认为连接已死亡。因此，curl默认使用TCP keepalive

- `--no-keepalive`：禁用keepalive

- `--keepalive-time`：设置keepalive的间隔时间，默认是60秒

#### 3.5.6 超时、允许的最长时间和允许的最慢速率

- 网络操作本质上是不可靠的，甚至是脆弱的，因为它们依赖了一系列服务和网络。这些服务的可用性时有时无，性能也可能随着时间的推移发生很大变化。TCP允许网络在很长一段时间内完全断开连接，传输的参与者不一定会注意到。这导致的结果就是有时网络传输需要很长时间。另外，curl的大多数操作默认没有设置超时！

- `-m`或`--max-time`：在curl抛出超时错误码（28）并退出前，可以用-m或--max-time选项告诉curl最长有多少时间（以秒为单位）可用。当指定的时间耗尽，无论当时发生什么，curl都会退出，即使它正在传输数据。

- 如果只能为curl设定固定的最长操作时间仍然不够灵活，特别是进行脚本传输且文件大小和传输时间变化非常大时，需要将固定超时时间设得很高，这样才能覆盖最坏的情况。

- `--speed-time`和`--speed-limit`：作为固定超时时间的替代方案，你可以告诉curl，如果传输速率低于某个特定值，并且在某个时间段内一直低于这个值，那么就放弃传输。
  
  ```bash
  # 如果15秒内的传输速率低于1000字节/秒，则停止传输
  curl --speed-tim 15 --speed-limit 1000 http://example.com
  ```

### 3.6 netrc

- Unix系统为用户提供了一种存储远程FTP服务器用户名和密码的方式，也就是netrc文件。FTP客户端也一直支持这种方式，让用户可以快速登录到已知服务器，无须每次重新手动输入凭证。.netrc文件通常保存在用户的主目录中。（在Windows系统上，curl会查找一个名为_netrc的文件。）

- netrc是一个已经广泛使用的概念，curl也支持它。并且，curl并没有将这项功能局限在FTP上，而是可以为任意协议获取凭证。

- .netrc的文件格式很简单：先在前面的行指定计算机名，后面的行是与该计算机相关的登录名和密码。主要是三部分：
  
  - 机器名：用于标识远程机器名。curl在.netrc文件中查找与URL中指定的远程机器匹配的名称节点。找到匹配的名称后再处理后续的.netrc节点，直到到达文件末尾或遇到另一个机器名。
  
  - 登录名：远程机器的用户名字符串。
  
  - 密码字符串：登录远程机器的密码。如果存在这个节点，并且远程服务器要求使用密码登录，那么curl就会提供这个字符串。注意，如果.netrc文件中存在这个节点，你需要确保这个文件不会被用户以外的人读取。

- 假设主机名为example.com，用户名为hello，密码为world，那么.netrc的内容如下所示：
  
  ```textile
  machine example.com
  login hello
  password world
  ```

- `-n`或`--netrc`：告诉curl查找并使用.netrc文件中的信息。

- `--netrc-file`：与--netrc类似，只是你还额外提供了文件的实际路径。当你想要提供的信息位于另一个目录或者想要使用其他文件名时，这会非常有用。

- `--netrc-optional`：与--netrc类似，只不过.netrc文件是可选的。

### 3.7 代理

- 代理是代表客户端执行某项操作的机器或软件，类似中间人角色。

- curl支持几种不同类型的代理。默认代理类型是HTTP，因此，如果指定没有scheme（通常为http://）的代理主机名（或IP地址），那么curl会假设它就是HTTP代理。curl还提供了多个选项来设置代理类型，而不是使用scheme前缀。

#### 3.7.1 PAC

- 某些网络环境为不同场景提供了不同类型的代理，浏览器为此提供了一种名为“代理自动配置”（Proxy Auto Config, PAC）的定制化处理方式。

- PAC文件包含了一个JavaScript函数，用于决定给定的网络连接（URL）应该使用哪个代理，或者也可以不使用代理。通常浏览器通过一个URL从本地网络读取PAC文件。

- 因为curl不支持JavaScript，所以也不支持PAC文件。如果你的浏览器和网络使用了PAC文件，最简单的办法是手动读取PAC文件，并找出需要指定给curl的代理。

#### 3.7.2 HTTP代理和HTTPS代理

- HTTP代理是客户端用来通过HTTP完成传输的代理。默认情况下，curl假设你使用-x或--proxy选项指定的主机就是HTTP代理。另外，除非你还指定了端口号，否则它将默认使用端口3128（使用这个特定端口号纯粹是历史原因）。

- `-x`或`--proxy`：指定HTTP代理主机（和端口）
  
  ```bash
  # 用主机192.168.0.1端口8080上的代理来请求example.com网页
  curl -x 192.168.0.1:8080 http://example.com
  ```

- 如果在与代理通信时通过-v启用详细模式，你将看到curl连接的是代理而不是远程服务器，而且它使用了稍微不同的请求行。

- HTTPS旨在为客户端和服务器（以及后端）提供安全的端到端隐私。为了在使用HTTP代理时仍然能够提供这种安全隐私，HTTP协议提供了一种特殊的请求，curl可以用它设置一个通道，这个通道经过代理，并可以对流量进行加密和验证。这个HTTP方法就是CONNECT。

- 用CONNECT方法设置好通道后，数据流经通道时就会被加密，代理在不破坏加密的情况下是无法查看或修改流量的

#### 3.7.3 MITM代理

- MITM是Man-In-The-Middle（中间人）的简写。想要对TLS加密流量进行监控的公司通常会在“企业环境”和其他地方部署MITM代理。

- MITM要求用户在客户端安装自定义“信任根”（CA证书），代理将会终结来自客户端的所有TLS流量，然后模拟远程服务器并充当代理。接下来，代理会返回由自定义CA签名的生成证书。这类代理通常会捕获从客户端发到远程计算机TCP 443端口的所有流量。在这样的网络中运行curl会导致其HTTPS流量被捕获。当然，这种做法为中间人窥探和解密TLS流量提供了机会。

#### 3.7.4 非HTTP协议

- “HTTP代理”意味着代理本身使用的是HTTP协议。HTTP代理主要用于代理HTTP流量，但也支持其他协议，比如FTP。

- 通过HTTP代理进行FTP传输意味着需要假装其他协议就像HTTP一样，然后要求代理“获取某个URL”，即使这个URL不是基于HTTP的。这点很重要，因为这意味着通过HTTP代理发送流量时，即使指定了FTP URL, curl也不会真正使用FTP，因此FTP的相关特性将不起作用
  
  ```bash
  curl -x http://proxy.example.com:8080 ftp://ftp.example.com/file.txt
  ```

#### 3.7.5 HTTP代理通道

- `-p`或`--proxytunnel`：让curl穿过HTTP代理。

- 大多数HTTP代理允许客户端“穿过”它，到达另一端的服务器。通过HTTP代理执行HTTPS传输就是一个很好的示例。

- 通过代理执行HTTPS传输时，通常会连接到远程默认的HTTPS TCP 443端口。你会发现，大多数HTTP代理只允许连接到这个端口，或许还会有其他少数几个端口。大多数代理会拒绝客户端连接到随机端口上。不过，假设HTTP代理允许连接到随机端口，那么你可以要求它通过通道连接到远程服务器的任意端口，这样就可以“正常”使用其他协议。可以按照以下方式使用FTP通道：
  
  ```bash
  curl -p -x http://proxy.example.com:8080 ftp://ftp.example.com/file.txt
  ```

- `--proxy1.0`：让curl在发送给HTTP代理的CONNECT请求中使用HTTP/1.0

#### 3.7.6 SOCKS类型代理

- SOCKS是一种代理协议，curl支持SOCKS 4和SOCKS 5，每种版本都有两种使用方法。

- 可以通过-x选项指定SOCKS版本，对于这种情况，给定的代理主机的scheme部分就是SOCKS版本，你也可以使用单独的选项来指定。

- 与SOCKS4类似，但是SOCKS4a不在本地解析主机名，会将主机名发送给服务器。

- 与SOCKS5类似，但是SOCKS5-hostname不在本地解析主机名，会将主机名发送给服务器。

- 示例：
  
  ```bash
  # SOCKS4
  curl -x socks4://proxy.example.com http://example.com
  curl --socks4 proxy.example.com http://example.com
  # SOCKS4a
  curl -x socks4a://proxy.example.com http://example.com
  curl --socks4a proxy.example.com http://example.com
  # SOCKS5
  curl -x socks5://proxy.example.com http://example.com
  curl --socks5 proxy.example.com http://example.com
  # SOCKS5a
  curl -x socks5h://proxy.example.com http://example.com
  curl --socks5-hostname proxy.example.com http://example.com
  ```

#### 3.7.7 代理身份验证

- HTTP代理可以要求进行身份验证，因此curl需要向代理提供适当的凭证，否则代理将返回407 HTTP响应码。代理的身份验证与“普通”的HTTP身份验证非常相似，但它与服务器身份验证是分开的，这样客户端就可以单独使用常规的主机身份验证和代理身份验证。

- `-U`或`--proxy-user`：设置代理身份验证的用户名和密码
  
  ```bash
  curl -U hello:world -x proxy.example.com:80 http://example.com
  ```

- `--proxy-digest`和`--proxy-negotiate`和`--proxy-ntlm`：一些代理会要求使用另一种身份验证方案（代理在返回407响应码时会在标头中告诉你使用哪个方案），你可以用--proxy-digest、--proxy-negotiate、--proxy-ntlm指定要使用的方法。

- `--proxy-anyauth`：你还可以让curl找出代理支持的认证方法，然后通过--proxy-anyauth使用这些方法（可能需要发送额外的请求）。
  
  ```bash
  curl -U hello:world --proxy-anyauth -x proxy.example.com:80 http://example.com
  ```

#### 3.7.8 使用HTTPS连接代理

- 前面提到的连接代理的所有协议都是明文协议，如HTTP和SOCKS。使用这些方法将导致某些人可以通过代理所在的本地网络窃听你的流量。解决方案之一是使用HTTPS连接代理，从而建立一个安全的加密连接，这样就不容易被监视。

#### 3.7.9 代理环境变量

- curl会在运行前检查是否存在某些特殊的环境变量，然后根据这些变量决定是否使用代理。可以通过设置[scheme]_proxy变量来指定代理（与用-x指定主机名的方式相同）。因此，要想让curl访问HTTP服务器时使用代理，则需要设置http_proxy环境变量，如下所示：
  
  ```bash
  http_proxy=http://proxy.example.com:80
  cur; http://example.com
  ```

- 也可以设置ftp_proxy、https_proxy，等等。除http_proxy外的所有环境变量名也可以是大写的，如HTTPS_PROXY。还可以通过单个变量ALL_PROXY来设置所有的协议。如果存在某个特定的协议变量，则优先使用这个变量。

- 使用环境变量设置代理时，很容易遇到需要将一个或几个无须使用代理的主机名排除在外的情况。这个时候需要使用NO_PROXY变量。我们使用逗号分隔这些不需要使用代理的主机名。你也可以将NO_PROXY设置为单个星号（'＊'）以匹配所有主机。

- `--noproxy`：与NO_PROXY变量的作用相同。

### 3.8 退出状态

- curl在出现问题时返回一个可用的退出码，如果一切正常则返回0（零）

- 可用的退出码列表：[curl可用的退出码](./curl可用的退出码.md)

- 以非零状态码退出时，curl还会输出一个错误消息（除非使用了--silent选项）。这个错误消息可能会包含一些额外的信息，因此，相同的错误码可能会与不同的错误消息一起出现。

- 前面的状态码列表包含了很多标记为“未使用”的数字。这些状态码没有用于现在版本的curl中，但过去曾经被使用过或打算使用。它们很可能会用于未来的curl版本中。

- 另外，状态码列表中最大的错误状态码是92，但这并不代表未来的curl版本不会在这个数字之后添加更多的退出码。

### 3.9 FTP

- FTP出现在互联网和计算机的一个特殊时代，因此它的工作方式与大多数其他协议略有不同。如果一切运行正常，通常可以忽略这些差异，但如果出现异常，就很有必要了解这些差异了。

- FTP协议是一种命令和响应协议（ping-pong），客户端发送命令，服务器做出响应。对于普通的传输，需要发送5～8个命令，服务器则需要做出等量响应。如果服务器是在远程，那么开始传输文件前需要花很多时间在ping-pong上。对于较小的文件，初始化命令很可能比实际数据传输花费更长的时间。

#### 3.9.1 传输模式

- 当FTP客户端开始传输数据时，它会向服务器指定要使用哪种“传输模式”。curl支持的两种传输模式是“ASCII”和“BINARY”。ASCII基本上用于文本，服务器将发送带有换行符的文件，而BINARY表示发送未经修改的数据，并假设要发送的文件不是文本文件。

- curl将默认使用FTP的BINARY传输模式，你可以通过-B、--use-ascii选项或者以；type=A作为URL的结尾来切换到ASCII模式。

- `-B`或`--use-aciii`：对于FTP传输，切换为ASCII模式

#### 3.9.2 身份验证

- 访问FTP通常需要用户名和密码，否则就无法访问。有些系统允许“匿名”访问，你可以使用自己喜欢的任意用户名和密码登录。

- 在使用curl进行FTP传输但没有指定用户名或密码的情况下，它将使用用户名anonymous和密码ftp@example.com。

- 要想提供其他用户名和密码，可以通过-u或--user选项将它们传给curl，或者包含在URL中。
  
  ```bash
  curl -u hello:world ftp://example.com/download
  curl ftp://hello:world@example.com/download
  ```

#### 3.9.3 建立连接

- FTP使用两个TCP连接！客户端在连接到FTP服务器时建立第一个连接，称作控制连接。作为初始连接，它将负责处理身份验证、切换到正确的远程服务器目录，等等。当客户端准备好传输文件时，第二个TCP连接将建立，并通过这个连接传输数据。因为各种原因，建立第二个连接会很麻烦。

- 建立用于数据传输的TCP连接有两种方式：主动连接和被动连接

- 客户端可以请求服务器回连到客户端来建立连接，即所谓的“主动”连接。这可以通过PORT或EPRT命令来完成。要想让远程主机回连到客户端端口，它们中间不能有防火墙或其他网络设备，但现实情况并非总是如此。

- `-P`或`--ftp-port`：请求进行主动传输。这个选项允许你指定要使用的地址，不过通常可以通过-P -指定当前的地址
  
  ```bash
  curl -P - ftp://example.com/download
  ```

- `--no-eprt`：可以通过--no-eprt选项明确要求curl不使用EPRT（这是一个比PORT略新的命令）。

- curl默认请求使用“被动”连接，它向服务器发送PASV或EPSV命令，服务器为第二个连接打开一个新端口，然后curl会连接到这个端口上。连接到新端口通常更加容易，对终端用户和客户端的限制也较少，但服务器端所在的网络必须允许这些连接。

- `--ftp-pasv`：默认情况下，curl启用的是被动连接，但如果之前已启用了主动连接，可以使用--ftp-pasv切换回被动连接。

- `--ftp-skip-pasv-ip`：有时候，因为服务器的一些奇怪设置，当curl发出PASV命令时，服务器会向curl返回一个IP地址，但这个地址可能是错误的，以至于curl无法建立数据连接。对于这种（很少见的）情况，你可以要求curl忽略PASV响应中返回的IP地址（--ftp-skip-pasv-ip），然后使用与控制连接相同的IP地址。

#### 3.9.4 遍历目录

- 在使用FTP命令遍历远程文件系统时，curl可以通过几种不同的方式来访问目标文件，即用户想要传输的文件，分别为multicwd、nocwd、singlecwd。

- `--ftp-method`：指定FTP访问文件的方法

- mulicwd：curl可以顺着文件树结构的每个目录执行一次更改目录命令（CWD）。如果完整路径是one/two/three/file.txt，那么请求传输file.txt文件前会执行三次CWD命令。因此，如果路径深度很深，则需要执行大量的CWD命令。早期规范（RFC 1738）强制要求使用这种方法，这也是curl的默认行为。
  
  ```bash
  curl --ftp-method multicwd ftp://example.com/one/two/three/file.txt
  ```

- nocwd：与为每个目录执行一次CWD命令相反的做法是不更改目录。这种方法请求服务器一次性使用整个路径，因此速度非常快。但有时在服务器上使用这种方法也会有问题，而且它并不符合标准。
  
  ```bash
  curl --ftp-method nocwd ftp://example.com/one/two/three/file.txt
  ```

- singlecwd：这种方法介于上述两种FTP方法之间。它会向目标目录发出一个CWD命令，然后请求指定的文件。
  
  ```bash
  curl --ftp-method singlecwd ftp://example.com/one/two/three/file.txt
  ```

#### 3.9.5 高级特性

- 你可以让curl列出远程FTP目录列表，只需要在URL末尾添加斜杠。如果URL以斜杠结尾，curl会认为你要列出这个目录。如果它不是目录，你很可能会得到一个错误。

- `-l`或`--list-only`：在使用标准的FTP命令LIST列出目录时，返回的目录结构没有标准的格式。返回的列表通常是可以被人类理解的，但不同的服务器返回的列表结构稍有不同。要想列出目录中的所有内容并避免出现特殊格式，可以使用--list-only（或-l）选项。curl会发出FTP命令NLST。
  
  ```bash
  curl --list-only ftp://example.com/dir/
  ```

- NLST也有一些奇怪的地方，有些FTP服务器在响应NLST命令时只列出文件部分，不包含目录和符号链接！

- `-T`或`--upload-file`：要想上传文件到FTP服务器，需要在URL中指定整个目标文件的路径和名字，并用-T或--upload-file选项指定要上传的本地文件。另外，如果目标URL以斜杠结尾，curl会自动将本地路径中的文件名追加到URL中，并将其作为远程文件名。
  
  ```bash
  curl -T localfile ftp://example.com/dir/path/
  ```

- -T参数支持通配。
  
  ```bash
  curl -T localfile[1-100] ftp://example.com/dir/path/
  ```

### 3.10 SCP和SFTP

- 如果用第三方库libssh2来构建curl，那么它就可以支持SCP和SFTP协议。

- SCP和SFTP都是建立在SSH之上的协议，SSH是一种类似于TLS的安全加密数据协议，但在某些方面有所不同。例如，SSH不使用任何类型的证书，而是使用公钥和私钥。如果使用得当，SSH和TLS都可以提供强大的安全传输。（SCP协议通常被认为是二者当中的“害群之马”，因为它的可移植性比较差，通常只能在Unix系统之间使用。）

- SFTP和SCP的URL与其他URL类似，使用这些协议下载文件的方式与其他协议差不多。如果URL尾部是斜杠，那么SFTP（不是SCP）可以获取文件列表。
  
  ```bash
  curl -u user sftp://example.com/file
  ```

- 在使用SFTP或SCP URL请求文件时，给定的文件路径被视为远程服务器上的绝对路径，除非你特别指定要使用用户主目录的相对路径。你可以使用/～/来指定用户主目录的相对路径。这与FTP URL完全相反，因此容易导致用户混淆。

- 安全的网络客户端需要确保远程主机就是它要与之通信的主机。在使用基于TLS的协议时，这是通过客户端验证服务器的证书来实现的。SSH协议不使用服务器证书，但每个服务器都需要提供唯一的密钥。而且与TLS不同的是，SSH没有证书颁发机构，因此客户端需要确保主机的密钥与它已经知道的（通过其他方式获得的）信息相匹配。

- 密钥匹配通常需要用到密钥的散列和客户端用来保存已知服务器散列的文件，这个文件通常叫作known_hosts，存放在专门的SSH目录中。在Linux系统上，通常是～/.ssh目录。

- 当连接到SFTP或SCP主机时，curl会先确认主机的密钥散列已存在于已知主机的文件中，否则它将拒绝后续操作，因为它不相信现在连接的服务器就是真正的目标主机。如果known_hosts中存在正确的散列，curl就会开始执行传输。

- `-k`或`--insecure`：要想强制curl跳过检查，可以使用-k或--insecure选项。在使用这两个选项时必须非常小心，因为这样有可能检测不到中间人的攻击。

### 3.11 IMAP和POP3

- 在互联网领域，从服务器读取或下载电子邮件（如果不把基于Web的读取方式算在内）的协议主要有两种，它们是IMAP和POP3。前者稍微更加现代一些。curl同时支持这两种协议。
  
  ```bash
  # 列出邮件编号和大小
  curl pop3://mail.example.com/
  # 下载邮件
  curl pop3://mail.example.com/1
  # 删除邮件
  curl --reuqest pop3://mail.example.com/1
  ```

### 3.12 SMTP

- SMTP是简单邮件传输协议（Simple Mail Transfer Protocol）的简写。curl支持将数据发送到SMTP服务器，可以通过一系列命令行选项将电子邮件发送给指定的一组收件人。

- `--mail-rcpt`：通过该选项告诉服务器收件人的邮件地址（至少一个）。可以多次使用这个选项，curl会告诉服务器所有指定的收件人都应该收到这封电子邮件。

- `--mail-from`：通过该选项告诉服务器哪个是发件人的邮件地址。这个电子邮件地址与显示在邮件正文中的From：不一定相同。

- 使用curl发送SMTP时，以上两个命令行选项是必选项。然后，你需要提供要发送的电子邮件数据。它应该是一个使用RFC 5322指定格式的（文本）文件。它由一组标头和正文组成。标头和正文都需要进行正确编码。标头通常包括To:、From:、Subject:、Date：等。
  
  ```bash
  curl smtp://mail.example.com \
   --mail-from myself@example.com \
   --mail-rcpt receiver@example.com \
   --upload-file email.txt
  ```

- 有些邮件提供商允许或要求使用SSL进行SMTP传输。他们可能为SSL分配了专门的端口，或允许从明文连接升级到SSL连接。如果你的邮件提供商提供了专门的SSL端口，则可以用smtps:// 代替smtp://，默认的SMTP SSL端口为465，并且整个连接都是基于SSL，如smtps://smtp.gmail.com/。

- `--ssl`：尝试使用SSL/TLS（FTP、IMAP、POP3、SMTP）。

- `--ssl-reqd`：要求使用SSL/TLS（FTP、IMAP、POP3、SMTP）。

- 如果你的提供商允许从明文升级到安全传输，则可以使用以上两个选项。

- 可以通过--ssl选项让curl尝试但不一定要升级到安全传输：
  
  ```bash
  curl --ssl smtp://mail.example.com
   --mail-from myself@example.com \
   --mail-rcpt receiver@example.com \
   --upload-file email.txt
  ```

- 也可以通过--ssl-reqd选项让curl一定要升级到安全传输：
  
  ```bash
  curl --ssl smtp://mail.example.com
   --mail-from myself@example.com \
   --mail-rcpt receiver@example.com \
   --upload-file email.txt
  ```

- SMTP请求的路径部分指定了与邮件服务器通信期间显示的主机名。如果省略了路径，那么curl将尝试使用本地计算机的主机名。不过，这样可能不会得到某些邮件服务器要求的完全限定名。你可以在路径中指定其他名字，例如计算机的完全限定名，完全限定名可以通过外部函数获取，例如gethostname或getaddrinfo。

- 要想curl在HELO或EHLO命令中将client.example.com发送到邮件服务器mail.example.com，参见以下方式：
  
  ```bash
  curl smtp://example.com/client.example.com
  ```

- 用普通邮件客户端发送电子邮件时，它首先会检查目标域名的MX记录。如果向joe@example.com发送电子邮件，客户端将获取example.com的MX记录，以便找出向example.com发送电子邮件时需要用到的邮件服务器。

- curl本身不进行MX查找。如果想要指定向某个服务器发送电子邮件，需要先将它们找出来，然后在curl中指定这些服务器。

### 3.13 TLS

- TLS是传输层安全（Transport Layer Security）的简写，这项技术之前叫作SSL。不过SSL这个术语并没有真正消失，现在TLS和SSL这两个术语经常互换使用，实际上它们指的是同一个东西。TLS是TCP“之上”的一个加密安全层，基于强大的公钥加密和数字签名实现数据防篡改，并确保服务器的可靠性。

- 当curl连接到TLS服务器时，它们会协商如何使用协议，协商过程涉及一些参数和变量，双方需要在这些参数和变量上达成一致。

- 协商的其中一个参数是关于使用哪种加密算法，即所谓的密码。随着时间的推移，安全研究人员会发现现有密码存在缺陷和弱点，因此它们会逐渐被淘汰。

- `--ciphers`：可以通过-v选项获得协商过程中确定的密码和TLS版本，也可以通过--ciphers选项更改协商中优先使用的密码

#### 3.13.1 启用TLS

- curl支持很多协议的TLS版本。HTTP有HTTPS、FTP有FTPS、LDAP有LDAPS、POP3有POP3S、IMAP有IMAPS、SMTP有SMTPS。如果服务器端也支持，则可以在curl中使用这些协议的TLS版本。

- 启用TLS的方法有两种。
  
  - 一种是从第一次进行连接握手时就使用TLS；
  
  - 另一种是用协议特定指令将连接从明文“升级”到TLS；

- `--ssl`：尝试将非TLS连接升级到基于TLS的连接。

- `--ssl-reqd`：要求将非TLS连接升级到基于TLS的连接。

- 如果在URL中明确指定了协议的TLS版本（以“S”字符结尾）, curl将尝试从一开始就建立TLS连接，而如果指定了非TLS版本，通常可以用--ssl选项将连接升级到基于TLS的连接。

- 使用--ssl选项意味着curl会尝试将连接升级到TLS，但如果失败，它仍将继续使用明文协议来执行传输。如果一定要使用TLS连接，可以使用--ssl-reqd选项，使用这个选项时，如果curl无法成功进行TLS协商，传输就会失败。
  
  ```bash
  curl --ssl-reqd ftp://ftp.example.com/file.txt
  ```

#### 3.13.2 SSL和TLS版本

- SSL是在20世纪90年代中期发明并发展起来的。SSL 2是第一个在互联网上广泛使用的版本，但很久以前就被认为是不安全的。然后就有了SSL 3，但它也被认为不够安全。

- TLS 1.0是第一个“标准”。RFC 2246于1999年发布。TLS 1.1于2006年问世，此版进一步提高了安全性，然后是2008年的TLS 1.2。十年来，TLS 1.2一直是TLS的黄金标准。

- 2018年8月，IETF最终确定了TLS 1.3（RFC 8446），并将其作为标准发布。这是迄今为止最安全、最快的TLS版本。但因为刚发布不久，很多软件、工具和库尚不支持它。

- curl默认使用SSL/TLS的“安全版本”，也就是说，除非特别说明，否则它不会进行SSL 2或SSL 3协商。实际上，一些TLS库不再支持这些协议，因此，在很多情况下，除非你费了很大劲，否则curl甚至无法使用这些协议版本。

#### 3.13.3 验证服务器证书和CA存储

- 如果不确定是否正在与正确的主机通信，那么就没有必要与服务器建立安全连接。如果不知道这一点，就有可能是在与一个冒名顶替者通信。为了确认是否与正确的TLS服务器通信，curl会使用一组存储在本地的CA证书来验证服务器的证书签名。作为TLS握手的一部分，所有服务器都需要向客户端提供证书，并且所有使用TLS的公共服务器都会从证书颁发机构获取证书。

- `-k`或`--insecure`：降低安全级别。在极少数情况下，即使证书验证失败，你可能仍然希望与TLS服务器展开通信。如果是这样，则必须接受你的通信可能受到中间人攻击的事实。

- curl需要一个“CA存储”（包含了一组CA证书）来验证TLS服务器。如果使用某个平台的“本地”TLS库来构建curl，那么这个库很可能使用了本地的CA存储。如果不是，那么必须让curl知道本地CA存储的位置，或者用户在调用curl时需要提供指向CA存储的路径。

- `--cacert`：指定要在TLS握手中使用的CA捆绑包，这个捆绑包需要采用PEM格式，也可以通过环境变量CURL_CA_BUNDLE设置捆绑包的完整路径。

- 在Windows上，没有用本地TLS库（Schannel）构建的curl需要按照一定顺序来查找和使用CA存储。curl将按以下顺序在这些目录中搜索名为“curl-ca-bundle.crt”的CA证书文件。
  
  - 应用的目录。
  
  - 当前工作目录。
  
  - Windows系统目录（如C:\windows\system32）。
  
  - Windows目录（如C:\windows）。
  
  - %PATH%中的所有目录。

#### 3.13.4 证书固定

- TLS证书固定用于验证签署服务器证书的公钥是否保持不变，即公钥是否是“固定”的。在进行TLS或SSL协商时，服务器会发送一个证明其身份的证书。如果从证书中提取的公钥与命令行给定的公钥不完全匹配，curl将在开始发送或接收数据前中止连接。

- 可以为curl指定一个文件，让它从这个文件中读取sha256值，或者直接在命令行中用“sha256//”前缀指定base64编码的散列。可以指定一个或多个这样的散列，用分号（;）分隔。
  
  ```bash
  curl --pinnedpubkey "sha//" https://example.com
  ```

- 并不是所有的TLS后端都支持这个特性。

#### 3.13.5 OCSP装订

- OCSP装订是指通过TLS扩展（证书状态请求，Certificate Status Request）要求服务器在握手过程中提供新的“证据”，即返回的证书仍然有效。这是一种确保服务器证书未被撤销的方法。如果服务器不支持这个扩展，那么测试将会失败，curl将返回错误。服务器不支持这种扩展的情况太常见了。

- `--cert-status`：要求在握手时使用装台请求

- 只有OpenSSL、GnuTLS和NSS后端支持这个特性。

#### 3.13.6 客户端证书

- TLS客户端证书是客户端以加密的方式向服务器证明自己就是真正对等方的一种方式。在命令行中指定证书和相应的密钥，然后在与服务器进行TLS握手时传递它们。密钥通常有密码保护，你需要提供密码或者以交互式的方式输入密码。

- `--cert`和`--key`：curl提供了一些选项让你可以指定单个文件，这些文件既是客户端证书又是私钥（通过--cert选项连接在一起），也可以用--key单独指定密钥文件：
  
  ```bash
  curl --cert mycert:mypassword https://example.com
  curl --cert mycert:mypassword --key mykey https://example.com
  ```

- 对于某些TLS后端，也可以传递不同类型的密钥和证书：
  
  ```bash
  curl --certmycert:mypassword --cert-type PEM \
   --key mykey --key-type PEM
   https://example.com
  ```

## 第四章 用curl执行HTTP操作

### 4.1 协议基础

- 每个HTTP请求都包含了一个方法，有时也被称为动词。最常用的方法是GET、POST、HEAD和PUT。不过，通常不会在命令行中指定方法，具体使用哪个方法取决于使用的选项。默认方法是GET, -d或-F选项对应POST方法，-I对应HEAD方法，-T对应PUT方法。

- `-G`或`--get`：使用GET方法。

- -`d`或`--data`：使用POST方法。

- `-F`：使用POST方法。

- `-I`：使用HEAD方法。

- `-T`：使用PUT方法。

- 如果HTTP URL路径中包含/../或/./这样的字符串，curl会在将路径发送到服务器前自动压缩它们，这些字符串的含义与本地文件系统中的路径含义是一样的。/../前面的部分会被移除，因此/hello/sir/../会变成/hello/, /./会直接被移除，因此/hello/./sir/会变成/hello/sir/。

- `--path-as-is`：防止curl在将这些路径字符串发送到服务器前压缩它们

### 4.2 响应

- 当HTTP客户端与服务器进行HTTP对话时，服务器将返回HTTP响应消息，否则curl会认为服务器出了问题，然后返回 (52) 错误码，并显示错误消息“Emptyreply from server”。

- HTTP响应消息具有一定的大小，curl需要知道它是多少。HTTP响应消息的结束可以通过几种不同的方式来表示，其中最基本的方法是在消息中使用Content-Length：标头，并在标头中指定响应正文的字节数。

- `--ignore-content-length`：一些早期的HTTP服务器在文件大小超过2GB时会有问题，并在Content-Length：标头中发送负的字节数，或者发送错误的数据。可以通过--ignore-content-length选项让curl完全忽略Content-Length：标头。这样做可能会产生一些负作用，但至少可以获取到数据。

- HTTP响应消息的第一行有一个3位数的响应码。响应码是服务器用来告诉客户端它是如何处理请求的一种方式。注意，即使响应码表明无法获取所请求的文档（或类似情况）, curl也不会将其视为错误。只要HTTP发送和接收成功，curl都将其视为正常情况。

- 因为同一个curl传输中可能存在一个HTTP请求和一个CONNECT请求，所以我们通常需要将CONNECT响应（来自代理）与远程服务器的HTTP响应区分开。CONNECT也是一个HTTP请求，因此它将获得相同数值范围的响应码，可以用--write-out获取这个响应码。

- HTTP 1.1服务器可以进行“分块”编码响应，但HTTP 1.0不支持这种特性。在发送分块响应时，响应消息中不包含用于指示内容大小的Content-Length：标头。相反，消息中包含了Transfer-Encoding: chunked标头，用于告诉curl后面还有其他数据块，数据将以一系列“块”的形式出现。每个数据块都以块的大小（十六进制）开头，然后是换行符，然后是块的内容。这个过程会一直重复，直到响应结束，并以大小为零的块作为结束。对于这种响应方式，客户端能够知道响应何时结束，即使服务器在开始发送数据之前不知道内容的大小。出现这种情况通常是因为响应是动态的，并且是在请求到来时生成的。当然，像curl这样的客户端会对块进行解码，并且不会向用户显示块大小。

- 与HTTP 1.1服务器通信时，可以让curl在不使用Content-Length：标头的情况下发送请求正文。通过使用分块传输编码，curl将逐块发送POST数据，每个数据块中会包含块的大小。

- HTTP响应消息能够以压缩的格式进行传输。服务器通常会在响应中包含Content-Encoding:gzip标头，以告诉客户端内容经过了压缩。在发送静态资源（提前进行压缩）或CPU资源大于带宽的情况下，使用压缩是很有意义的。

- `--compressed`：让curl请求压缩数据，并在接收gzip（或curl可以理解的任意其他压缩算法）压缩的内容后自动对其进行解压

- 压缩是与传输编码一起使用的一个不太常见的特性。压缩本身是很常见的。随着时间的推移，前面提到的Content-Encoding已经成为HTTP压缩的主要方式。HTTP最初的目的是允许将压缩作为传输编码，而curl也支持这个特性。客户端要求服务器进行压缩传输编码，如果服务器接受了，它将做出响应，并通过一个标头指明它将进行压缩编码，curl将在接收到数据时对其进行解压。

- `--tr-encoding`：-请求服务器进行压缩传输编码

- 可以通过--raw选项要求curl直接传递所收到的数据，不对齐进行解码

### 4.3 HTTP身份验证

- 每个HTTP请求都可以进行身份验证。如果服务器或代理希望用户证明他们具有访问某些URL或执行某些操作的凭证，则可以向客户端发送HTTP响应码，告诉客户端要在请求中提供正确的HTTP身份验证标头。

- 需要身份验证的服务器会发回401响应码和WWW-Authenticate：标头，这个标头列出了服务器支持的所有身份验证方法。

- 需要身份验证的HTTP代理会发回407响应码和Proxy-Authenticate：标头，这个标头列出了代理支持的所有身份验证方法。

- 需要注意的是，现在的大多数网站在登录时都不使用HTTP身份验证，但会要求用户登录网页，浏览器会发出带有用户名和密码等信息的POST请求，然后为会话保持cookie。

- 要让curl发出带有身份验证的HTTP请求，可以用-u或--user选项提供用户名和密码（用冒号分隔）

- curl将使用默认的“Basic” HTTP身份验证方法。这个方法确实非常基础。

- `--basic`：明确指定使用“Basic”HTTP身份验证方法

- 因为Basic身份验证方法通过网络（经过base64编码）发送明文的用户名和密码，所以应该避免在HTTP传输中使用这种方法。

- 当被要求用（指定或隐含的）单个身份验证方法进行HTTP传输时，curl将在第一个请求中插入身份验证标头。

- `--anyauth`：如果要让curl先确认服务器是否真的需要身份验证，可以使用--anyauth选项，它会自动使用curl所知道的最安全的身份验证方法。curl将尝试无须身份验证的请求，然后在必要时使用身份验证：
  
  ```bash
  curl --anyauth --user hello:world http://example.com
  ```

- curl通常（取决于它是如何构建的）也支持其他几种身份验证方法，包括Digest、Negotiate和NTLM。

### 4.4 HTTP区间

- HTTP允许客户端请求特定区间的数据。客户端可以向服务器请求起始偏移量和结束偏移量区间内的数据。它甚至可以在同一个请求中请求几个区间内的数据，并进行组合让这些片段并排发回。服务器发回多个独立的片段作为响应，并用mime边界字符串进行分隔，如何处理它们取决于用户的应用。curl不会进一步对这样的响应进行分隔。

- 不过，获取数据区间也只是对服务器的一种请求。服务器不一定会满足所有这样的请求，在很多情况下，例如服务器动态生成内容时，它将拒绝执行这种区间请求，而是返回完整的内容。

- `-r`或`--range`：让curl发起区间请求。
  
  ```bash
  curl -r 0-199 http://example.com
  curl -r 0-199,1000-1999 http://example.com
  ```

### 4.5 HTTP版本

- 从2016年年中开始，curl默认用HTTP/1.1连接HTTP服务器。如果要连接到HTTPS服务器，并且你的libcurl内置了HTTP/2功能，那么curl将尝试使用HTTP/2，或在协商失败时降级至HTTP/1.1。默认情况下，不支持HTTP/2的curl将使用HTTP/1.1。

- 如果默认值对于你来说还不够好，可以考虑使用CURLOPT_HTTP_VERSION选项直接指定HTTP版本。

### 4.6 HTTP POST

- POST是为将数据发送到Web应用而发明的HTTP方法，也是HTML表单中最常用的方法。它通常会向接收者发送相对少量的数据块。

- 在表单中填充好数据后，浏览器以“URL编码”的形式（以 & 符号分隔的一系列键值对）将数据发送出去

- `-d`或`--data`：发送POST数据
  
  ```bash
  curl -d 'name=admin&num=12' http://example.com
  ```

- 在命令行上指定多个-d选项时，curl会将它们串联起来，并在它们之间插入 & 符号。
  
  ```bash
  curl -d 'name=admin' -d "num=12" http://example.com
  ```

- 如果要发送的数据量不适合作为字符串放在命令行中，还可以从文件中读取
  
  ```bash
  curl -d @filename http://example.com
  ```

- `--data-binary`：用-d选项从文件读取内容时，回车符和换行符将被移除。如果想让curl从文件读取二进制内容，可以使用--data-binary。

- curl的-d选项不会改变数据，也不会对数据进行编码，它只会发送你要它发送的内容。注意-d选项默认使用的Content-Type，因为它可能不是你想要的。

#### 4.6.1 Content-Type

- 用curl的-d选项发送POST请求时，请求中默认会包含一个类似Content-Type:application/x-www-form-urlencoded这样的标头。典型的浏览器在发送POST请求时也会使用这个标头。

- 如果默认标头对你来说还不够好，则可以用其他标头来代替。例如，如果想将JSON POST到服务器，并且准确地告诉服务器你发送的内容是什么类型，可以用-H选项修改标头。

#### 4.6.2 URL编码

- URL编码（也称为百分号编码）是一种编码数据的方式，这种方式允许数据以合法的形式出现在URL中。用application/x-www-form-urlencoded发送POST请求时通常会使用这种编码方式，curl用--data和--data-binary选项发送内容时就是这样。该种编码可以顺利解决汉字、特殊符号等内容的问题

- `--data-urlencode`：为了能够发送未经编码的数据，curl提供了--data-urlencode选项。这个选项提供了几种不同的方式对提供的数据进行URL编码。

- 可以像使用其他--data选项那样使用--data-urlencode data。为了与CGI兼容，data部分应该以名字开头，后面跟上分隔符和内容。可以用以下几种语法将数据传给curl。
  
  - “content”：curl将对内容进行URL编码并发送数据。确保内容中不包含任何=或@符号，否则就会变成下面的其他情况之一！
  
  - “=content”：curl将对内容进行URL编码并发送数据。开头的“=”符号不包含在数据中。
  
  - “name=content”：curl将对内容进行URL编码并发送数据。注意，name部分应该已经经过URL编码。
  
  - “@filename”：curl将从给定文件加载数据（包括换行符），对数据进行URL编码并在POST中发送数据。
  
  -  “name@filename”：curl将从给定文件加载数据（包括换行符），对数据进行URL编码并在POST中发送数据。name部分会附加一个等号，变成name=urlencoded-file-content。注意，name应该已经经过URL编码。

- 示例：
  
  ```bash
  # 会被编码成为name=%E4%BD%A0%20%E5%A5%BD
  curl --dat-urlencode "name=你 好" http://example.com
  # 如果想对name进行URL编码，比如想要发送“user name”字段名，
  # 那么你可以让curl在字段前面添加一个等号来编码整个字符串（等号不会被发送）
  curl --dat-urlencode "=user name=你 好" http://example.com
  ```

#### 4.6.3 转换为GET

- 使用-G或--get选项可以将-d选项指定的数据附加到URL右边，并使用“? ”分隔，然后让curl用GET方法发送数据。使用这个选项可以在POST和GET之间轻松切换。

#### 4.6.5 Expect: 100-continue

- HTTP无法在保持连接的情况下停止正在进行的传输（不管是哪个方向）。因此，如果在传输开始后发现需要停止传输，只有两种方法：关闭当前连接并为下一个请求重新建立连接，或者继续传输，这样会浪费带宽，但进行下一次传输时可以重用连接。当通过HTTP发送大型文件，却发现服务器需要身份验证并发回401响应码时，就会发生这种情况。

- 要想缓解这个问题，可以让curl传递一个额外的标头，即Expect: 100-continue，这让服务器在客户端发送大量数据前有机会拒绝请求。如果POST请求的大小是已知的或者可能比较大，那么curl就会发送这个Expect：标头。PUT请求也是如此。当服务器收到包含100-continue的请求并允许这个请求，它将返回100响应码，客户端就可以继续发送数据。如果服务器不允许这个请求，它会返回错误响应码。遗憾的是，很多服务器对Expect：标头的支持并不好，或者没能正确处理，因此curl只会在收到第一个响应前等待1000毫秒，然后继续发送数据。

- 等待的那1000毫秒可能就被浪费了。你可以从请求中移除Expect:，并通过-H选项来避免等待：
  
  ```bash
  curl -H Expect: -d "hello" http://example.com
  ```

- 在某些情况下，如果要发送的内容非常少（例如低于1千字节）, curl将禁止使用Expect:标头，因为“浪费”这么小的数据块并不是个大问题。

### 4.7 HTTP multipart formpost

- multipart formpost是指HTTP客户端在HTML表单的enctype属性被设置为“multipart/form-data”时所发送的内容。它是一个HTTP POST请求，其中请求正文被格式化为一系列“part”，并以MIME边界字符串为分隔。

- `-F`或`--form`：可以通过该选项添加每个单独的multipart，然后继续为表单的每个字段添加一个-F。
  
  ```bash
  curl -F person=hello -F secret=@file.txt http://example.com
  ```

- 用curl的-F选项进行POST时，请求中会包含默认的Content-Type标头。标头的值是multipart/form-data，后面是MIME边界字符串。multipart formpost使用默认的Content-Type，当然你也可以自行修改，如果你做了修改，curl会自动将边界字符串附加到被替换的标头中。你无法修改边界字符串，因为curl需要用它来生成POST数据。

- -d和-F两个选项都会将指定的数据发送到服务器。不同之处在于如何格式化数据。大多数情况下，接收端会接收特定格式的数据，并期望发送方可以正确格式化数据。客户端无法只使用自己选定的格式。

- 使用浏览器和HTML时，一般会向用户提供一个表单，并在用户填好表单后将数据发送出去。<form>标签用于网页，告诉浏览器如何格式化POST请求。如果<form>标签包含了enctype=multipart/form-data，那就是要浏览器将数据作为multipartformpost发送，这与curl中的-F选项等效。当表单中包含（用于文件上载的）<input type = file>标签时，通常会使用这个方法。

- 表单默认的enctype是application/x-www-form-urlencoded（因为是默认值，所以很少在HTML中显式指定）。浏览器将输入字段“URL编码”成键值对，避免出现不安全的字符。我们通常将其称为常规POST，在curl中可以通过-d选项实现。

- 如果这些服务需要“原始”数据或格式化为JSON的数据，那么可以使用常规的POST方法。

### 4.8 重定向

- `-L`或`--location`：一般来说，curl只会做一些基本的操作，除非你告诉它，否则默认情况下它不会遵循HTTP重定向。可以通过-L或--location选项让它遵循HTTP重定向。

- `--max-redirs`：指定遵循重定向的选项后，curl默认最多会执行50次重定向。设置最大次数限制是为了避免陷入无限循环。如果50次对你来说还不够，可以通过--max-redirs选项修改最大重定向次数。

- 可以通过--post301、--post302和--post303三个选项让curl在收到30x响应后不将非GET请求方法改为GET。如果你在开发基于libcurl的应用，那么可以使用CURLOPT_POSTREDIR选项来控制这种行为。

- 在使用curl时，你可能会为特定网站提供用户名和密码等凭证，但由于HTTP重定向可能会转移到其他主机，curl会对发送给其他主机的内容加以限制。如果你希望凭证也被发送到后面的主机，即使与原始主机不同（这么做大概是因为你信任它们并且知道没有任何害处），则可以使用--location-trusted选项。

### 4.9 修改HTTP请求

#### 4.9.1 请求方法

- `-X`或`--request`：该选项后面跟上方法名，让curl使用其他方法。
  
  ```bash
  curl -X DELETE http://example.com
  ```

- -X选项会修改请求的文本，但不会改变它的行为。知道这一点很重要，如果通过-X选项要求curl发送HEAD请求，HEAD请求将收到与GET响应相同的标头，但不会收到响应正文，即使标头表明可能会有。因此，将-X HEAD添加到命令行将导致curl挂起，一直等待不会到来的响应正文。

- 用curl执行HTTP传输时，它会根据选项选择正确的方法，因此没必要用-X来指定。还需要注意的是，如果用-L选项要求curl遵循重定向，那么使用-X设置的请求方法也会出现在后续的重定向请求中。

#### 4.9.2 自定义标头

- curl设置的所有标头都是可以被覆盖的，如果愿意，还可以替换它们。可以通过-H或--header选项让curl使用新的标头，如果指定的标头字段与内部字段匹配，它将替换内部标头，否则它会将指定的标头添加到标头列表中

- 如果想要删除内部标头，只要向curl提供标头名称但不提供值即可，即冒号右侧没有任何内容。

- 如果真的想要在冒号右侧添加一个没有内容的标头（这种情况很罕见），那么可以在标头后面加个分号，如下所示：
  
  ```bash
  curl -H "Empty; " http://example.com
  ```

#### 4.9.3 referer

- 当用户点击网页上的链接时，浏览器会将用户带到下一个URL，它会在新的请求中添加referer标头，表明请求的来源。虽然referer拼写错误，但它就是这个意思！

- `-e`或`--referer`：设置referer标头

#### 4.9.4 User-Agent

- 客户端可以在请求中设置User-Agent标头，用于告诉服务器它属于哪种用户代理。有时服务器会检查这个标头，并根据标头内容决定如何做出响应。这个标头的默认值为“curl/[version]”, curl 7.54.1的值为User-Agent:curl/7.54.1。

- `-A`或`--user-agnet`：可以用-A或--user-agent选项加上要使用的字符串来设置你喜欢的值。

#### 4.9.5 时间条件

- HTTP支持“条件请求”。条件请求是指包含条件的请求，它请求服务器只在相关条件为true时才返回响应正文。时间是一个有用的条件。

- `--time-cond`：设置时间请求。

- curl可以直接从本地文件中获取时间戳。如果服务器的文件没有发生变更，那么就无须再次下载，如果字符串与时间或日期格式不匹配，curl会检查是否存在同名文件，如果存在，就使用文件的修改时间。
  
  ```bash
  curl --time-cond file http://example.com/file -O
  ```

### 4.10 PUT

- PUT和POST之间的区别很微妙。除了方法名不同之外，其他几乎都是一样的。POST是指将数据传给现有的远程资源，而PUT是指创建新的资源。在这方面，PUT类似于其他协议的文件上传。你可以用PUT上传新的资源，用URL指定远程资源，并指定要发送的本地文件。

- 因为POST和PUT非常相似，所以你也可以用-d加上字符串来发送PUT请求。
  
  ```bash
  curl -d "Hello" -X PUT http://example.com
  ```

### 4.11 cookie

- HTTP cookie是存储在客户端的键值对。在后续请求中，它们被发回服务器，用于保持请求间的状态。HTTP本身是没有状态的，客户端必须在后续请求中重新发送需要服务器知道的所有数据。

- 服务器用Set-Cookie：标头来设置cookie，并在每个cookie中发送一堆额外的属性，客户端在发回cookie时需要匹配这些属性。这些属性包括域名、路径，以及最重要的cookie应该保留多长时间

- cookie的到期时间要么设置为未来的某个固定时刻（或者存活几秒），要么永不过期。没有过期时间的cookie称为“会话cookie”，只在“会话”期间存活。会话通常被认为是浏览器在浏览网站时的生命周期。关闭浏览器后，会话也将结束。

- curl通常只做最少的事情，除非你告诉它，否则默认情况下它不会使用cookie。你需要打开“cookie引擎”才能让curl跟踪它接收到的cookie，然后在后续的请求中发送它们。

- 可以让curl读取或写入cookie，以此来启用cookie引擎。如果让curl从一个不存在的文件中读取cookie，那么它只会打开引擎，但内部的cookie存储是空的。

- `-b`：指定cookie内容
  
  ```bash
  curl -b name=hello http://example.com
  curl -b cookie.txt http://example.com
  ```

- 记住，-b只是从文件中读取cookie。如果服务器更新了响应中的cookie，那么curl会更新内存的cookie存储，并在退出时将它们全部丢弃。如果后续读取相同的输入文件，则会使用与原来相同的cookie。

- curl使用的cookie文件格式叫作Netscape cookie格式，因为浏览器曾经也使用了这种格式，所以curl可以很轻松地处理浏览器cookie！出于便利性方面的考虑，curl还支持一种特殊的cookie文件，文件的内容是一组用于设置cookie的HTTP标头。它是一种次优的格式，但也可能是你唯一可以修改的格式。

- cookie存储有时称为“cookie jar”。当你启用了curl的cookie引擎，并且它接收到了cookie，那么可以让curl在退出之前将所有已知的cookie写入文件（也就是cookie jar）中。curl只会在退出时（而不是生命周期内）更新cookie jar，无论处理输入需要多长时间。

- `-c`：指定cookie-jar文件
  
  ```bash
  curl -c cookie-jar.txt http://example.com
  ```

- -c指示curl将cookie写入文件，-b指示curl从文件读取cookie。通常需要同时使用它们。将cookie写入文件时，curl将保存所有已知的cookie，包括会话cookie（没有过期时间）。curl本身没有会话的概念，它不知道会话什么时候结束，因此不会刷新会话cookie，除非你告诉它要这么做。

- `-j`或`--junk-session-cookies`：为了刷新会话cookie，并向服务器发出开始新会话的信号，curl提供了一个选项，以便用户可以决定新会话何时开始。新的cookie会话意味着所有会话cookie都将被丢弃，这相当于重启浏览器。可以通过-j或--junk-session-cookies选项让curl开始新的cookie会话：

### 4.12 HTTP/2

- curl同时为HTTP:// 和HTTPS:// 类型的URL提供HTTP/2支持（前提是用相关依赖项构建curl）。如果指定了HTTPS URL，那么它会默认使用HTTP/2。如果用curl访问不支持HTTP/2的网站，则发出的请求将进行HTTP/1.1协商。

- 如果使用的是HTTP:// 类型的URL，则需要使用Upgrade：标头升级到HTTP/2，但这可能会导致额外的网络往返。更麻烦的是，相当多的旧服务器遇到这种情况时会返回400响应码。

- 还需要注意的是，一些（可能是大多数）支持HTTP/2的服务器（本身并非都是服务器）无法识别POST请求中的Upgrade：标头。

- 如果你的curl不支持HTTP/2，那么命令行将返回错误。运行curl -V就可以知道你的curl版本是否支持HTTP/2。

- HTTP/2协议的主要特性之一是能够在同一物理连接上复用多个逻辑流。在使用curl命令行工具时，你无法利用这个很酷的特性，因为curl严格按照串行的方式执行网络请求，一个接一个，后一个要在前一个结束后才能开始。
