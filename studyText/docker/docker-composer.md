# docker-composer.yml

Compose 文件是一个定义服务、网络和卷的 YAML 文件

+ build

  1.直接指定路径

```yaml
build: ./dir #指定包含构建上下文路径的字符串
```

2. 作为具有上下文和可选的Dockerfile 和args的对象

```yaml
build:
	context: ./dir #可以是包含 Dockerfile 的目录的路径，也可以是 git 存储库的 url。
	dockerfile: Dockerfile-alternate #Dockerfile 文件名 默认Dockerfile
	args: #添加生成参数，用于赋值 Dockerfile文件中定义的ARG参数
		buildno: 1  #第一种写法
		- gitcommithash=cdc3b19 #第二种写法
		- test #当省略参数值时，表示它的构建值是 Composer 运行环境中的值
		-is="true" #YAML 布尔值(“ true”、“ false”、“ yes”、“ no”、“ on”、“ off”)必须用引号括起来，以便解析器将其解释为字符串。
	cache_from: #指定缓存的镜像列表
		- alpine:latest 
	labels: #使用标签生成镜像的元数据
	 	com.example.description: "Accounting webapp" #建议反向dns表示
    	- "com.example.department=Finance"
    netwprk: host #设置生成期间 RUN 指令连接到的网络容器。 none 表示禁用网络
	shm_size: '2gb' (10000000)	#设置/dev/shm 分区的大小。指定为表示字节数的整数值或表示字节值的字符串。
    target: prod #按照 Dockerfile 中的定义构建指定的阶段。同docker build --target
cap_add: #添加容器功能
    - ALL
cap_drop: #删除容器功能
  	- NET_ADMIN
  	- SYS_ADMIN
cgroup_parent: m-executor-abcd #为容器指定一个可选的父cgroup
command: bundle exec thin -p 3000 #重写默认命令，命令也可以是一个列表，类似于 dockerfile: ["bundle", "exec", "thin", "-p", "3000"]
configs: 
container_name: my_container_name #容器名称
credential_spec: #证书规格
  file: my-credential-spec.json
depends_on: #服务依赖，依赖会比服务先启动
	- db
	- redis
```

