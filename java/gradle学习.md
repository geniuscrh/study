[TOC]

# 案例

``` properties
buildscript {
   ext {
      springBootVersion = '2.0.3.RELEASE'
   }
//修改云下载路径，加快下载速度
   repositories {
      maven {url 'http://maven.aliyun.com/nexus/content/groups/public/' }
      maven{ url 'http://maven.aliyun.com/nexus/content/repositories/jcenter'}
      //mavenCentral()
   }
   dependencies {
      classpath("org.springframework.boot:spring-boot-gradle-plugin:${springBootVersion}")
   }
}

apply plugin: 'java'
apply plugin: 'eclipse'
apply plugin: 'org.springframework.boot'
apply plugin: 'io.spring.dependency-management'

group = 'com.geniuscrh'
version = '0.0.1-SNAPSHOT'
sourceCompatibility = 1.8

//修改云下载路径，加快下载速度
repositories {
   //mavenCentral()
   maven {url 'http://maven.aliyun.com/nexus/content/groups/public/' }
    maven{ url 'http://maven.aliyun.com/nexus/content/repositories/jcenter'} 
}


dependencies {
   compile('org.springframework.boot:spring-boot-starter')
   compile('org.springframework.boot:spring-boot-starter-web')
   compile('org.springframework.boot:spring-boot-starter-thymeleaf')
   compile('org.springframework.boot:spring-boot-starter-data-jpa')
   compile ("mysql:mysql-connector-java")
   compile ("org.apache.poi:poi:3.14-beta1")
   compile("org.apache.httpcomponents:httpclient")
   compile("org.springframework:springloaded:1.2.7.RELEASE")
   compile group: 'org.apache.shiro', name: 'shiro-spring', version: '1.4.0'
   compile files('libs/JNative.jar')
   testCompile('org.springframework.boot:spring-boot-starter-test')
}

```


# 常用Jar支持
``` properties
dependencies {
   //srping基本包
   compile('org.springframework.boot:spring-boot-starter')
   //web支持
   compile('org.springframework.boot:spring-boot-starter-web')
  //thymeleaf支持
   compile('org.springframework.boot:spring-boot-starter-thymeleaf')
//jap支持
   compile('org.springframework.boot:spring-boot-starter-data-jpa')
//mysql支持
   compile ("mysql:mysql-connector-java")
//poi支持
   compile ("org.apache.poi:poi:3.14-beta1")
//Http下载
   compile("org.apache.httpcomponents:httpclient")
//文件上传
   compile("org.springframework:springloaded:1.2.7.RELEASE")
//shiro
   compile group: 'org.apache.shiro', name: 'shiro-spring', version: '1.4.0'
//载入自定义包
   compile files('libs/JNative.jar')
//测试
   testCompile('org.springframework.boot:spring-boot-starter-test')
}

```


# 其他
## 修改镜像
``` properties
Buildscript. Repositories{
	maven {url 'http://maven.aliyun.com/nexus/content/groups/public/' }
   	maven{ url 'http://maven.aliyun.com/nexus/content/repositories/jcenter'}
}
Repositories{
maven {url 'http://maven.aliyun.com/nexus/content/groups/public/' }
    maven{ url 'http://maven.aliyun.com/nexus/content/repositories/jcenter'}
}
```