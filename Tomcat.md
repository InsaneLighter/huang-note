# Tomcat

## 常用配置详解

- 目录结构

   ```shell
  /bin：脚本文件目录。
  /common/lib：存放所有web项目都可以访问的公共jar包（使用Common类加载器加载）。
  /conf：存放配置文件，最重要的是server.xml。
  /logs：存放日志文件。
  /server/webapps：来管理Tomcat-web服务用的。仅对TOMCAT可见，对所有的WEB APP都不可见（使用Catalina类加载器加载）。
  /shared/lib：仅对所有WEB APP可见，对TOMCAT不可见（使用Shared类加载器加载）。
  /temp：Tomcat运行时候存放临时文件用的。
  /webapps：web应用发布目录。
  /work：Tomcat把各种由jsp生成的servlet文件放在这个目录下。删除后，启动时会自动创建。
  ```

- 配置文件

  ```shell
  server.xml：主要的配置文件。
  web.xml：缺省的web app配置，WEB-INF/web.xml会覆盖该配置。
  context.xml：不清楚跟server.xml里面的context是否有关系。
     
  server.xml配置
     server标签
         port：指定一个端口，这个端口负责监听关闭tomcat的请求。
         shutdown：指定向端口发送的命令字符串。
  
     service标签
         name：指定service的名字。
  
     Connector(表示客户端和service之间的连接)标签
          port：指定服务器端要创建的端口号，并在这个端口监听来自客户端的请求。
          minProcessors：服务器启动时创建的处理请求的线程数。
          maxProcessors：最大可以创建的处理请求的线程数。
          enableLookups：如果为true，则可以通过调用request.getRemoteHost()进行DNS查询来得到远程客户端的实际主机名，若为false则不进行DNS查询，而					   是返回其ip地址。
          redirectPort：指定服务器正在处理http请求时收到了一个SSL传输请求后重定向的端口号。
          acceptCount：指定当所有可以使用的处理请求的线程数都被使用时，可以放到处理队列中的请求数，超过这个数的请求将不予处理。
          connectionTimeout：指定超时的时间数(以毫秒为单位)。
   
     Engine(表示指定service中的请求处理机，接收和处理来自Connector的请求)标签
          defaultHost：指定缺省的处理请求的主机名，它至少与其中的一个host元素的name属性值是一样的。
   
     Context(表示一个web应用程序，通常为WAR文件，关于WAR的具体信息见servlet规范)标签
           docBase：该web应用的文档基准目录（Document Base，也称为Context Root），或者是WAR文件的路径。可以使用绝对路径，也可以使用相对于context					所属的Host的appBase路径。
           path：表示此web应用程序的url的前缀，这样请求的url为http://localhost:8080/path/****。
           reloadable：如果为true，则tomcat会自动检测应用程序的/WEB-INF/lib和/WEB-INF/classes目录的变化，自动装载新的应用程序，我们可以在不重起					    tomcat的情况下改变应用程序。
           useNaming：如果希望Catalina为该web应用使能一个JNDI InitialContext对象，设为true。该InitialialContext符合J2EE平台的约定，缺省值为					 true。
           workDir：Context提供的临时目录的路径，用于servlet的临时读/写。利用javax.servlet.context.tempdir属性，servlet可以访问该目录。如果没					 有指定，使用$CATALINA_HOME/work下一个合适的目录。
           swallowOutput：如果该值为true，System.out和System.err的输出被重定向到web应用的logger。如果没有指定，缺省值为false
           debug：与这个Engine关联的Logger记录的调试信息的详细程度。数字越大，输出越详细。如果没有指定，缺省为0。
   
     host(表示一个虚拟主机)标签
           name：指定主机名。
           appBase：应用程序基本目录，即存放应用程序的目录。
           unpackWARs：如果为true，则tomcat会自动将WAR文件解压，否则不解压，直接从WAR文件中运行应用程序。
  
     Logger(表示日志，调试和错误信息)标签
           className：指定logger使用的类名，此类必须实现org.apache.catalina.Logger接口。
           prefix：指定log文件的前缀。
           suffix：指定log文件的后缀。
           timestamp：如果为true，则log文件名中要加入时间，如下例:localhost_log.2001-10-04.txt。
   
     Realm(表示存放用户名，密码及role的数据库)标签
           className：指定Realm使用的类名，此类必须实现org.apache.catalina.Realm接口。
  
     Valve(功能与Logger差不多，其prefix和suffix属性解释和Logger  中的一样)标签
           className：指定Valve使用的类名，如用org.apache.catalina.valves.AccessLogValve类可以记录应用程序的访问信息。
           directory：指定log文件存放的位置。
           pattern：有两个值，common方式记录远程主机名或ip地址，用户名，日期，第一行请求的字符串，HTTP响应代码，发送的字节数。combined方式比common方式记录的值更多。
  ```

  

- 配置虚拟目录

  - 直接部署到webapps目录下面访问。
  - 修改conf/server.xml文件。在<Host name="localhost" appBase="webapps" unpackWARs="true" xmlValidation="false" xmlNamespaceAware="false"></host>中加入<Context path="/test" docBase="webdemo" debug="0" reloadable="true" />。docBase目录默认使用appBase="webapps"这个目录。也可以是绝对路径。配置主目录，可以将path=""。
  - 当项目没有放在webapps目录下时，可以在conf/Catalina/localhost新建一个XXX.XML文件。里面加入<Context docBase="E:webdemo" debug="0" reloadable="true" />。
    - path属性不需要设置，设置了也不会起作用的。
    - 可以使用该方法建立主目录指向另一个目录，例如：<Context docBase="E:webdemo" debug="0" reloadable="true" />命名为ROOT.xml，这样默认访问的主目录就被修改过了。

- 配置连接数

  ```shell
   maxThreads：Tomcat使用线程来处理接收的每个请求。这个值表示Tomcat可创建的最大的线程数。
   acceptCount：指定当所有可以使用的处理请求的线程数都被使用时，可以放到处理队列中的请求数，超过这个数的请求将不予处理。
   minSpareThreads：Tomcat初始化时创建的线程数。
   maxSpareThreads：一旦创建的线程超过这个值，Tomcat就会关闭不再需要的socket线程。
   enableLookups：是否反查域名，取值为：true或false。为了提高处理能力，应设置为false
   connectionTimeout：网络连接超时，单位：毫秒。设置为0表示永不超时，这样设置有隐患的。默认可设置为20000毫秒。 
   web server允许的最大连接数还受制于操作系统的内核参数设置，通常Windows是2000个左右，Linux是1000个左右。
  ```

  

- 配置内存大小

  ```shell
   修改bin/catalina.bat中的set CATALINA_OPTS=-Xms64m -Xmx128m。
   Xms指最小内存，Xmx指最大内存。
  ```

  

- 安全配置
  
- 将<Server port="8005" shutdown="SHUTDOWN">SHUTDOWN修改为其他一些字符串。否则就容易被人给停止掉了。
   - 对应tomcat3.1中，屏蔽目录文件自动列出
      修改conf/web.xml中的
       <servlet>
          <servlet-name>default</servlet-name>
          <servlet-class>org.apache.catalina.servlets.DefaultServlet</servlet-class>
          <init-param>
            <param-name>debug</param-name>
            <param-value>0</param-value>
          </init-param>
          <init-param>
            <param-name>listings</param-name>
            <param-value>true</param-value><!-- 改成false -->
          </init-param>
          <load-on-startup>1</load-on-startup>
        </servlet>
- 访问日志设置
      在server.xml中加入
   <Valve className="org.apache.catalina.valves.AccessLogValve"
              directory="logs"  prefix="localhost_access_log." suffix=".txt"
              pattern="common" resolveHosts="false"/>
      这样访问日志会记录到Logs中。
   - 修改用户名、密码
   conf/tomcat-users.xml
   - 屏蔽后台管理入口
      方法一：从控制用户和权限着手。废掉要管理权限的用户就可以了。
      方法二：将conf/Catalina/localhost/manager.xml改名。
   - 配置403,404,500错误页面
      默认情况下，报出HTTP错误的时候会暴露tomcat版本号。如果不想暴露的话，就需要重新定义错误跳转页面。
       <error-page>
       <error-code>401</error-code>
      <location>/401.jsp</location>
      </error-page>
      <error-page>
      <error-code>404</error-code>
      <location>/404.jsp</location>
      </error-page>
      <error-page>
      <error-code>500</error-code>
      <location>/500.jsp</location>
      </error-page>

  

## tomcat优化配置参数

优化内存，主要是在bin/catalina.bat/sh 配置文件中进行。linux上，在catalina.sh中添加：

```shell
JAVA_OPTS="-server -Xms1G -Xmx2G -Xss256K -Djava.awt.headless=true -Dfile.encoding=utf-8 -XX:MaxPermSize=256m -XX:PermSize=128M -XX:MaxPermSize=256M"

• -server：启用jdk的server版本。
• -Xms：虚拟机初始化时的最小堆内存。
• -Xmx：虚拟机可使用的最大堆内存。 #-Xms与-Xmx设成一样的值，避免JVM因为频繁的GC导致性能大起大落
• -XX:PermSize：设置非堆内存初始值,默认是物理内存的1/64。
• -XX:MaxNewSize：新生代占整个堆内存的最大值。
• -XX:MaxPermSize：Perm（俗称方法区）占整个堆内存的最大值，也称内存最大永久保留区域。
```

1）错误提示：java.lang.OutOfMemoryError:Java heap space

Tomcat默认可以使用的内存为128MB，在较大型的应用项目中，这点内存是不够的，有可能导致系统无法运行。常见的问题是报Tomcat内存溢出错误，Outof Memory(系统内存不足)的异常，从而导致客户端显示500错误，一般调整Tomcat的-Xms和-Xmx即可解决问题，通常将-Xms和-Xmx设置成一样，堆的最大值设置为物理可用内存的最大值的80%。

```
set JAVA_OPTS=-Xms512m-Xmx512m
```

2）错误提示：java.lang.OutOfMemoryError: PermGenspace

PermGenspace的全称是Permanent Generationspace,是指内存的永久保存区域，这块内存主要是被JVM存放Class和Meta信息的,Class在被Loader时就会被放到PermGenspace中，它和存放类实例(Instance)的Heap区域不同,GC(Garbage Collection)不会在主程序运行期对PermGenspace进行清理，所以如果你的应用中有很CLASS的话,就很可能出现PermGen space错误，这种错误常见在web服务器对JSP进行precompile的时候。如果你的WEB APP下都用了大量的第三方jar, 其大小超过了jvm默认的大小(4M)那么就会产生此错误信息了。解决方法：

```
set JAVA_OPTS=-XX:PermSize=128M
```

3）在使用-Xms和-Xmx调整tomcat的堆大小时，还需要考虑垃圾回收机制。如果系统花费很多的时间收集垃圾，请减小堆大小。一次完全的垃圾收集应该不超过3-5 秒。如果垃圾收集成为瓶颈，那么需要指定代的大小，检查垃圾收集的详细输出，研究垃圾收集参数对性能的影响。一般说来，你应该使用物理内存的 80% 作为堆大小。当增加处理器时，记得增加内存，因为分配可以并行进行，而垃圾收集不是并行的。

2、连接数优化：

\#优化连接数，主要是在conf/server.xml配置文件中进行修改。

2.1、优化线程数

找到Connector port="8080" protocol="HTTP/1.1"，增加maxThreads和acceptCount属性（使acceptCount大于等于maxThreads），如下：

```
<Connector port="8080" protocol="HTTP/1.1"connectionTimeout="20000" redirectPort="8443"acceptCount="500" maxThreads="400" />
```

其中：

```
• maxThreads：tomcat可用于请求处理的最大线程数，默认是200
• minSpareThreads：tomcat初始线程数，即最小空闲线程数
• maxSpareThreads：tomcat最大空闲线程数，超过的会被关闭
• acceptCount：当所有可以使用的处理请求的线程数都被使用时，可以放到处理队列中的请求数，超过这个数的请求将不予处理.默认100
```

2.2、使用线程池

在server.xml中增加executor节点，然后配置connector的executor属性，如下：

```
<Executor name="tomcatThreadPool" namePrefix="req-exec-"maxThreads="1000" minSpareThreads="50"maxIdleTime="60000"/>
<Connector port="8080" protocol="HTTP/1.1"executor="tomcatThreadPool"/>
```

其中：

```
• namePrefix：线程池中线程的命名前缀
• maxThreads：线程池的最大线程数
• minSpareThreads：线程池的最小空闲线程数
• maxIdleTime：超过最小空闲线程数时，多的线程会等待这个时间长度，然后关闭
• threadPriority：线程优先级
```

注：当tomcat并发用户量大的时候，单个jvm进程确实可能打开过多的文件句柄，这时会报java.net.SocketException:Too many open files错误。可使用下面步骤检查：

```
• ps -ef |grep tomcat 查看tomcat的进程ID，记录ID号，假设进程ID为10001
• lsof -p 10001|wc -l 查看当前进程id为10001的 文件操作数
• 使用命令：ulimit -a 查看每个用户允许打开的最大文件数
```

3、Tomcat Connector三种运行模式（BIO, NIO, APR）

3.1、三种模式比较：

1）BIO：一个线程处理一个请求。缺点：并发量高时，线程数较多，浪费资源。Tomcat7或以下在Linux系统中默认使用这种方式。

2）NIO：利用Java的异步IO处理，可以通过少量的线程处理大量的请求。Tomcat8在Linux系统中默认使用这种方式。Tomcat7必须修改Connector配置来启动（conf/server.xml配置文件）：

```
<Connector port="8080"protocol="org.apache.coyote.http11.Http11NioProtocol" connectionTimeout="20000"redirectPort="8443"/>
```

3）APR(Apache Portable Runtime)：从操作系统层面解决io阻塞问题。Linux如果安装了apr和native，Tomcat直接启动就支持apr。

3.2、apr模式

安装apr以及tomcat-native

```
yum -y install apr apr-devel
```

进入tomcat/bin目录，比如：

```
cd /opt/local/tomcat/bin/
tar xzfv tomcat-native.tar.gz
cd tomcat-native-1.1.32-src/jni/native
./configure --with-apr=/usr/bin/apr-1-config
make && make install
```

\#注意最新版本的tomcat自带tomcat-native.war.gz，不过其版本相对于yum安装的apr过高，configure的时候会报错。

解决：yum remove apr apr-devel –y,卸载yum安装的apr和apr-devel,下载最新版本的apr源码包，编译安装;或者下载低版本的tomcat-native编译安装

安装成功后还需要对tomcat设置环境变量，方法是在catalina.sh文件中增加1行：

```
CATALINA_OPTS="-Djava.library.path=/usr/local/apr/lib"
```

修改8080端对应的conf/server.xml

protocol="org.apache.coyote.http11.Http11AprProtocol"

```
<Connector executor="tomcatThreadPool"
port="8080"
protocol="org.apache.coyote.http11.Http11AprProtocol"
connectionTimeout="20000"
enableLookups="false"
redirectPort="8443"
URIEncoding="UTF-8" />
```

PS:启动以后查看日志 显示如下表示开启 apr 模式

```
Sep 19, 2016 3:46:21 PM org.apache.coyote.AbstractProtocol start
INFO: Starting ProtocolHandler ["http-apr-8081"]
```

**tomcat常见面试题**

tomcat默认采用的BIO模型，在几百并发下性能会有很严重的下降。tomcat自带还有NIO的模型，另外也可以调用APR的库来实现操作系统级别控制。NIO模型是内置的，调用很方便，只需要将上面配置文件中protocol修改成 org.apache.coyote.http11.Http11NioProtocol，重启即可生效。如下面的参数配置，默认的是HTTP/1.1。

<Connector port=”8080″ protocol=”org.apache.coyote.http11.Http11NioProtocol” connectionTimeout=”20000″ redirectPort=”8443″ maxThreads=”500″ minSpareThreads=”20″ acceptCount=”100″ disableUploadTimeout=”true”

enableLookups=”false” URIEncoding=”UTF-8″ />

**二、tomcat 如何优化？**

1、优化连接配置.这里以tomcat7的参数配置为例，需要修改conf/server.xml文件，修改连接数，关闭客户端dns查询。

参数解释：

URIEncoding=”UTF-8″ :使得tomcat可以解析含有中文名的文件的url，真方便，不像apache里还有搞个mod_encoding，还要手工编译

maxSpareThreads : 如果空闲状态的线程数多于设置的数目，则将这些线程中止，减少这个池中的线程总数。

minSpareThreads : 最小备用线程数，tomcat启动时的初始化的线程数。

enableLookups : 这个功效和Apache中的HostnameLookups一样，设为关闭。

connectionTimeout : connectionTimeout为网络连接超时时间毫秒数。

maxThreads : maxThreads Tomcat使用线程来处理接收的每个请求。这个值表示Tomcat可创建的最大的线程数，即最大并发数。

acceptCount : acceptCount是当线程数达到maxThreads后，后续请求会被放入一个等待队列，这个acceptCount是这个队列的大小，如果这个队列也满了，就直接refuse connection

maxProcessors与minProcessors : 在 Java中线程是程序运行时的路径，是在一个程序中与其它控制线程无关的、能够独立运行的代码段。它们共享相同的地址空间。多线程帮助程序员写出CPU最 大利用率的高效程序，使空闲时间保持最低，从而接受更多的请求。

通常Windows是1000个左右，Linux是2000个左右。

useURIValidationHack:

我们来看一下tomcat中的一段源码：

【security】

if (connector.getUseURIValidationHack()) {

String uri = validate(request.getRequestURI());

if (uri == null) {

res.setStatus(400);

res.setMessage(“Invalid URI”);

throw new IOException(“Invalid URI”);

} else {

req.requestURI().setString(uri);

// Redoing the URI decoding

req.decodedURI().duplicate(req.requestURI());

req.getURLDecoder().convert(req.decodedURI(), true);

可以看到如果把useURIValidationHack设成”false”，可以减少它对一些url的不必要的检查从而减省开销。

enableLookups=”false” ： 为了消除DNS查询对性能的影响我们可以关闭DNS查询，方式是修改server.xml文件中的enableLookups参数值。

disableUploadTimeout ：类似于Apache中的keeyalive一样

给Tomcat配置gzip压缩(HTTP压缩)功能

compression=”on” compressionMinSize=”2048″ compressableMimeType=”text/html,text/xml,text/javascript,text/css,text/plain”

HTTP 压缩可以大大提高浏览网站的速度，它的原理是，在客户端请求网页后，从服务器端将网页文件压缩，再下载到客户端，由客户端的浏览器负责解压缩并浏览。相对于普通的浏览过程HTML,CSS,Javascript , Text ，它可以节省40%左右的流量。更为重要的是，它可以对动态生成的，包括CGI、PHP , JSP , ASP , Servlet,SHTML等输出的网页也能进行压缩，压缩效率惊人。

1)compression=”on” 打开压缩功能

2)compressionMinSize=”2048″ 启用压缩的输出内容大小，这里面默认为2KB

3)noCompressionUserAgents=”gozilla, traviata” 对于以下的浏览器，不启用压缩

4)compressableMimeType=”text/html,text/xml”　压缩类型

最后不要忘了把8443端口的地方也加上同样的配置，因为如果我们走https协议的话，我们将会用到8443端口这个段的配置，对吧？｛

tomcat设置https端口时,8443和443区别:

- 8443端口在访问时需要加端口号,相当于http的8080,不可通过域名直接访问,需要加上端口号;https://yuming.com:8443。

- 443端口在访问时不需要加端口号,相当于http的80,可通过域名直接访问;例:https://yuming.com。

问:https使用域名访问网站,而不显示端口号?

 答:将端口号设置为443,即可通过域名直接访问网站

<!–enable tomcat ssl–>

<Connector port=”8443″ protocol=”HTTP/1.1″ URIEncoding=”UTF-8″ minSpareThreads=”25″ maxSpareThreads=”75″ enableLookups=”false” disableUploadTimeout=”true” connectionTimeout=”20000″ acceptCount=”300″ maxThreads=”300″ maxProcessors=”1000″ minProcessors=”5″ useURIValidationHack=”false” compression=”on” compressionMinSize=”2048″ compressableMimeType=”text/html,text/xml,text/javascript,text/css,text/plain”

SSLEnabled=”true”scheme=”https” secure=”true”clientAuth=”false” sslProtocol=”TLS”keystoreFile=”d:/tomcat2/conf/shnlap93.jks” keystorePass=”aaaaaa”/>

2、优化JDK

Tomcat默认可以使用的内存为128MB,Windows下,在文件{tomcat_home}/bin/catalina.bat，Unix下，在文件$CATALINA_HOME/bin/catalina.sh的前面，增加如下设置：

JAVA_OPTS=”‘$JAVA_OPTS” -Xms[初始化内存大小] -Xmx[可以使用的最大内存]

或

设置环境变量：export JAVA_OPTS=””$JAVA_OPTS” -Xms[初始化内存大小] -Xmx[可以使用的最大内存]”

一般说来，你应该使用物理内存的 80% 作为堆大小。如果本机上有Apache服务器，可以先折算Apache需要的内存，然后修改堆大小。建议设置为70％；建议设置[[初始化内存大小]等于[可以使用的最大内存]，这样可以减少平凡分配堆而降低性能。

本例使用加入环境变量的方式：

\# vi /etc/profile

加入：export JAVA_OPTS=””$JAVA_OPTS” -Xms700 —Xmx700

\# source /etc/profile

【参数说明】

-Xms 是指设定程序启动时占用内存大小。一般来讲，大点，程序会启动的 快一点，但是也可能会导致机器暂时间变慢。

-Xmx 是指设定程序运行期间最大可占用的内存大小。如果程序运行需要占 用更多的内存，超出了这个设置值，就会抛出OutOfMemory 异常。

-Xss 是指设定每个线程的堆栈大小。这个就要依据你的程序，看一个线程 大约需要占用多少内存，可能会有多少线程同时运行等。

-XX:PermSize设置非堆内存初始值，默认是物理内存的1/64 。

-XX:MaxPermSize设置最大非堆内存的大小，默认是物理内存的1/4。

**三、tomcat 有那几种Connector 运行模式？**

tomcat的运行模式有3种.修改他们的运行模式.3种模式的运行是否成功,可以看他的启动控制台,或者启动日志.或者登录他们的默认页面http://localhost:8080/查看其中的服务器状态。

1)bio

默认的模式,性能非常低下,没有经过任何优化处理和支持.

2)nio

利用java的异步io护理技术,no blocking IO技术.

想运行在该模式下，直接修改server.xml里的Connector节点,修改protocol为

<Connector port=”80″ protocol=”org.apache.coyote.http11.Http11NioProtocol” connectionTimeout=”20000″ URIEncoding=”UTF-8″ useBodyEncodingForURI=”true” enableLookups=”false” redirectPort=”8443″ />启动后,就可以生效。

3)apr

安装起来最困难,但是从操作系统级别来解决异步的IO问题,大幅度的提高性能.

必须要安装apr和native，直接启动就支持apr。下面的修改纯属多余，仅供大家扩充知识,但仍然需要安装apr和native。如nio修改模式,修改protocol为org.apache.coyote.http11.Http11AprProtocol

## tomcat调优

1. JVM参数调优：-Xms<size> 表示JVM初始化堆的大小，-Xmx<size>表示JVM堆的最大值。这两个值的大小一般根据需要进行设置。当应用程序需要的内存超出堆的最大值时虚拟机就会提示内存溢出，并且导致应用服务崩溃。因此一般建议堆的最大值设置为可用内存的最大值的80%。在catalina.bat中，设置JAVA_OPTS='-Xms256m -Xmx512m'，表示初始化内存为256MB，可以使用的最大内存为512MB。 
2. 禁用DNS查询 
   　当web应用程序想要记录客户端的信息时，它也会记录客户端的IP地址或者通过域名服务器查找机器名转换为IP地址。DNS查询需要占用网络，并且包括可能从很多很远的服务器或者不起作用的服务器上去获取对应的IP的过程，这样会消耗一定的时间。为了消除DNS查询对性能的影响我们可以关闭DNS查询，方式是修改server.xml文件中的enableLookups参数值： 
   <Connector port="80" maxThreads="150" minSpareThreads="25" maxSpareThreads="75" enableLookups="false" redirectPort="8443" acceptCount="100" debug="0" connectionTimeout="20000" disableUploadTimeout="true"/> 
3. 调整线程数 
   通过应用程序的连接器（Connector）进行性能控制的的参数是创建的处理请求的线程数。Tomcat使用线程池加速响应速度来处理请求。在Java中线程是程序运行时的路径，是在一个程序中与其它控制线程无关的、能够独立运行的代码段。它们共享相同的地址空间。多线程帮助程序员写出CPU最大利用率的高效程序，使空闲时间保持最低，从而接受更多的请求。 
   Tomcat4中可以通过修改minProcessors和maxProcessors的值来控制线程数。这些值在安装后就已经设定为默认值并且是足够使用的，但是随着站点的扩容而改大这些值。minProcessors服务器启动时创建的处理请求的线程数应该足够处理一个小量的负载。也就是说，如果一天内每秒仅发生5次单击事件，并且每个请求任务处理需要1秒钟，那么预先设置线程数为5就足够了。但在你的站点访问量较大时就需要设置更大的线程数，指定为参数maxProcessors的值。maxProcessors的值也是有上限的，应防止流量不可控制（或者恶意的服务攻击），从而导致超出了虚拟机使用内存的大小。如果要加大并发连接数，应同时加大这两个参数。web server允许的最大连接数还受制于操作系统的内核参数设置，通常Windows是2000个左右，Linux是1000个左右。 
   在Tomcat5对这些参数进行了调整，请看下面属性： 
   maxThreads  Tomcat使用线程来处理接收的每个请求。这个值表示Tomcat可创建的最大的线程数。 
   acceptCount  指定当所有可以使用的处理请求的线程数都被使用时，可以放到处理队列中的请求数，超过这个数的请求将不予处理。 
   connnectionTimeout  网络连接超时，单位：毫秒。设置为0表示永不超时，这样设置有隐患的。通常可设置为30000毫秒。 
   minSpareThreads  Tomcat初始化时创建的线程数。 
   maxSpareThreads   一旦创建的线程超过这个值，Tomcat就会关闭不再需要的socket线程。 
   最好的方式是多设置几次并且进行测试，观察响应时间和内存使用情况。在不同的机器、操作系统或虚拟机组合的情况下可能会不同，而且并不是所有人的web站点的流量都是一样的，因此没有一刀切的方案来确定线程数的值。 



Tomcat作为Web服务器，它的处理性能直接关系到用户体验，下面是几种常见的优化措施：

   一、掉对web.xml的监视，把jsp提前编辑成Servlet。有富余物理内存的情况，加大tomcat使用的jvm的内存

   二、服务器资源
　　服务器所能提供CPU、内存、硬盘的性能对处理能力有决定性影响。
　　(1) 对于高并发情况下会有大量的运算，那么CPU的速度会直接影响到处理速度。
　　(2) 内存在大量数据处理的情况下，将会有较大的内存容量需求，可以用-Xmx -Xms -XX:MaxPermSize等参数对内存不同功能块进行划分。我们之前就遇到过内存分配不足，导致虚拟机一直处于full GC，从而导致处理能力严重下降。
　　(3) 硬盘主要问题就是读写性能，当大量文件进行读写时，磁盘极容易成为性能瓶颈。最好的办法还是利用下面提到的缓存。

  三、利用缓存和压缩
　　对于静态页面最好是能够缓存起来，这样就不必每次从磁盘上读。这里我们采用了Nginx作为缓存服务器，将图片、css、js文件都进行了缓存，有效的减少了后端tomcat的访问。
　　另外，为了能加快网络传输速度，开启gzip压缩也是必不可少的。但考虑到tomcat已经需要处理很多东西了，所以把这个压缩的工作就交给前端的Nginx来完成。
　　除了文本可以用gzip压缩，其实很多图片也可以用图像处理工具预先进行压缩，找到一个平衡点可以让画质损失很小而文件可以减小很多。曾经我就见过一个图片从300多kb压缩到几十kb，自己几乎看不出来区别。

   四、采用集群
　　单个服务器性能总是有限的，最好的办法自然是实现横向扩展，那么组建tomcat集群是有效提升性能的手段。我们还是采用了Nginx来作为请求分流的服务器，后端多个tomcat共享session来协同工作。

   五、 优化tomcat参数

<Connector port="8080"  
      protocol="org.apache.coyote.http11.Http11NioProtocol"  
      connectionTimeout="20000"  
      redirectPort="8443"  
      maxThreads="500"  
      minSpareThreads="20"  
      acceptCount="100" 
      disableUploadTimeout="true" 
      enableLookups="false"  
      URIEncoding="UTF-8" /> 

