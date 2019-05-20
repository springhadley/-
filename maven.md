**创建私有库**

​	第一步：修改 settings.xml文件

~~~xml
<server>
   <id>releases</id>
   <username>admin</username>
   <password>admin123</password>
 </server>
<server>
   <id>snapshots</id>
   <username>admin</username>
   <password>admin123</password>
 </server>
~~~

​	第二步:  配置项目 pom.xml 

注意：pom.xml 这里id 和 settings.xml 配置 id 对应

~~~xml
<distributionManagement>
 <repository>
    <id>releases</id>
    <url>http://localhost:8081/nexus/content/repositories/releases/</url>
 </repository>
 <snapshotRepository>
    <id>snapshots</id>
    <url>http://localhost:8081/nexus/content/repositories/snapshots/</url>
 </snapshotRepository>
</distributionManagement>


~~~

  测试:

​	1、首先启动 nexus

​	2、对 ssm_dao 工程执行 deploy 命令



**从私服下载 jar 包**

在 setting.xml 中配置仓库

~~~xml
<profile> 
<!--profile 的 id-->
 <id>dev</id> 
 <repositories> 
 <repository> 
<!--仓库 id，repositories 可以配置多个仓库，保证 id 不重复-->
 <id>nexus</id>
   <!--仓库地址，即 nexus 仓库组的地址-->
 <url>http://localhost:8081/nexus/content/groups/public/</url> 
<!--是否下载 releases 构件-->
 <releases> 
 <enabled>true</enabled> 
 </releases> 
<!--是否下载 snapshots 构件-->
 <snapshots> 
 <enabled>true</enabled> 
 </snapshots> 
 </repository> 
 </repositories> 
<pluginRepositories> 
 <!-- 插件仓库，maven 的运行依赖插件，也需要从私服下载插件 -->
 <pluginRepository> 
 <!-- 插件仓库的 id 不允许重复，如果重复后边配置会覆盖前边 -->
 <id>public</id> 
 <name>Public Repositories</name> 
 <url>http://localhost:8081/nexus/content/groups/public/</url> 
 </pluginRepository> 
 </pluginRepositories> 
 </profile>
~~~

使用 profile 定义仓库需要激活才可生效。

~~~xml
<activeProfiles>
 	<activeProfile>dev</activeProfile>
</activeProfiles>
~~~

pom文件中配置仓库

~~~xml
  <repositories>
        <repository>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
            <id>public</id>
            <name>Public Repositories</name>
            <url>http://localhost:8081/nexus/content/groups/public/</url>
        </repository>
        <repository>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <id>central</id>
            <name>Central Repository</name>
            <url>https://repo.maven.apache.org/maven2</url>
        </repository>
    </repositories>
    <pluginRepositories>
        <pluginRepository>
            <id>public</id>
            <name>Public Repositories</name>
            <url>http://localhost:8081/nexus/content/groups/public/</url>
        </pluginRepository>
        <pluginRepository>
            <releases>
                <updatePolicy>never</updatePolicy>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
            <id>central</id>
            <name>Central Repository</name>
            <url>https://repo.maven.apache.org/maven2</url>
        </pluginRepository>
    </pluginRepositories>

~~~

clear compile



将第三方jar包导入本地仓库

~~~c
mvn install:install-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37
-Dfile= fastjson-1.1.37.jar -Dpackaging=jar
~~~

导入私服

配置server信息

~~~c
<server> 
   <id>thirdparty</id> 
   <username>admin</username>
   <password>admin123</password> 
</server>
~~~

在jar包目录下

~~~c
mvn deploy:deploy-file -DgroupId=com.alibaba -DartifactId=fastjson -Dversion=1.1.37 
-Dpackaging=jar -Dfile=fastjson-1.1.37.jar 
-Durl=http://localhost:8081/nexus/content/repositories/thirdparty/ 
-DrepositoryId=thirdparty
~~~











