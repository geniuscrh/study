# 结构图

![结构图](d:\java web(spring boot)\结构图.png)



# 后台主要技术栈

- 核心框架：Spring Boot + Spring Cloud
- 视图框架：Spring MVC
- 页面引擎：Thymeleaf
- ORM 框架：tk.mybatis 简化 MyBatis 开发
- 数据库连接池：Alibaba Druid
- 数据库缓存：Redis Sentinel
- 消息中间件：RabbitMQ
- 接口文档引擎：Swagger2 RESTful 风格 API 文档生成
- 全文检索引擎：ElasticSearch
- 分布式链路追踪：ZipKin
- 分布式文件系统：Alibaba FastDFS
- 分布式服务监控：Spring Boot Admin
- 分布式协调系统：Spring Cloud Eureka
- 分布式配置中心：Spring Cloud Config
- 分布式日志系统：ELK（ElasticSearch + Logstash + Kibana）
- 反向代理负载均衡：Nginx



# 服务部署

## 创建统一的依赖管理:dependencies

### pom.xml配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>2.0.3.RELEASE</version>
    </parent>

    <groupId>com.geniuscrh</groupId>
    <artifactId>spring-cloud-dependencies</artifactId>
    <version>1.0.0-SNAPSHOT</version>
    <packaging>pom</packaging>

    <name>spring-cloud-dependencies</name>

    <properties>
        <!-- Environment Settings -->
        <java.version>1.8</java.version>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <!-- Spring Settings -->
        <spring-cloud.version>Finchley.RC1</spring-cloud.version>
    </properties>

    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring-cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
        </dependencies>
    </dependencyManagement>

    <build>
        <plugins>
            <!-- Compiler 插件, 设定 JDK 版本 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <configuration>
                    <showWarnings>true</showWarnings>
                </configuration>
            </plugin>

            <!-- 打包 jar 文件时，配置 manifest 文件，加入 lib 包的 jar 依赖 -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-jar-plugin</artifactId>
                <configuration>
                    <archive>
                        <addMavenDescriptor>false</addMavenDescriptor>
                    </archive>
                </configuration>
                <executions>
                    <execution>
                        <configuration>
                            <archive>
                                <manifest>
                                    <!-- Add directory entries -->
                                    <addDefaultImplementationEntries>true</addDefaultImplementationEntries>
                                    <addDefaultSpecificationEntries>true</addDefaultSpecificationEntries>
                                    <addClasspath>true</addClasspath>
                                </manifest>
                            </archive>
                        </configuration>
                    </execution>
                </executions>
            </plugin>

            <!-- resource -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-resources-plugin</artifactId>
            </plugin>

            <!-- install -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-install-plugin</artifactId>
            </plugin>

            <!-- clean -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-clean-plugin</artifactId>
            </plugin>

            <!-- ant -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-antrun-plugin</artifactId>
            </plugin>

            <!-- dependency -->
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-dependency-plugin</artifactId>
            </plugin>
        </plugins>

        <pluginManagement>
            <plugins>
                <!-- Java Document Generate -->
                <plugin>
                    <groupId>org.apache.maven.plugins</groupId>
                    <artifactId>maven-javadoc-plugin</artifactId>
                    <executions>
                        <execution>
                            <phase>prepare-package</phase>
                            <goals>
                                <goal>jar</goal>
                            </goals>
                        </execution>
                    </executions>
                </plugin>

                <!-- YUI Compressor (CSS/JS压缩) -->
                <plugin>
                    <groupId>net.alchim31.maven</groupId>
                    <artifactId>yuicompressor-maven-plugin</artifactId>
                    <version>1.5.1</version>
                    <executions>
                        <execution>
                            <phase>prepare-package</phase>
                            <goals>
                                <goal>compress</goal>
                            </goals>
                        </execution>
                    </executions>
                    <configuration>
                        <encoding>UTF-8</encoding>
                        <jswarn>false</jswarn>
                        <nosuffix>true</nosuffix>
                        <linebreakpos>30000</linebreakpos>
                        <force>true</force>
                        <includes>
                            <include>**/*.js</include>
                            <include>**/*.css</include>
                        </includes>
                        <excludes>
                            <exclude>**/*.min.js</exclude>
                            <exclude>**/*.min.css</exclude>
                        </excludes>
                    </configuration>
                </plugin>
            </plugins>
        </pluginManagement>

        <!-- 资源文件配置 -->
        <resources>
            <resource>
                <directory>src/main/java</directory>
                <excludes>
                    <exclude>**/*.java</exclude>
                </excludes>
            </resource>
            <resource>
                <directory>src/main/resources</directory>
            </resource>
        </resources>
    </build>

    <repositories>
        <repository>
            <id>aliyun-repos</id>
            <name>Aliyun Repository</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>

        <repository>
            <id>sonatype-repos</id>
            <name>Sonatype Repository</name>
            <url>https://oss.sonatype.org/content/groups/public</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>sonatype-repos-s</id>
            <name>Sonatype Repository</name>
            <url>https://oss.sonatype.org/content/repositories/snapshots</url>
            <releases>
                <enabled>false</enabled>
            </releases>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>

        <repository>
            <id>spring-snapshots</id>
            <name>Spring Snapshots</name>
            <url>https://repo.spring.io/snapshot</url>
            <snapshots>
                <enabled>true</enabled>
            </snapshots>
        </repository>
        <repository>
            <id>spring-milestones</id>
            <name>Spring Milestones</name>
            <url>https://repo.spring.io/milestone</url>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </repository>
    </repositories>

    <pluginRepositories>
        <pluginRepository>
            <id>aliyun-repos</id>
            <name>Aliyun Repository</name>
            <url>http://maven.aliyun.com/nexus/content/groups/public</url>
            <releases>
                <enabled>true</enabled>
            </releases>
            <snapshots>
                <enabled>false</enabled>
            </snapshots>
        </pluginRepository>
    </pluginRepositories>
</project>
```

- parent：继承了 Spring Boot 的 Parent，表示我们是一个 Spring Boot 工程
- package：`pom`，表示该项目仅当做依赖项目，没有具体的实现代码
- `spring-cloud-dependencies`：在 `properties` 配置中预定义了版本号为 `Finchley.RC1` ，表示我们的 Spring Cloud 使用的是 F 版
- build：配置了项目所需的各种插件
- repositories：配置项目下载依赖时的第三方库

在实际开发中，我们所有的项目都会依赖这个 `dependencies` 项目，整个项目周期中的所有第三方依赖的版本也都由该项目进行管理。









## 服务注册与发现：Eureka

### pom.xml配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">

    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.geniuscrh</groupId>
        <artifactId>spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>spring-cloud-eureka</artifactId>
    <packaging>jar</packaging>

    <name>spring-cloud-eureka</name>


    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.geniuscrh.springCloudDemo.EurekaApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

### Application

```java
package com.funtl.hello.spring.cloud.eureka;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.server.EnableEurekaServer;

@SpringBootApplication
//关键
@EnableEurekaServer
public class EurekaApplication {
    public static void main(String[] args) {
        SpringApplication.run(EurekaApplication.class, args);
    }
}
```

application.yml

```yml
spring:
  application:
    name: spring-cloud-eureka

server:
  port: 8761

eureka:
  instance:
    hostname: localhost
  client:
    registerWithEureka: false
    fetchRegistry: false
    serviceUrl:
      defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/
```

通过 `eureka.client.registerWithEureka:false` 和 `fetchRegistry:false` 来表明自己是一个 Eureka Server.











## 配置中心：Spring Cloud Config

### pom.xml配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.funtl</groupId>
        <artifactId>hello-spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../hello-spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>hello-spring-cloud-config</artifactId>
    <packaging>jar</packaging>

    <name>hello-spring-cloud-config</name>


    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-config-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.funtl.hello.spring.cloud.config.ConfigApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```

主要增加了 `spring-cloud-config-server` 依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-config-server</artifactId>
</dependency>
```

### Application.java

```java
package com.funtl.hello.spring.cloud.config;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.config.server.EnableConfigServer;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
@EnableConfigServer
@EnableEurekaClient
public class ConfigApplication {
    public static void main(String[] args) {
        SpringApplication.run(ConfigApplication.class, args);
    }
}
```

### application.yml

```yaml
spring:
  application:
    name: hello-spring-cloud-config
  cloud:
    config:
      label: master
      server:
        git:
          uri: https://github.com/topsale/spring-cloud-config
          search-paths: respo
          username:
          password:

server:
  port: 8888

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```

相关配置说明，如下：

- `spring.cloud.config.label`：配置仓库的分支
- `spring.cloud.config.server.git.uri`：配置 Git 仓库地址（GitHub、GitLab、码云 …）
- `spring.cloud.config.server.git.search-paths`：配置仓库路径（存放配置文件的目录）
- `spring.cloud.config.server.git.username`：访问 Git 仓库的账号
- `spring.cloud.config.server.git.password`：访问 Git 仓库的密码

注意事项：

- 如果使用 GitLab 作为仓库的话，`git.uri` 需要在结尾加上 `.git`，GitHub 则不用

### 测试

浏览器端访问：<http://localhost:8888/config-client/dev/master> 显示如下：



证明配置服务中心可以从远程程序获取配置信息

### HTTP 请求地址和资源文件映射

- [http://ip:port/{application}/{profile}[/{label}\]](http://ip:port/%7Bapplication%7D/%7Bprofile%7D[/%7Blabel%7D])
- [http://ip:port/{application}-{profile}.yml](http://ip:port/%7Bapplication%7D-%7Bprofile%7D.yml)
- [http://ip:port/{label}/{application}-{profile}.yml](http://ip:port/%7Blabel%7D/%7Bapplication%7D-%7Bprofile%7D.yml)
- [http://ip:port/{application}-{profile}.properties](http://ip:port/%7Bapplication%7D-%7Bprofile%7D.properties)
- [http://ip:port/{label}/{application}-{profile}.properties](http://ip:port/%7Blabel%7D/%7Bapplication%7D-%7Bprofile%7D.properties)





### 客户端

#### pom.xml增加依赖

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-config</artifactId>
</dependency>
```

#### bootstrap.yml

增加 Config Client 相关配置，并设置端口号为：`8889`

```yaml
spring:
  application:
    name: hello-spring-cloud-config-client
  cloud:
    config:
      uri: http://localhost:8888
      name: config-client
      label: master
      profile: dev


```

相关配置说明，如下：

- `spring.cloud.config.uri`：配置服务中心的网址

- `spring.cloud.config.name`：配置文件名称的前缀

- `spring.cloud.config.label`：配置仓库的分支

- ```
  spring.cloud.config.profile
  ```

  ：配置文件的环境标识

  - dev：表示开发环境
  - test：表示测试环境
  - prod：表示生产环境

注意事项：

- 配置服务器的默认端口为 `8888`，如果修改了默认端口，则客户端项目就不能在 `application.yml` 或 `application.properties` 中配置 `spring.cloud.config.uri`，必须在 `bootstrap.yml` 或是 `bootstrap.properties` 中配置，原因是 `bootstrap` 开头的配置文件会被优先加载和配置，切记。







































## 创建服务提供者

### pom.xml配置

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.geniuscrh</groupId>
        <artifactId>spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>spring-cloud-service-admin</artifactId>
    <packaging>jar</packaging>

    <name>spring-cloud-service-admin</name>
    

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.geniuscrh.springCloudDemo.ServiceAdminApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```



### Application.java

```java
package com.funtl.hello.spring.cloud.service.admin;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.netflix.eureka.EnableEurekaClient;

@SpringBootApplication
//注册Eureka
@EnableEurekaClient
public class ServiceAdminApplication {
    public static void main(String[] args) {
        SpringApplication.run(ServiceAdminApplication.class, args);
    }
}
```

### application.yml

```yaml
spring:
  application:
    name: spring-cloud-service-admin

server:
  port: 8762

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```







## 创建服务消费者（Feign）

### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

   <parent>
        <groupId>com.geniuscrh</groupId>
        <artifactId>spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>spring-cloud-web-feign</artifactId>
    <packaging>jar</packaging>

    <name>spring-cloud-web-feign</name>
  

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-thymeleaf</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        
        <!-- 增加Fetion依赖 -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-openfeign</artifactId>
        </dependency>
        <!-- Spring Cloud End -->

       
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.geniuscrh.springCloudDemo.web.WebFeignApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```



### application.yml

```yaml
spring:
  application:
    name: hello-spring-cloud-web-admin-feign
  thymeleaf:
    cache: false
    mode: LEGACYHTML5
    encoding: UTF-8
    servlet:
      content-type: text/html

server:
  port: 8765

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/
```



### Application.java

```java

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.cloud.client.discovery.EnableDiscoveryClient;
import org.springframework.cloud.openfeign.EnableFeignClients;

@SpringBootApplication
@EnableDiscoveryClient
//开启Feign
@EnableFeignClients
public class WebAdminFeignApplication {
    public static void main(String[] args) {
        SpringApplication.run(WebAdminFeignApplication.class, args);
    }
}
```



### 创建 Feign 接口

通过 `@FeignClient("服务名")` 注解来指定调用哪个服务。代码如下：

```
package com.funtl.hello.spring.cloud.web.admin.feign.service;

import org.springframework.cloud.openfeign.FeignClient;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;

@FeignClient(value = "spring-cloud-service-admin")
public interface AdminService {

    @RequestMapping(value = "hi", method = RequestMethod.GET)
    public String sayHi(@RequestParam(value = "message") String message);
}
```





###  Controller

```java
package com.funtl.hello.spring.cloud.web.admin.feign.controller;

import com.funtl.hello.spring.cloud.web.admin.feign.service.AdminService;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class AdminController {

    @Autowired
    private AdminService adminService;

    @RequestMapping(value = "hi", method = RequestMethod.GET)
    public String sayHi(@RequestParam String message) {
        return adminService.sayHi(message);
    }
}
```





## 使用路由网关统一访问接口

### pom.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <modelVersion>4.0.0</modelVersion>

    <parent>
        <groupId>com.geniuscrh</groupId>
        <artifactId>spring-cloud-dependencies</artifactId>
        <version>1.0.0-SNAPSHOT</version>
        <relativePath>../spring-cloud-dependencies/pom.xml</relativePath>
    </parent>

    <artifactId>spring-cloud-zuul</artifactId>
    <packaging>jar</packaging>

    <name>spring-cloud-zuul</name>
 

    <dependencies>
        <!-- Spring Boot Begin -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-tomcat</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>
        <!-- Spring Boot End -->

        <!-- Spring Cloud Begin -->
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-eureka-server</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-netflix-zuul</artifactId>
        </dependency>
        <!-- Spring Cloud End -->
    </dependencies>

    <build>
        <plugins>
            <plugin>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-maven-plugin</artifactId>
                <configuration>
                    <mainClass>com.geniuscrh.springCloudDemo.zuul.ZuulApplication</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>
```



### application.yml

```yaml
spring:
  application:
    name: spring-cloud-zuul

server:
  port: 8769

eureka:
  client:
    serviceUrl:
      defaultZone: http://localhost:8761/eureka/

zuul:
  routes:
    api-a:
      path: /api/login/**
      serviceId: spring-cloud-web-feign
    api-b:
      path: /api/b/**
      serviceId: hello-spring-cloud-web-admin-feign
```



### Application.java

```java
@EnableZuulProxy
```



# Docker部署SpringCloud：

## 不使用插件

### 1.创建Dockerfile 

创建一个文件名为Dockerfile的文件，复制以下内容到文件中

```
FROM java:8 
#VOLUME /tmp 
ADD spring-cloud-eureka-1.0.0-SNAPSHOT.jar app.jar 
RUN bash -c 'touch /app.jar'
##CMD java -jar app.jar
ENTRYPOINT [“java”,”-Djava.security.egd=file:/dev/./urandom”,”-jar”,”/app.jar”]
```

```
FROM java:8 
#VOLUME /tmp 
ADD spring-cloud-eureka-1.0.0-SNAPSHOT.jar app.jar 
#RUN bash -c 'touch /app.jar'
#CMD java -jar app.jar
ENTRYPOINT java -jar app.jar
```



其中 lemon-member-0.0.1.jar为你的jar包的名字,其他配置解释后续添加

### 2.创建docker镜像 

将创建好的Dockerfile文件和jar包上传到服务器，放在同一文件夹下,进入jar包所在文件夹,执行命令

```
docker build -t lemon-order .
```

lemon-order为生成的镜像名， .表示当前目录

### 3.查看生成的镜像

```
docker images
```

可以看到有一个名为lemon-order的镜像

### 4.创建容器并运行jar包

```
docker run -d -p 8080:8080 lemon-order
```





## **使用docker-maven-plugin**

pom.xml增加plugin

```xml
<plugin>
				<groupId>com.spotify</groupId>
				<artifactId>docker-maven-plugin</artifactId>
				<version>1.0.0</version>
				<configuration>
					<imageName>${docker.image.prefix}/${project.artifactId}</imageName>
					<baseImage>java</baseImage>
					<entryPoint>["java", "-jar", "/${project.build.finalName}.jar"]</entryPoint>
					<resources>
						<resource>
							<targetPath>/</targetPath>
							<directory>${project.build.directory}</directory>
							<include>${project.build.finalName}.jar</include>
						</resource>
					</resources>
				</configuration>
			</plugin>
```



  **· imageName**:指定了镜像的名称，“/”前面是仓库名称，后面是镜像名称，也可以再加上标签名称；

  **· resources**:是指那些需要和Dockerfile放在一起，在构建镜像时使用的文件，一般应用 jar 包需要纳入。resources.resource.directory用于指定需要复制的根目录，${project.build.directory}表示target目录，resources.resource.include用于指定需要复制的文件，${project.build.finalName}.jar指的是打包后的jar包文件。