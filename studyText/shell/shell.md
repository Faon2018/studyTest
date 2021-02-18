# Shell

Shell 是一个用 C 语言编写的程序，它是用户使用 Linux 的桥梁。Shell 既是一种命令语言，又是一种程序设计语言。

Shell 是指一种应用程序，这个应用程序提供了一个界面，用户通过这个界面访问操作系统内核的服务。

在计算机科学中，Shell俗称壳（用来区别于核），是指“为使用者提供操作界面”的[软件](https://baike.baidu.com/item/软件/12053)（命令解析器）。它类似于DOS下的command.com和后来的cmd.exe。它接收用户命令，然后调用相应的[应用程序](https://baike.baidu.com/item/应用程序/5985445)。

脚本种类：

+ Bourne Shell（/usr/bin/sh或/bin/sh）

+ Bourne Again Shell（/bin/bash）

+ C Shell（/usr/bin/csh）

+ K Shell（/usr/bin/ksh）

+ Shell for Root（/sbin/sh）

+ ...

  在一般情况下，不区分 Bourne Shell 和 Bourne Again Shell，所以，像 **#!/bin/sh**，它同样也可以改为 **#!/bin/bash**

## 编写shell脚本

```bash
#!/bin/bash #指定脚本种类
echo "hello world !"
```

## 执行shell脚本

### 运行 Shell 脚本有两种方法：

**1、作为可执行程序**

如下例 1、2

**2、作为解释器参数**

如下例 3、4

### 例子

/home/test.sh

```bash
#!/bin/bash
echo "hello world !"
```

+ 执行的第一种方式（切换到脚本所在目录，使用相对路径）

  ```shell
  cd /home 
  chmod +x ./test.sh #赋予脚本执行的权限
  ./test.sh #执行脚本。 .（点）表示当前目录下
  ```

+ 执行的第二种方式(绝对路径)

  ```bash
  chmod +x /home/test.sh #赋予脚本执行的权限
  /home/test.sh #执行脚本。 .（点）表示当前目录下
  ```

+ 执行的第三种方式（bash命令）

  ```bash
  /bin/bash /home/test.sh
  #不需要赋予脚本执行权限
  ```

+ 执行的第四种方式

  ```bash
  . /home/test.sh 
  #点（空格）相对或绝对路径，也不需要权限
  ```

  

前三种方式都是在当前shell中打开一个子shell来执行脚本内容，当脚本内容结束，则子shell关闭，回到父shell中。

第四种是使脚本内容在当前shell里执行，而不是单独开子shell执行。

开子shell与不开子shell的区别就在于，环境变量的继承关系，如在子shell中设置的当前变量，不做特殊通道处理的话，父shell是不可见的。

而在当前shell中执行的话，则所有设置的环境变量都是直接生效可用的。

**注意**

相对路径时一定要写成 ./test.sh，而不是 **test.sh**，运行其它二进制的程序也一样，直接写 test.sh，linux 系统会去 PATH 里寻找有没有叫 test.sh 的，而只有 /bin, /sbin, /usr/bin，/usr/sbin 等在 PATH 里，你的当前目录通常不在 PATH 里，所以写成 test.sh 是会找不到命令的，要用 ./test.sh 告诉系统说，就在当前目录找。

