# zheng 搭建

## 基础环境

1. java 环境: 1.7.0_141
2. maven 环境: 3.0.5
3. redis version: 3.2.8; path: /root/software/redis-3.2.8 localhost:6379
4. nginx 部署方式: yum 安装; version: 1.10.3
5. zookeeper 192.168.1.95/132/70117:2181 version: 3.4.6
6. apache-activemq: version: 5.9.0  path: /root/software/

编译结果:
```
./zheng-oss/zheng-oss-web/target/zheng-oss-web.war
./zheng-oss/zheng-oss-admin/target/zheng-oss-admin.war
./zheng-wechat/zheng-wechat-mp/zheng-wechat-mp-admin/target/zheng-wechat-mp-admin.war
./zheng-pay/zheng-pay-web/target/zheng-pay-web.war
./zheng-pay/zheng-pay-admin/target/zheng-pay-admin.war
./zheng-demo/zheng-demo-web/target/zheng-demo-web.war
./zheng-cms/zheng-cms-search/target/zheng-cms-search.war
./zheng-cms/zheng-cms-job/target/zheng-cms-job.war
./zheng-cms/zheng-cms-web/target/zheng-cms-web.war
./zheng-cms/zheng-cms-admin/target/zheng-cms-admin.war
./zheng-api/zheng-api-server/target/zheng-api-server.war
./zheng-ucenter/zheng-ucenter-web/target/zheng-ucenter-web.war
./zheng-upms/zheng-upms-server/target/zheng-upms-server.war
```

```
zheng-api/zheng-api-rpc-service/target/zheng-api-rpc-service-assembly.tar.gz
zheng-cms/zheng-cms-rpc-service/target/zheng-cms-rpc-service-assembly.tar.gz
zheng-demo/zheng-demo-rpc-service/target/zheng-demo-rpc-service-1.0.0-assembly.tar.gz
zheng-pay/zheng-pay-rpc-service/target/zheng-pay-rpc-service-assembly.tar.gz
zheng-ucenter/zheng-ucenter-rpc-service/target/zheng-ucenter-rpc-service-assembly.tar.gz
zheng-upms/zheng-upms-rpc-service/target/zheng-upms-rpc-service-assembly.tar.gz
```

## 部署步骤

编译前需要将配置文件修改好。
通过 `<project_path>/zheng-common/src/main/java/com/zheng/common/util/AESUtil.java` 来获取加密后的各种配置。

编译指令: `mvn clean install`

生成相应的 war 包和注册 dubbo 的 tar 包文件。
将服务端的 war 包发布, 解压 tar 包文件, 执行相应的 start 脚本。
发布相应的 xxx-web.war 包。


## 问题

1. nginx 作用是什么

即哪些部分是需要通过 nginx 代理的, 哪些不需要通过 nginx 代理(不考虑其他因素, 在何处必须依赖 nginx)。

2. log:

```
2017-10-26 21:33:27 [ ERROR ] [ ContextLoader.java :350(initWebApplicationContext)]  Context initialization failed
org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'org.springframework.cache.interceptor.CacheInterceptor#0': Cannot resolve reference to bean 'cacheManager' while setting bean property 'cacheManager'; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'cacheManager' defined in file [/root/tomcat/tomcat-upm-service/webapps/zheng-upms-server/WEB-INF/classes/applicationContext-ehcache.xml]: Cannot resolve reference to bean 'cacheManagerFactory' while setting bean property 'cacheManager'; nested exception is org.springframework.beans.factory.BeanCreationException: Error creating bean with name 'cacheManagerFactory' defined in file [/root/tomcat/tomcat-upm-service/webapps/zheng-upms-server/WEB-INF/classes/applicationContext-ehcache.xml]: Invocation of init method failed; nested exception is net.sf.ehcache.CacheException: Error configuring from input stream. Initial cause was null:12: Element <defaultCache> does not allow nested <persistence> elements.
```

3. 192.168.1.125 出现的问题

```
2017-10-30 21:26:51 [ WARN ] [ AbstractRegistry.java :212(doSaveProperties)]   [DUBBO] Failed to save registry store file, cause: No such file or directory, dubbo version: 2.5.6, current host: 192.168.1.125
java.io.IOException: No such file or directory
	at java.io.UnixFileSystem.createFileExclusively(Native Method)
	at java.io.File.createNewFile(File.java:1006)
	at com.alibaba.dubbo.registry.support.AbstractRegistry.doSaveProperties(AbstractRegistry.java:176)
	at com.alibaba.dubbo.registry.support.AbstractRegistry$SaveProperties.run(AbstractRegistry.java:518)
	at java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1145)
	at java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:615)
	at java.lang.Thread.run(Thread.java:745)
```