## bat批处理教程

------

### 1.批处理简介
批处理，也称为批处理脚本，英文译为BATCH，批处理文件后缀BAT就取的前三个字母。它的构成没有固定格式，只要遵守以下这条就ok了：
+ 每一行可视为一个命令，每个命令里可以含多条子命令，从第一行开始执行，直到最后一行结束，它运行的平台是DOS。
+ 批处理有一个很鲜明的特点：使用方便、灵活，功能强大，自动化程度高。

栗子1：

将下面的几行命令保存为name.bat然后执行（以后文中只给出代码，保存和执行方式类似）：

```
ping sz.tencent.com > a.txt
ping sz1.tencent.com >> a.txt
ping sz2.tencent.com >> a.txt
ping sz3.tencent.com >> a.txt
ping sz4.tencent.com >> a.txt
ping sz5.tencent.com >> a.txt
ping sz6.tencent.com >> a.txt
ping sz7.tencent.com >> a.txt
exit
```

栗子2：

```
@echo off
if exist C:\Progra~1\Tencent\AD.gif del C:\Progra~1\Tencent\AD.gif
a.bat
```

栗子3：使用批处理脚本查是否中冰河。脚本内容如下：

```
@echo off
netstat -a -n > a.txt
type a.txt | find "7626" && echo "Congratulations! You have infected GLACIER!"
del a.txt
pause & exit
```
这里利用了netstat命令，检查所有的网络端口状态，只需要你清楚常见木马所使用的端口，就能很easy的判断出来是否被人种了冰河。然这不是确定的，因为冰河默认的端口7626，完全可以被人修改。这里介绍的只是方法和思路。这里介绍的是方法和思路稍做改动，就变成可以检查其他木马的脚本了，再改动一下，加进去参数和端口及信息列表文件后，就变成自动检测所有木马的脚本了。呵呵，是不是很过瘾？脚本中还利用了组合命令&&和管道命令|，后面将详细介绍。

栗子4：借批处理自动清除系统垃圾，脚本如下：
```
@echo off
if exist c:\windows\temp. del c:\windows\temp\.
if exist c:\windows\Tempor~1. del c:\windows\Tempor~1\.
if exist c:\windows\History. del c:\windows\History\.
if exist c:\windows\recent. del c:\windows\recent\.
```
将以上脚本内容保存到autoexec.bat里，每次开机时就把系统垃圾给自动删除了。这里需要注意两点：一、DOS不支持长文件名，所以就出现了Tempor~1这个东东；二、可根据自己的实际情况进行改动，使其符合自己的要求。


### 2.批处理常用的命令

很多常见DOS命令在批处理脚本中有这广泛的应用，它们是批处理脚本的BODY部分，但批处理比DOS更灵活多样，更具备自动化。要学好批处理，DOS一定要有比较扎实的基础。这里只讲述一些比较少用（相对来说）的DOS命令，常用命令如COPY、DIR等就不做介绍了

栗子5：
```
@echo off

::close echo

cls

::clean screen

echo This programme is to make the MASM programme automate

::display info

echo Edit by CODERED

::display info

echo Mailto me : qqkiller@sina.com

::display info

if "%1"=="" goto usage

::if input without paramater goto usage

if "%1"=="/?" goto usage

::if paramater is "/?" goto usage

if "%1"=="help" goto usage

::if paramater is "help" goto usage

pause

::pause to see usage

masm %1.asm

::assemble the .asm code

if errorlevel 1 pause & edit %1.asm

::if error pause to see error msg and edit the code

link %1.obj & %1

::else link the .obj file and execute the .exe file

:usage

::set usage

echo Usage: This BAT file name [asm file name]

echo Default BAT file name is START.BAT

::display usage
```

在这个脚本中出现了如下几个命令：@、echo、::、pause、:和goto、%以及if。而这一章就将讲述这几个命令。

1.@

行首有了它的话，这一行的命令就不显示了。在上栗中，首行的@echo off中，@的作用就是让脚本在执行时不显示后面的echo off部分

2.echo

中文为“反馈”、“回显”的意思。它其实是一个开关命令，就是说它只有两种状态：打开和关闭。于是就有了echo on和echo off两个命令了。直接执行echo命令将显示当前echo命令状态（off或on）执行echo off将关闭回显，它后面的所有命令都不显示命令本身，只显示执行后的结果，除非执行echo on命令。在例五中，首行的@命令和echo off命令联合起来，达到了两个目的：不显示echo off命令本身，不显示以后各行中的命令本身。的确是有点乱，但你要是练习一下的话，3分钟包会，不会的退钱！

+ echo命令的另一种用法一：可以用它来显示信息！如例五中倒数第二行，Default BAT file name is START.BAT将在脚本执行后的窗口中显示，而echo命令本身不显示。

+ echo命令的另一种用法二：可以直接编辑文本文件。栗6：
```
echo nbtstat -A 192.168.0.1 > a.bat
echo nbtstat -A 192.168.0.2 >> a.bat
echo nbtstat -A 192.168.0.3 >> a.bat
```
以上脚本内容的编辑方法是，直接是命令行输入，每行一回车。最后就会在当前目录下生成一个a.bat的文件，直接执行就会得到结果。

3.::

这个命令的作用很简单，它是注释命令，在批处理脚本中和rem命令等效。它后面的内容在执行时不显示，也不起任何作用，因为它只是注释，只是增加了脚本的可读性，和C语言中的/…………/类似。

4.pause

它的作用，是让当前程序进程暂停一下，并显示一行信息：请按任意键继续. . .。在例五中这个命令运用了两次，第一次的作用是让使用者看清楚程序信息，第二个是显示错误的汇编代码信息（其实不是它想显示，而是masm程序在显示错误信息时被暂它停了，以便让你看清楚你的源代码错在哪里）。

5.:和goto

goto是个跳转命令，:是一个标签。当程序运行到goto时，将自动跳转到:定义的部分去执行了.栗5中倒数第5行行首出现一个:，则程序在运行到goto时就自动跳转到:标签定义的部分执行，结果是显示脚本usage（usage就是标签名称）。不难看出，goto命令就是根据这个冒号和标签名称来寻找它该跳转的地方，它们是一一对应的关系。goto命令也经常和if命令结合使用。至于这两个命令具体用法，参照栗5。


6.%

这个百分号严格来说是算不上命令的，它只是批处理中的参数而已


### 3.批处理脚本中最重要的几个命令

7.if

if命令是一个表示判断的命令，根据得出的每一个结果，它都可以对应一个相应的操作。关于它的三种用法，在这里分开讲。

(1)、输入判断。还是用栗5里面的那几句：

```
if "%1"=="" goto usage
::%1表示第一个参数
if "%1"=="/?" goto usage
if "%1"=="help" goto usage
```

(2)、存在判断。再看栗2里这句：

```
if exist C:\Progra~1\Tencent\AD.gif del C:\Progra~1\Tencent\AD.gif
if not exist C:\Progra~1\Tencent\AD.gif exit
::如果不存在那些gif文件则退出脚本”
```

(3)、结果判断

```
masm %1.asm
if errorlevel 1 pause & edit %1.asm
link %1.obj
```
先对源代码进行汇编，如果失败则暂停显示错误信息，并在按任意键后自动进入编辑界面；否则用link程序连接生成的obj文件。

8.call

call命令用来从一个批处理脚本中调用另一个批处理脚本

```
start.bat：
……
CALL 10.BAT 0
……

10.bat：
……
ECHO %IPA%.%1 >HFIND.TMP
……
CALL ipc.bat IPCFind.txt

ipc.bat：
for /f "tokens=1,2,3 delims= " %%i in (%1) do call HACK.bat %%i %%j %%k
```

9.find

这是一个搜索命令，用来在文件中搜索特定字符串,至于find命令的其他几个参数如v、n、i等，有兴趣的朋友自己去研究吧，这已经属于DOS学习的内容了，这里就不做介绍

10.for、set、shift

Windows bat脚本的for语句基本形态如下：

+ 在cmd窗口中：for %I in (command1) do command2 
+ 在批处理文件中：for %%I in (command1) do command2

之所以要区分cmd窗口和批处理文件两种环境，是因为在这两种环境下，命令语句表现出来的行为虽然基本一样，但是在细节上还是稍有不同，最明显的一个差异就是：在cmd窗口中，for之后的形式变量I必须使用单百分号引用，即%I；而在批处理文件中，引用形式变量I必须使用双百分号，即%%I。为了方便起见，若不是特别强调，以下的讲解都以批处理文件环境为例。
我们先来看一下for语句的基本要素都有些什么：

1. for、in和do是for语句的关键字，它们三个缺一不可；
2. %%I是for语句中对形式变量的引用，即使变量l在do后的语句中没有参与语句的执行，也是必须出现的；
3. in之后，do之前的括号不能省略；
4. command1表示字符串或变量，command2表示字符串、变量或命令语句；
 
下面来看一个Windows bat脚本的demo(记为demo1)：
```
@echo off
for  %%I in (ABC) do echo %%I
pause
```
对批处理文件的for循环就是这样简单，下面来看看for语句的注意事项，并运行更复杂的for循环实例。

1.for语句的形式变量I，可以换成26个字母中的任意一个，这些字母会区分大小写，也就是说，%%I和%%i会被认为不是同一个变量；形式变量I还可以换成其他的字符，但是，为了不与批处理中的%0～%9这10个形式变量发生冲突，请不要随意把%%I替换为%%0～%%9中的任意一个；

2.in和do之间的command1表示的字符串或变量可以是一个，也可以是多个，每一个字符串或变量，我们称之为一个元素，每个元素之间，用空格键、跳格键、逗号、分号或等号分隔；

3.for语句依次提取command1中的每一个元素，把它的值赋予形式变量I，带到do后的command2中参与命令的执行；并且每次只提取一个元素，然后执行一次do后的命令语句，而无论这个元素是否被带到command2中参与了command2的运行；当执行完一次do后的语句之后，再提取command1中的下一个元素，再执行一次command2，如此循环，直到command1中的所有元素都已经被提取完毕，该for语句才宣告执行结束。

有了以上的基础，我们再来看下面这个例子，这个例子修改了demo1中的部分内容(记为demo2)，结果将大不一样：

```
@echo off
for  %%I in (A,B,C) do echo %%I
pause
```

 高级用法：

1)搜索当前目录下有哪些文件？

```
@echo off
for %%i in (*.*) do echo "%%i"
pause
```
2)搜索当前目录下所有的文本文件？
```
@echo off
for %%i in (*.txt) do echo "%%i"
pause
```
------
set 功能一览 
```
[设置变量] 
格式：set 变量名=变量值 
详细：被设定的变量以%变量名%引用

[取消变量] 
格式：set 变量名= 
详细：取消后的变量若被引用%变量名%将为空

[展示变量] 
格式：set 变量名 
详细：展示以变量名开头的所有变量的值

[列出所有可用的变量] 
格式：set

[计算器] 
格式：set /a 表达式 
示例：set /a 1+2*3 输出 7

[设置变量为表达式计算后的值] 
set a=1+2

echo %a% ==>输出1+2
set /a a=1+2 
echo %a% ==>输出3

[设置变量为用户输入的值] 
set /p a=输入一个值 ==>输出 输入一个值 
echo %a% ==>显示用户输入的值

!注意! 
set不能用在复合语句里面比如if 1==1 set a=2或者for %%i in (a) do set a=2

预定义的变量

下面是些已经被底层定义好可以直接使用的变量：不会出现在 SET 显示的变量列表中 
%CD% - 扩展到当前目录字符串。 
%DATE% - 用跟 DATE 命令同样的格式扩展到当前日期。 
%TIME% - 用跟 TIME 命令同样的格式扩展到当前时间。 
%RANDOM% - 扩展到 0 和 32767 之间的任意十进制数字。 
%ERRORLEVEL% - 扩展到当前 ERRORLEVEL 数值。 
%CMDEXTVERSION% - 扩展到当前命令处理器扩展名版本号。 
%CMDCMDLINE% - 扩展到调用命令处理器的原始命令行。 
%0 bat的完整路径名如”C:\Windows\system32\xxx.bat” 
%1 bat参数1依次类推%2参数2… 
%path% - 当前的环境变量。以分号隔开的路径列表，路径可包含空格，可以以’\’结尾, 可以以双引号包围之。

扩展变量

@ 与%i相关的变量（bat参数或者for循环的%i) 
假设文件为C:\Documents and Settings\jinsun\桌面\ParseSinglePkgs.bat 
%0 C:\Documents and Settings\jinsun\桌面\ParseSinglePkgs.bat 
%~dp0 C:\Documents and Settings\jinsun\桌面\ 
%cd% C:\Documents and Settings\jinsun\桌面 
%~nx0 ParseSinglePkgs.bat 
%~n0 ParseSinglePkgs 
%~x0 .bat

@ 与%VAR%相关的变量 
%VAR:str1=str2% 会将VAR中的str1替换为str2(str2如果为空则可以达到删除的效果,str1前可以加*，变量%ABC:*B=%是C) 
%VAR:~0,-2% 会提取VAR 变量的所有字符，除了最后两个 
%VAR:~2% 会提取VAR 变量的除前两个的所有字符 
%VAR:~-2% 会提取VAR 变量的最后两个 
%VAR:~2,5% 提取从第2个字符开始的5个字符
```
------

批处理文件中可引用的参数为%0~%9， %0是指批处理文件的本身，也可以说是一个外部命令；%1~%9是批处理参数，也称形参；而替换形参的实参若超过了批处理文件中所规定数值（9个）且想在批处理文件中应用这些实参的话，shift命令可以帮你实现！ 


Shift 命令：更改批处理文件中可替换参数的位置 
```
shift [/n]   
```

n 的取值是[0,8],且为整数；[/n]为可选参数，当赋予n某个值时，就意味着命令从第n个参数开始移位；当n赋予的值为0,1或不带有任何命令选项的 shift时，则表示批处理文件中替换参数左移一个位置，后面的替换参数陆续填补上去，直至可替换参数为空。 

eg: 创建批处理文件d:\test.bat; 

```
@echo off    
    cls    
    echo   %1 %2 %3 %4 %5 %6 %7 %8 %9    
    shift /0    
    echo   %1 %2 %3 %4 %5 %6 %7 %8 %9    
    shift /1    
    echo   %1 %2 %3 %4 %5 %6 %7 %8 %9    
    shift /2    
    echo   %1 %2 %3 %4 %5 %6 %7 %8 %9    
    shift /3    
    echo   %1 %2 %3 %4 %5 %6 %7 %8 %9    
    shift /4    
    echo   %1 %2 %3 %4 %5 %6 %7 %8 %9    
    shift /5    
    echo   %1 %2 %3 %4 %5 %6 %7 %8 %9    
    shift /6    
    echo   %1 %2 %3 %4 %5 %6 %7 %8 %9    
    shift /7    
    echo   %1 %2 %3 %4 %5 %6 %7 %8 %9    
    shift /8    
    echo   %1 %2 %3 %4 %5 %6 %7 %8 %9    
    pause 1>nul   
```

然后在命令提示符d:\下输入test 1 2 3 4 ......17显示执行结果，就知晓其中的规律啦！
```
    D:\>test 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16 17    
       
    1 2 3 4 5 6 7 8 9    
    2 3 4 5 6 7 8 9 10    
    3 4 5 6 7 8 9 10 11    
    3 5 6 7 8 9 10 11 12    
    3 5 7 8 9 10 11 12 13    
    3 5 7 9 10 11 12 13 14    
    3 5 7 9 11 12 13 14 15    
    3 5 7 9 11 13 14 15 16    
    3 5 7 9 11 13 15 16 17    
    3 5 7 9 11 13 15 17
```



11.|

管道命令,作用，就是让前一命令的输出当做后一命令的输入。

12.>、>>

这两个命令的效果从本质上来说都是一样的，他们都是输出重定向命令，说的通俗一点，就是把前面命令的输出写入到一个文件中。这两个命令的唯一区别是，>会清除掉原有文件中的内容后把新的内容写入原文件，而>>只会另起一行追加新的内容到原文件中，而不会改动其中的原有内容

```
echo @echo off > a.bat
echo echo This is a pipeline command example. >> a.bat
echo echo It is very easy? >> a.bat
echo echo Believe your self! >> a.bat
echo pause >> a.bat
echo exit >> a.bat
```

13.<、>&、<&

这三个命令也是管道命令，但它们一般不常用，你只需要知道一下就ok了，当然如果想仔细研究的话，可以自己查一下资料。

+ <，输入重定向命令，从文件中读入命令输入，而不是从键盘中读入。

+ >&，将一个句柄的输出写入到另一个句柄的输入中。

+ <&，刚好和>&相反，从一个句柄读取输入并将其写入到另一个句柄输出中。

14.&

这可以说是最简单的一个组合命令了，它的作用是用来连接n个DOS命令，并把这些命令按顺序执行，而不管是否有命令执行失败

```
copy a.txt b.txt /y & del a.txt
```

15.&&

与&命令不同之处在于，它在从前往后依次执行被它连接的几个命令时会自动判断是否有某个命令执行出错，一旦发现出错后将不继续执行后面剩下的命令

```
dir c:\ > a.txt && dir d:\ >> a.txt
```

16.||

这个命令的用法和&&几乎一样，但作用刚好和它相反：利用这种方法在执行多条命令时，当遇到一个执行正确的命令就退出此命令组合，不再继续执行下面的命令。题目：查看当前目录下是否有以s开头的exe文件，如果有则退出

```
@echo off
dir s.exe || exit
```





