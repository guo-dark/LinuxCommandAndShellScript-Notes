# 环境变量


## 什么是环境变量

bash shell用一个叫作环境变量（environment variable）的特性来存储有关shell会话和工作环 境的信息（这也是它们被称作环境变量的原因）。这项特性允许你在内存中存储数据，以便程序 或shell中运行的脚本能够轻松访问到它们。这也是存储持久数据的一种简便方法。

在bash shell中，环境变量分为两类： 

+ 全局变量
+ 局部变量

### 全局环境变量

全局环境变量对于shell会话和所有生成的子shell都是可见的。局部变量则只对创建它们的 shell可见。这让全局环境变量对那些所创建的子shell需要获取父shell信息的程序来说非常有用。 、

系统环境变量基本上都是使用全大写字母，以区别于普通用户的环境变量。 要查看全局变量，可以使用env或printenv命令。 

系统为bash shell设置的全局环境变量数目众多，其中有 很多是在登录过程中设置的，另外，你的登录方式也会影响到所设置的环境变量。 

要显示个别环境变量的值，可以使用printenv命令，但是不要用env命令。 

也可以使用echo显示变量的值。在这种情况下引用某个环境变量的时候，必须在变量前面 加上一个美元符（$）。 
```shell
echo $HOME
```

在echo命令中，在变量名前加上$可不仅仅是要显示变量当前的值。它能够让变量作为命令 行参数。 
```bash
ls $HOME
```

全局环境变量可用于进程的所有子shell。 

### 局部环境变量

局部环境变量只能在定义它们的进程中可见。
Linux系统也默认定义了标准的局部环境变量。

查看局部环境变量的列表有点复杂。遗憾的是，在Linux系统并没有一个只显示局部环境 变量的命令。set命令会显示为某个特定进程设置的所有环境变量，包括局部变量、全局变量 以及用户定义变量.

## 设置用户定义变量

### 设置局部用户定义变量

一旦启动了bash shell（或者执行一个shell脚本），就能创建在这个shell进程内可见的局部变 量了。可以通过等号给环境变量赋值，值可以是数值或字符串。 
![](image/2020-06-05-17-48-25.png)

如果要给变量赋一个含有空格的字符串值，必须用单引号来界定字符串的首和尾。 没有单引号的话，bash shell会以为下一个词是另一个要执行的命令。

> 在涉及用户定义的局部变量 时坚持使用小写字母，这能够避免重新定义系统环境变量可能带来的灾难。 

变量名、等号和值之间没有空格，这一点非常重要。如果在赋值表达式中加上了空格， bash  shell就会把值当成一个单独的命令

设置了局部环境变量后，就能在shell进程的任何地方使用它了。但是，如果生成了另外一个 shell，它在子shell中就不可用。 

在子进程中设置了一个局部变量，那么一旦你退出了子进程，那个局部环境 变量就不可用。

### 设置全局环境变量

在设定全局环境变量的进程所创建的子进程中，该变量都是可见的。

创建全局环境变量的方法是先创建一个局部环境变量，然后再把它导出到全局环境中。 

这个过程通过export命令来完成，变量名前面不需要加$。 

```shell
my_variable="I am Global now" 
export my_variable
```

修改子shell中全局环境变量并不会影响到父shell中该变量的值。 
```bash
$ my_variable="I am Global now" 
$ export my_variable 
$ 
$ echo $my_variable 
I am Global now 
$ 
$ bash 
$ 
$ echo $my_variable I am Global now 
$ 
$ my_variable="Null" 
$ 
$ echo $my_variable Null 
$ 
$ exit 
exit 
$ 
$ echo $my_variable 
I am Global now 
$
```
在定义并导出变量my_variable后，bash命令启动了一个子shell。在这个子shell中能够正 确显示出全局环境变量my_variable的值。子shell随后改变了这个变量的值。但是这种改变仅在子shell中有效，并不会被反映到父shell中。 

子shell甚至无法使用export命令改变父shell中全局环境变量的值。 

## 删除环境变量

可以用unset命令 完成这个操作。在unset命令中引用环境变量时，记住不要使用$。 

```bash
$ echo $my_variable 
I am Global now 
$ 
$ unset my_variable 
$ 
$ echo $my_variable 
 
$
```

> 如果要用到变量，使用$；如果要操作变量，不使用$。这条规则的一 个例外就是使用printenv显示某个变量的值。 

如果你是在子进程中删除了一个全局环境变量， 这只对子进程有效。该全局环境变量在父进程中依然可用。 

和修改变量一样，在子shell中删除全局变量后，你无法将效果反映到父shell中。 

## 默认的shell环境变量

![](image/2020-06-05-18-00-15.png)
![](image/2020-06-05-18-00-35.png)
......

## 设置PATH环境变量

当你在shell命令行界面中输入一个外部命令时，shell必须搜索系统来找到对应 的程序。PATH环境变量定义了用于进行命令和程序查找的目录。

如果命令或者程序的位置没有包括在PATH变量中，那么如果不使用绝对路径的话，shell是没 法找到的。

向环境变量中添加地址
![](image/2020-06-05-18-03-35.png)

> 如果希望子shell也能找到你的程序的位置，一定要记得把修改后的PATH环境变量导出。

对PATH变量的修改只能持续到退出或重启系统。这种效果并不能一直持续。

## 定位系统环境变量

在你登入Linux系统启动一个bash shell时，默认情况下bash会在几个文件中查找命令。这些 文件叫作启动文件或环境文件。bash检查的启动文件取决于你启动bash shell的方式。启动bash shell有3种方式： 

+ 登录时作为默认登录shell 
+ 作为非登录shell的交互式shell 
+ 作为运行脚本的非交互shell 

### 登录shell

登录shell会从5个不同的启动文件里读取命令：

+ /etc/profile 
+ $HOME/.bash_profile 
+ $HOME/.bashrc 
+ $HOME/.bash_login 
+ $HOME/.profile 

/etc/profile文件是系统上默认的bash shell的主启动文件。系统上的每个用户登录时都会执行这个启动文件。 

> 要留意的是有些Linux发行版使用了可拆卸式认证模块（Pluggable Authentication Modules ，PAM）。在这种情况下，PAM文件会在bash shell启动之前处理，这些文件中可 能会包含环境变量。PAM文件包括/etc/environment文件和$HOME/.pam_environment文件。

另外4个启动文件是针对用户的，可根据个人需求定制。

**/etc/profile文件**

/etc/profile文件是bash shell默认的的主启动文件。只要你登录了Linux系统，bash就会执行 /etc/profile启动文件中的命令。

每个发行版的 /etc/profile文件都有不同的设置和命令

在Ubuntu发行版的/etc/profile文件中， 涉及了一个叫作/etc/bash.bashrc的文件。这个文件包含了系统环境变量。 

在CentOS发行版的/etc/profile文件中，并没有出现这个文件。

这两个系统的/etc/profile文件内都用for语句迭代了/etc/profile.d目录下的所有文件。这为Linux系统提供了一个放置特定应用程序启 动文件的地方，当用户登录时，shell会执行这些文件。

/etc/profile.d目录下的有些文件与系统中的特定应用有关。大部分应用都会创建两个启动文件：一个供 bash shell使用（使用.sh扩展名），一个供c shell使用（使用.csh扩展名）。 
lang.csh和lang.sh文件会尝试去判定系统上所采用的默认语言字符集，然后设置对应的LANG 环境变量。 

**$HOME目录下的启动文件**

剩下的启动文件都起着同一个作用：提供一个用户专属的启动文件来定义该用户所用到的环 境变量。大多数Linux发行版只用这四个启动文件中的一到两个

+ $HOME/.bash_profile 
+ $HOME/.bashrc 
+ $HOME/.bash_login 
+ $HOME/.profile

它们位于用户的HOME目录下，所以每个用户都可以编辑这些文件并添加自己的环境变 量，这些环境变量会在每次启动bash shell会话时生效。

> Linux发行版在环境文件方面存在的差异非常大。本节中所列出的$HOME下的那些文件并 非每个用户都有。例如有些用户可能只有一个$HOME/.bash_profile文件。这很正常。 

shell会按照按照下列顺序，运行第一个被找到的文件，余下的则被忽略： 
```bash
$HOME/.bash_profile 
$HOME/.bash_login 
$HOME/.profile 
```

注意，这个列表中并没有$HOME/.bashrc文件。这是因为该文件通常通过其他文件运行的。 

> $HOME表示的是某个用户的主目录。它和波浪号（~）的作用一样。 

再CentOS Linux系统中 .bash_profile启动文件会先去检查HOME目录中是不是还有一个叫.bashrc的启动文件。如果有 的话，会先执行启动文件里面的命令。 

### 交互式shell

如果你的bash shell不是登录系统时启动的（比如是在命令行提示符下敲入bash时启动），那 么你启动的shell叫作交互式shell。交互式shell不会像登录shell一样运行，但它依然提供了命令行 提示符来输入命令。

如果bash是作为交互式shell启动的，它就不会访问/etc/profile文件，只会检查用户HOME目录 中的.bashrc文件。

.bashrc文件有两个作用：一是查看/etc目录下通用的bashrc文件，二是为用户提供一个定制自 己的命令别名和私有脚本函数的地方。 

### 非交互式shell

系统执行shell脚本时用的是非交互式shell。它没有命令行提示符。但是当你在系统上运行脚本时，也许希望能够运行一些特定启动的命令。 

> 脚本能以不同的方式执行。只有其中的某一些方式能够启动子shell。

为了处理这种情况，bash shell提供了BASH_ENV环境变量。当shell启动一个非交互式shell进 程时，它会检查这个环境变量来查看要执行的启动文件。如果有指定的文件，shell会执行该文件 里的命令，这通常包括shell脚本变量设置。 

如果BASH_ENV变量没有设置，子shell可以继承父shell导出过的变量。 
举例来说，如果父shell是登录shell，在/etc/profile、/etc/profile.d/*.sh和$HOME/.bashrc文件中 设置并导出了变量，用于执行脚本的子shell就能够继承这些变量。 (子shell无法继承局部变量。 )
对于那些不启动子shell的脚本，变量已经存在于当前shell中了。所以就算没有设置 BASH_ENV，也可以使用当前shell的局部变量和全局变量。 

### 环境变量持久化

对全局环境变量来说（Linux系统中所有用户都需要使用的变量），可能更倾向于将新的或修 改过的变量设置放在/etc/profile文件中，但这可不是什么好主意。如果你升级了所用的发行版， 这个文件也会跟着更新，那你所有定制过的变量设置可就都没有了。 

最好是在/etc/profile.d目录中创建一个以.sh结尾的文件。把所有新的或修改过的全局环境变 量设置放在这个文件中。 

在大多数发行版中，存储个人用户永久性bash shell变量的地方是$HOME/.bashrc文件。这一 点适用于所有类型的shell进程。但如果设置了BASH_ENV变量，那么记住，除非它指向的是 $HOME/.bashrc，否则你应该将非交互式shell的用户变量放在别的地方。 

> 图形化界面组成部分（如GUI客户端）的环境变量可能需要在另外一些配置文件中设置， 这和设置bash shell环境变量的地方不一样。 

## 数组变量

环境变量有一个很酷的特性就是，它们可作为数组使用。数组是能够存储多个值的变量。这 些值可以单独引用，也可以作为整个数组来引用。 

要给某个环境变量设置多个值，可以把值放在括号里，值与值之间用空格分隔。 
```bash
$ mytest=(one two three four five) 
$
```
要引用一个单独的数组元素，就必须用代表它在数组中位 置的数值索引值。索引值要用方括号括起来。
```bash
$ echo ${mytest[2]} 
three 
$
```

> 环境变量数组的索引值都是从零开始。

要显示整个数组变量，可用星号作为通配符放在索引值的位置。
```bash
$ echo ${mytest[*]} 
one two three four five 
$
```

也可以改变某个索引值位置的值。 
```bash
$ mytest[2]=seven 
$ 
$ echo ${mytest[*]} 
one two seven four five 
$
```

甚至能用unset命令删除数组中的某个值
```bash
$ unset mytest[2] 
$ 
$ echo ${mytest[*]} 
one two four five 
$ 
$ echo ${mytest[2]} 
 
$ echo ${mytest[3]} 
four 
$
```

可以在unset命令后跟上数组名来删除整个数组。 
```bash
$ unset mytest 
$ 
$ echo ${mytest[*]} 
 
$
```

有时数组变量会让事情很麻烦，所以在shell脚本编程时并不常用。对其他shell而言，数组变 量的可移植性并不好，如果需要在不同的shell环境下从事大量的脚本编写工作，这会带来很多不 便。有些bash系统环境变量使用了数组（比如BASH_VERSINFO），但总体上不会太频繁用到。 

