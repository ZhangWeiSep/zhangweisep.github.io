---
layout:     post
title:      zuul网关升级gateway问题——win10
subtitle:   zuul网关升级gateway问题
date:       2019-4-10
author:     逍遥
header-img: img/post-bg-debug.png
catalog: true
tags:
    - Spring Cloud
---
最近在做spring cloud的网关升级，遇到了各种各样奇怪的问题，在此做个记录，以防后面在遇到类似问题，好有个解决方案。

也希望能对一起在技术道路上摸爬滚打的同道有所帮助。

## 一、启动报错

### 问题一：spring-cloud-starter-gateway和spring-boot-starter-web产生冲突

pom.xml配置文件：

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.0.6.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
  </parent>
  <groupId>com.example</groupId>
  <artifactId>demo</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <name>demo</name>
  <description>Demo project for Spring Boot</description>

  <properties>
    <java.version>1.8</java.version>
    <spring-cloud.version>Finchley.SR2</spring-cloud.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-gateway</artifactId>
    </dependency>

    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>

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
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
```

错误说明：

```java
Description:

Parameter 0 of method modifyRequestBodyGatewayFilterFactory in org.springframework.cloud.gateway.config.GatewayAutoConfiguration required a bean of type 'org.springframework.http.codec.ServerCodecConfigurer' that could not be found.


Action:

Consider defining a bean of type 'org.springframework.http.codec.ServerCodecConfigurer' in your configuration.
```

解决方式：

jar包冲突问题，引入的spring-cloud-starter-gateway和spring-boot-starter-web两个包产生冲突，只需要删除spring-boot-starter-web的引用即可

### 问题二：spring-boot-starter-parent版本兼容

pom.xml配置文件：

```xml
<parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>2.1.1.RELEASE</version>
    <relativePath/> <!-- lookup parent from repository -->
  </parent>
  <groupId>com.example</groupId>
  <artifactId>demo</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <name>demo</name>
  <description>Demo project for Spring Boot</description>

  <properties>
    <java.version>1.8</java.version>
    <spring-cloud.version>Finchley.SR2</spring-cloud.version>
  </properties>

  <dependencies>
<!--    <dependency>-->
<!--      <groupId>org.springframework.boot</groupId>-->
<!--      <artifactId>spring-boot-starter-web</artifactId>-->
<!--    </dependency>-->
    <dependency>
      <groupId>org.springframework.cloud</groupId>
      <artifactId>spring-cloud-starter-gateway</artifactId>
    </dependency>

    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
      <scope>test</scope>
    </dependency>
  </dependencies>

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
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
```

错误说明：

```java
Description:

The bean 'hiddenHttpMethodFilter', defined in class path resource [org/springframework/cloud/gateway/config/GatewayAutoConfiguration.class], could not be registered. A bean with that name has already been defined in class path resource [org/springframework/boot/autoconfigure/web/reactive/WebFluxAutoConfiguration.class] and overriding is disabled.

Action:

Consider renaming one of the beans or enabling overriding by setting spring.main.allow-bean-definition-overriding=true
```

解决方式：

降低spring-boot-starter-parent版本，由2.1.x版本降级到2.0.x版本即可正常运行，适应spring cloud的版本



### 项目案例

这是我自己的练手项目，感兴趣的可以看一下

gitHub：<https://github.com/ZhangWeiSep/springCloudDemo>

