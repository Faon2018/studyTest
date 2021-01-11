### 配置文件解释

+ server.xml

  + user

  ```xml
  #定义登录 mycat 的用户和权限
  <user name="test">
      <property name="password">test</property>
      <property name="schemas">TESTDB</property>
      <property name="readOnly">true</property>
      <property name="benchmark">11111</property>
      <property name="usingDecrypt">1</property>
      <privileges check="false">
      <schema name="TESTDB" dml="0010" showTables="custome/mysql">
      <table name="tbl_user" dml="0110"></table>
      <table name="tbl_dynamic" dml="1111"></table>
      </schema>
      </privileges>
  </user>
  #用户名为 test、密码也为 test，可访问的 schema 也只有 TESTDB 一个
  #schemas:可访问的schema,多个用逗号隔开
  #readOnly:是否只是可读
  #benchmark:mycat连接服务降级处理。benchmark 基准, 当前端的整体 connection 数达到基准值是, 对来自该账户的请求开始拒绝连接，0 或不设表示不限制
  #usingDecrypt:密码是否加密，同writeHost标签中的usingDecrypt属性
  ```

  + privileges

    ```xml
    <user name="zhuam">
    <!-- 表级权限: Table 级的 dml(curd)控制，未设置的 Table 继承 schema 的 dml -->
    <!-- TODO: 非 CURD SQL 语句, 透明传递至后端 -->
    <privileges check="true">
        <schema name="TESTDB" dml="0110" >
        <table name="table01" dml="0111"></table>
        <table name="table02" dml="1111"></table>
        </schema>
        <schema name="TESTDB1" dml="0110">
        <table name="table03" dml="1110"></table>
        <table name="table04" dml="1010"></table>
        </schema>
    </privileges>
    </user>
    #Schema/Table 上的 dml 属性说明：表示 insert,update,select,delete 的权限，用4位0或1的数表示
    ```

  + system

    ```xml
    #这个标签内嵌套的所有 property 标签都与系统配置有关
    <system> 
        <!-- chartset 字符集设置,配置字符集的时候一定要坚持 mycat 的字符集与数据库端的字符集是一致的-->
        <property name="charset">utf8</property> 
        <!--指定默认的解析器.可用的取值有：druidparser 和 fdbparser-->
        <property name="defaultSqlParser">druidparser</property>
        <!--指定系统可用的线程数，默认值为机器 CPU 核心线程数。-->
        <property name="processors">4</property>
        <!--指定每次分配 Socket Direct Buffer 的大小，默认是 4096 个字节-->
        <property name="processorBufferChunk">4096</property>
        <!--指定 bufferPool 计算 比例值;默认这个属性的值为： 默认 bufferChunkSize(4096) * processors 属性 * 1000-->
        <property name="processorBufferPool">4096*4*1000</property>
        <!--控制分配这个 ThreadLocalPool 的大小,默认值为 100。-->
        <property name="processorBufferLocalPercent">100</property>
        <!--指定使用 Mycat 全局序列的类型。0 为本地文件方式，1 为数据库方式，2 为时间戳序列方式，3 为分布式ZK ID 生成器，4 为 zk 递增 id 生成-->
        <property name="sequnceHandlerType"></property>
        <!--mysql连接相关-->
        <!-- 指定 Mysql 协议中的报文头长度。默认 4。-->
        <property name="packetHeaderSize">4</property>
        <!-- 指定 Mysql 协议可以携带的数据最大长度。默认 16M。-->
        <property name="maxPacketSize">16M</property>
        <!-- 指定连接的空闲超时时间。某连接在发起空闲检查下，发现距离上次使用超过了空闲时间，那么这个连接会被回收，就是被直接的关闭掉。默认 30 分钟，单位毫秒-->
        <property name="idleTimeout">30*60*1000</property>
        <!--  连接的初始化字符集。默认为 utf8。-->
        <property name="charset">utf8</property>
        <!-- 前端连接的初始化事务隔离级别，只在初始化的时候使用。默认REPEATED_READ，设置值为数字默认 3。-->
        <property name="txIsolation">3</property>
        <!-- SQL 执行超时的时间，Mycat 会检查连接上最后一次执行 SQL 的时间，若超过这个时间则会直接关闭这连接。默认时间为 300 秒，单位秒。-->
        <property name="sqlExecuteTimeout">300</property>
        <!--心跳属性-->
        <!--清理 NIOProcessor 上前后端空闲、超时和关闭连接的间隔时间。默认是 1 秒，单位毫秒。-->
        <property name="processorCheckPeriod">1000</property>
        <!--  对后端连接进行空闲、超时检查的时间间隔，默认是 300 秒，单位毫秒。-->
        <property name="dataNodeIdleCheckPeriod">300000</property>
        <!--  对后端所有读、写库发起心跳的间隔时间，默认是 10 秒，单位毫秒。-->
        <property name="dataNodeHeartbeatPeriod">10000</property>
        <!--服务相关属性-->
        <!--mycat 服务监听的 IP 地址，默认值为 0.0.0.0。-->
        <property name="bindIp">0.0.0.0</property>
        <!--定义 mycat 的使用端口，默认值为 8066。-->
        <property name="serverPort">8066</property>
        <!-- 定义 mycat 的管理端口，默认值为 9066-->
        <property name="managerPort">9066</property>
        <!--mycat 模拟的 mysql 版本号，默认值为 5.6 版本，如非特需，不要修改这个值，目前支持设置 5.5,5.6 版本，其他版本可能会有问题。-->
        <property name="fakeMySQLVersion">5.6</property>
        <!--全局表一致性检测;1为开启全局唯一性检测，0为关闭；-->
        <property name="useGlobleTableCheck">0</property>
        <!-- 分布式事务-->
        <!--分布式事务开关，0 为不过滤分布式事务，1 为过滤分布式事务（如果分布式事务内只涉及全局表，则不过滤），2 为不过滤分布式事务,但是记录分布式事务日志-->
    	<property name="handleDistributedTransactions">0</property>
    	<!-- Off Heap forMycat-->
        <!--使用非堆内存(Direct Memory)处理跨分片结果集的 Merge/order by/group by/limit。1 开启 0 关闭-->
    	<property name="useOffHeapForMerge">1</property>
    </system>
    
    #Mycat 中有两个主要的 buffer 池:
    - BufferPool
    - ThreadLocalPool
    BufferPool 由 ThreadLocalPool 组合而成，每次从 BufferPool 中获取 buffer 都会优先获取ThreadLocalPool 中的 buffer，未命中之后才会去获取 BufferPool 中的 buffer。也就是说 ThreadLocalPool 是作为 BufferPool 的二级缓存，每个线程内部自己使用的。
    bufferPool=默认 bufferChunkSize(4096) * processors 属性 * 1000;
    BufferPool 的总长度 = bufferPool / bufferChunk。
    线程缓存百分比 = bufferLocalPercent / processors 属性。
    ThreadLocalPool 的长度 = 线程缓存百分比 * BufferPool 长度 / 100
    ```

    

+ schema.xml

  + schema

    ```xml
    #schema 标签用于定义 MyCat 实例中的逻辑库，MyCat 可以有多个逻辑库，每个逻辑库都有自己的相关配置。可以使用 schema 标签来划分这些不同的逻辑库。如果不配置 schema 标签，所有的表配置，会属于同一个默认的逻辑库。
    <schema name="TESTDB" checkSQLschema="false" sqlMaxLimit="100">
    </schema>
    # name 逻辑库名称
    # checkSQLschema=true 时，如果我们执行语句 select * from TESTDB.travelrecord;则 MyCat 会把语句修改为select * from travelrecord;。即把表示 schema 的字符去掉（即逻辑库名），避免发送到后端数据库执行时报
    #sqlMaxLimit=100时,每条执行的 SQL 语句，如果没有加上 limit 语句，MyCat 也会自动的加上所对应的值（即加上 limit 100）。
    ```

  + table

    ```xml
    #Table 标签定义了 MyCat 中的逻辑表，所有需要拆分的表都需要在这个标签中定义。
    <schema>
    <table name="travelrecord" dataNode="dn1,dn2,dn3" rule="auto-sharding-long"> </table>
    </schema>
    
    #name: 定义逻辑表的表名,同个 schema 标签中定义的名字必须唯一。一个table标签只能有一个name值，想要书写多个，请重新书写table标签
    #dataNode:逻辑表所属的 dataNode, 该属性的值需要和 dataNode 标签中 name 属性的值相互对应。如果需要定义多个，用逗号隔开即可
    #rule:指定逻辑表要使用的规则名字，规则名字在 rule.xml 中定义，必须与 tableRule 标签中 name 属性属性值一一对应。
    #ruleRequired:是否绑定分片规则，为true时，必须配置rule,否则报错。
    #primaryKey:逻辑表对应真实表的主键，MyCat 会缓存主键与具体 DN 的信息。
    #type:逻辑表的类型。全局表：global。普通表：不指定该值为 globla 的所有表。
    #autoIncrement:指定表是否使用自增主键，默认为false，禁用状态。
    #subTables：分表。 例:subTables=user$1-3,就表示分成user1、user2、user3三个表
    #needAddLimit:是否需要自动的在每个语句后面加上 limit 限制,防止数据过大，操作时间过长，默认true
    #
    ```

  + childTable

    ```xml
    #childTable 标签用于定义 E-R 分片的子表。通过标签上的属性与父表进行关联。
    <schema>
    	<table>
        	 <childTable name="orders" primaryKey="ID" joinKey="customer_id" parentKey="id">
               		<childTable name="order_items" joinKey="order_id"parentKey="id" />
             </childTable>
    		 <childTable name="customer_addr" primaryKey="ID" joinKey="customer_id" parentKey="id" />
        </table>
    </schema> 
    #name：子表的表名
    #joinKey：插入子表的时候会使用这个列的值查找父表存储的数据节点。(子表上的字段)
    #parentKey:与父表建立关联关系的列名（父表上的字段）
    #primaryKey:主键
    #needAddLimit:是否需要添加limit
    ```

  + dataNode

    ```xml
    #dataNode 标签定义了 MyCat 中的数据节点，也就是我们通常说所的数据分片。一个dataNode 标签就是一个独立的数据分片。
    <dataNode name="dn" dataHost="lch3307" database="db1" ></dataNode>
    #使用名字为 lch3307 数据库实例上的 db1 物理数据库，这就组成一个数据分片，最
    后，我们使用名字 dn 标识这个分片
    #name:定义数据节点的名字，这个名字需要是唯一的，我们需要在 table 标签上应用这个名字，来建立表与分片对应的关系
    #dataHost:该属性用于定义该分片属于哪个数据库实例的，属性值是引用 dataHost 标签上定义的 name 属性。
    #database:具体物理数据库
    ```

    

  + dataHost

    ```xml
    #定义了具体的数据库实例、读写分离配置和心跳语句。
    <dataHost name="localhost1" maxCon="1000" minCon="10" balance="0"
    writeType="0" dbType="mysql" dbDriver="native">
    <heartbeat>select user()</heartbeat>#心跳语句
    <!-- can have multi write hosts -->
    <writeHost host="hostM1" url="localhost:3306" user="root"
    password="123456">#写配置
    <!-- can have multi read hosts -->
    <!-- <readHost host="hostS1" url="localhost:3306" user="root" password="123456"
    /> -->#读配置
    </writeHost>
    <!-- <writeHost host="hostM2" url="localhost:3316" user="root" password="123456"/> -->
    </dataHost>
    #name:唯一标识 dataHost 标签，供上层的标签使用。与dataNode标签中的dataHost属性互相引用
    #maxCon:每个读写实例连接池的最大连接。
    #minCon:每个读写实例连接池的最小连接，初始化连接池的大小。
    #balance:负载均衡类型。
    	#1. balance="0", 不开启读写分离机制，所有读操作都发送到当前可用的 writeHost 上。
    	#2. balance="1"，全部的 readHost 与 stand by writeHost 参与 select 语句的负载均衡，简单的说，当双主双从模式(M1->S1，M2->S2，并且 M1 与 M2 互为主备)，正常情况下，M2,S1,S2 都参与 select 语句的负载均衡。
    	#3. balance="2"，所有读操作都随机的在 writeHost、readhost 上分发。
    	#4. balance="3"，所有读请求随机的分发到 wiriterHost 对应的 readhost 执行，writerHost 不负担读压力。
    #writeType:负载均衡类型。1.5后废弃。
    #dbType:指定后端连接的数据库类型，目前支持二进制的 mysql 协议，还有其他使用 JDBC 连接的数据库。例如：mongodb、oracle、spark 等。
    #dbDriver:指定连接后端数据库使用的 Driver。如果使用 JDBC 的话需要将符合 JDBC 4 标准的驱动 JAR 包放到 MYCAT\lib 目录下，并检查驱动 JAR 包中包括如下目录结构的文件：META-INF\services\java.sql.Driver。在这个文件内写上具体的 Driver 类名，例如：com.mysql.jdbc.Driver。
    #switchType:-1 表示不自动切换;1 默认值，自动切换;2 基于 MySQL 主从同步的状态决定是否切换心跳语句为 show slave status;3 基于 MySQL galary cluster 的切换机制（适合集群）（1.4.1）心跳语句为 show status like ‘wsrep%’
    #tempReadHostAvailable:如果配置了这个属性 writeHost 下面的 readHost 仍旧可用，默认 0 可配置（0、1）。
    
    ```

    

  + heartbeat

    ```xml
    <dataHost>
    	<heartbeat>select user()</heartbeat>
    </dataHost>
    这个标签内指明用于和后端数据库进行心跳检查的语句。例如,MYSQL 可以使用 select user()，Oracle 可以使用 select 1 from dual 等。
    这个标签还有一个 connectionInitSql 属性，主要是当使用 Oracla 数据库时，需要执行的初始化 SQL 语句就这个放到这里面来。例如：alter session set nls_date_format='yyyy-mm-dd hh24:mi:ss'
    1.4 主从切换的语句必须是：show slave status
    ```

    

  + writeHost|readHost

    ```xml
    用于实例化后端连接池。唯一不同的是，writeHost 指定写实例、readHost 指定读实例。
    writeHost 指定的后端数据库宕机，那么这个 writeHost 绑定的所有 readHost 都将不可用,并切换到备用的 writeHost 上去。
    <writeHost host="hostM1" url="localhost:3306" user="root"
    password="123456">
    <readHost host="hostS1" url="localhost:3306" user="root" password="123456"/> 
    </writeHost>
    #host:标识不同实例，一般 writeHost 我们使用*M1，readHost 我们用*S1。
    #url:后端实例连接地址，如果是使用 native 的 dbDriver，则一般为 address:port 这种形式。用 JDBC 或其他的dbDriver，则需要特殊指定。当使用 JDBC 时则可以这么写：jdbc:mysql://localhost:3306/。
    #user:后端存储实例需要的用户名字。
    #password:后端存储实例需要的密码。
    #weight:配置在 readhost 中作为读节点的权重。
    #usingDecrypt:是否对密码加密默认 0 否 如需要开启配置 1，同时使用加密程序对密码加密，加密命令为：
    java -cp Mycat-server-1.4.1-dev.jar io.mycat.util.DecryptUtil 1:host:user:password
    Mycat-server-1.4.1-dev.jar 为 mycat download 下载目录的 jar
    1:host:user:password 中 1 为 db 端加密标志，host 为 dataHost 的 host 名称
    ```

+ rule.xml

  ```xml
  #定义表规则，与schema.xml中属性rule对应
  <tableRule name="rule1">
      <!--内嵌的 rule 标签则指定对物理表中的哪一列进行拆分和使用什么路由算法。-->
      <rule>
      <columns>id</columns>
      <algorithm>func1</algorithm>
      </rule>
  </tableRule>
  #name：指定唯一的名字，用于标识不同的表规则。
  #columns :指定要拆分的列名字。
  #algorithm 使用 function 标签中的 name 属性。连接表规则和具体路由算法。
  ```

  + function

    ```xml
    <function name="hash-int"
    class="io.mycat.route.function.PartitionByFileMap">
    <property name="mapFile">partition-hash-int.txt</property>
    </function>
    #name 指定算法的名字。
    #class 制定路由算法具体的类名字。
    #property 为具体算法需要用到的一些属性。
    ```

    + 10.5 Mycat 常用的分片规则