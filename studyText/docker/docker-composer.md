



# docker-composer.yml

Compose 文件是一个定义服务、网络和卷的 YAML 文件

+ build


```yaml
#直接指定路径
version: '3'
services:
  serviceName:
	build: ./dir #指定包含构建上下文路径的字符串
#或者作为具有上下文和可选的Dockerfile 和args的对象
version: '3'
services:
  serviceName:
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

```

+ cap_add、cap_drop、command

```yaml
version: '3'
services:
  serviceName:
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

+ deploy

```yaml
version: '3'
services:
  serviceName:
  	deploy: #指定服务的部署和运行相关的配置
        endpoint_mode: vip #vip:为服务分配一个虚拟ip作为客户端的访问服务的地址,dnsrr:Docker 为该服务设置 DNS 条目, 使得服务名称的 DNS 查询返回一个 IP 地址列表, 客户端直接访问其中的一个地址
        labels: #服务标签
            com.example.description: "This label will appear on the web service"
        mode: global #deploy模式；global:每个群集节点只有一个容器.replicated:指定容器的数量（默认）
        replicas: 6 #指定容器数量，与 mode:replicated 一起使用
        placement: #指定约束和首选项的位置
        resources: #配置资源约束
            limits:               # 设置容器的资源限制
                cpus: "0.5"           # 设置该容器最多只能使用 50% 的 CPU 
                memory: 50M           # 设置该容器最多只能使用 50M 的内存空间 
             reservations:         # 设置为容器预留的系统资源(随时可用)
                cpus: "0.2"           # 为该容器保留 20% 的 CPU
                memory: 20M           # 为该容器保留 20M 的内存空间
        restart_policy: #重启策略配置
            condition: on-failure #重启策略选择；none:不尝试重启；on-failure:只有当容器内部应用程序出现问题才会重启；any:无论如何都会尝试重启(默认)
            delay: 5s #尝试重启的间隔时间(默认为 0s) 
            max_attempts: 3  # 尝试重启次数(默认一直尝试重启)
            window: 120s  # 检查重启是否成功之前的等待时间(即如果容器启动了, 隔多少秒之后去检测容器是否正常, 默认 0s)
        update_config:         # 用于配置滚动更新配置
             parallelism: 2         # 一次性更新的容器数量
             delay: 10s         # 更新一组容器之间的间隔时间
             failure_action: continue        # 定义更新失败的策略;continue:继续更新,rollback:回滚更新,pause:暂停更新(默认)
             monitor:               # 每次更新后的持续时间以监视更新是否失败(单位: ns|us|ms|s|m|h) (默认为0)
             max_failure_ratio:     # 回滚期间容忍的失败率(默认值为0)
             order: stop-first      # v3.4 版本中新增的参数, 回滚期间的操作顺序
                        stop-first            #旧任务在启动新任务之前停止(默认)
                        start-first           #首先启动新任务, 并且正在运行的任务暂时重叠
         rollback_config:       # v3.7 版本中新增的参数, 用于定义在 update_config 更新失败的回滚策略
              parallelism:           # 一次回滚的容器数, 如果设置为0, 则所有容器同时回滚
              delay:                 # 每个组回滚之间的时间间隔(默认为0)
              failure_action: continue        # 定义回滚失败的策略
                        continue              # 继续回滚
                        pause                 # 暂停回滚
               monitor:               # 每次回滚任务后的持续时间以监视失败(单位: ns|us|ms|s|m|h) (默认为0)
               max_failure_ratio:     # 回滚期间容忍的失败率(默认值0)
               order: stop-first       # 回滚期间的操作顺序
                       stop-first       # 旧任务在启动新任务之前停止(默认)
                        start-first     # 首先启动新任务, 并且正在运行的任务暂时重叠

```

+ networks

```yaml
version: '3'
services:


networks:
	netName:
		external: true #如果设置为 true，Docker-compose up 不会尝试创建它，会引用已存在的网络，如果不存在则会引发错误。 false时会创建 
		#or
		external:
			name: netName #引用网络  
		name: mynetName #为此网络设置自定义名称,可包含特殊的字符
		driver: overlay #网络应驱动程序;可选值：BRIDGE、OVERLAY、HOST 、 NONE、自定义驱动程序
		driver_opts: #传递给此网络驱动程序的键-值对
          foo: "1"
          bar: "2"
    	attachable: true #当驱动程序设置为 overlay（覆盖）时才使用。如果设置为 true，那么除了服务之外，独立容器还可以连接到这个网络。
    	internal: true #默认情况下，Docker 还将桥接网络连接到它，以提供外部连接。如果要创建外部隔离覆盖网络，可以将此选项设置为 true。
    	labels: #向容器添加元数据
    		 - "com.example.description=Financial transaction network"
    		 com.example.description: "Financial transaction network"
    	ipam: #自定义 IPAM 配置
          driver: default #自定义 IPAM 驱动程序
          config: #配置块的列表
          	- subnet: 172.28.0.0/16 #网段的 CIDR 格式子网
  
```





## 应用实例

搭建mycat读写分离

```yaml
version: '3'
services:
  mynginx:
    restart: always
    image: nginx:1.18-alpine
    container_name: nginx-compose
    network_mode: "bridge"
  	networks:
      nginx_network: 
      	- mynetalia
    ports:
      - 80:80
networks:
  test:
    external:
      name: nginx_network
```

