# Dockerfile 编写

Docker build 命令根据Dockerfile和上下文构建一个镜像。构建的上下文是指位置PATH或URL的文件集。PATH是本地文件系统的一个目录。URL是一个Git存储库位置。

构建由 Docker 守护进程运行，而不是由 CLI 运行。构建过程要做的第一件事是将整个上下文(递归地)发送到守护进程。在大多数情况下，最好从一个**空目录**作为上下文开始，**并将 Dockerfile 保存在该目录中**。

+ ARG

  ```sheLL
  #Dockerfile文件
  ARG NGINX_VERSION #等待构建传参，必传
  ARG BUILD_HELLO=hello #不传参时，默认为 hello,有传参会覆盖 hello值
  FROM nginx:${NGINX_VERSION}
  ARG BUILD_HELLO #变量在指令FROM之后被应用时，需要再次声明不带值的空变量申明
  RUN echo "${BUILD_HELLO} ,build success from nginx:${NGINX_VERSION} " #在FROM指令之后被引用
  ```

  ```shell
  #构建命令
  docker build --build-arg NGINX_VERSION=1.18-alpine \--build-arg BUILD_HELLO=hi -t nginx:test .
  
  ##命令解析
   Dockerfile文件声明的NGINX_VERSION将会接受 1.18-alpine
  BUILD_HELLO的值将会被hi覆盖
  
  ```

+ FROM

  ```shell
  FROM [--platform=<platform>] <image>[:<tag>] [AS <name>]
  #--platform 用于指定获取镜像的平台 如：linux/amd64, linux/arm64,  windows/amd64
  # AS name 为获取的镜像命名，可在后续指令中应用
  ```

+ RUN

  RUN 指令将在当前镜像顶部的新层中执行任何命令并提交结果。生成的提交映像将用于 Dockerfile 中的下一步。

+ CMD

  一个 Dockerfile 中只能有一条 CMD 指令。如果您列出了多个 CMD，那么只有最后一个 CMD 才会生效。

+ LABEL

  ```shell
  LABEL multi.label1="value1" multi.label2="value2" other="value3"
  #指定标签
  docker image inspect --format='' myimage
  #结果
  {
    "com.example.vendor": "ACME Incorporated",
    "com.example.label-with-value": "foo",
    "version": "1.0",
    "description": "This text illustrates that label-values can span multiple lines.",
    "multi.label1": "value1",
    "multi.label2": "value2",
    "other": "value3"
  }
  ```



+ ~~MAINTAINER~~

  MAINTAINER 指令设置生成的图像的 Author 字段,可用label替代。

+ EXPOSE 

  暴露容器监听的端口,会被镜像运行时同过-p指定的端口覆盖

  ```shell
  EXPOSE 80/tcp
  EXPOSE 80/udp
  #镜像运行覆盖端口
  docker run -p 80:8080/tcp 
  ```




+ ENV

  ```shell
  ENV <key>=<value> ...
  #多个值可以空格隔开，也可以，value中的空格 用\加空格 表说，或将整个值用引号包裹
  ENV MY_NAME="John Doe"
  ENV MY_DOG=Rex\ The\ Dog
  ENV MY_CAT=fluffy
  ENV MY_NAME="John Doe" MY_DOG=Rex\ The\ Dog MY_CAT=fluffy
  
  #ENV 指令还允许使用另一种语法 ENV < key > < value > ，省略了 = 。
  ENV MY_VAR my-value #但只能设置一个值
  ```

  ENV指定的环境变量可用于 运行镜像时设定 

  ```shell
  docker run --env MY_NAME="jonh doe" \--env MY_DOG="jogn doe"
  ```


+ ADD

  ADD 指令从 < src > 复制新的文件、目录或远程文件 url，并将它们添加到位于 < dest > 路径的镜像文件系统中。

  ```shell
  #将text.txt添加到<WORKDIR>/relativeDir/  (相对路径)
  ADD test.txt relationDir/
  #将“ test.txt”添加到/absoluteDir/ （绝对路径）
  ADD test.txt /absoluteDir/
  ```

  

+ COPY

  COPY 指令从 < src > 复制新的文件或目录，并将它们添加到位于路径 < dest > 的容器的文件系统中。

  同 ADD指令

+ VOLUME

  挂载卷

+ USER

  USER 指令设置运行映像时要使用的用户名(或 UID)和可选的用户组(或 GID) ，以及 Dockerfile 中跟随它的任何 RUN、 CMD 和 ENTRYPOINT 指令

+ WORKDIR

  指令为任何在 Dockerfile 中跟随它的 RUN、 CMD、 ENTRYPOINT、 COPY 和 ADD 指令设置工作目录。如果 WORKDIR 不存在，即使它不在任何后续的 Dockerfile 指令中使用，它也会被创建。













# .dockerignore 编写

```shell
# comment  //这是注释
*/temp*  //忽略在根目录的任何直接子目录中，名称以temp开头的文件和目录。如 /somedir/temporary.txt 或 /somedir/temporary
*/*/temp*  //忽略位于根目录下面两个级别的子目录中，名称以temp开头的文件和目录。如：/somedir/subdir/temporary.txt 或 /somedir/subdir/temporary
tem? //排除根目录中名称为temp的单字符扩展名的文件和目录(根目录下名字以temp开头，后面为任意一个字符的文件或目录)。如目录/tempa和/tempb都会被排除

*.md
!README.md  //排除 除README.md 外所有的md类型的文件
```



