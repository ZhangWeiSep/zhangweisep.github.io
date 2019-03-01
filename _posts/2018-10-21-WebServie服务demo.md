---
layout:     post
title:      WebService服务demo
subtitle:   WebService服务demo
date:       2018-10-21
author:     逍遥
header-img: img/post-bg-debug.png
catalog: true
tags:
    - WebService
---
# WebService服务demo

### demo环境

操作系统：windows 10 x64

JDK：jdk1.8.0_172

开发工具：intellij idea 2018.3.3 x64

项目容器：Tomcat 7.0.90

### 服务端

这里分别提供两种服务发布方式

1：普通java工程

2：Web工程

#### 普通java工程

##### 新建测试接口

```java
package pojoService;

import javax.jws.WebMethod;
import javax.jws.WebParam;
import javax.jws.WebResult;
import javax.jws.WebService;
import javax.jws.soap.SOAPBinding;

/**
 * @Filename: HelloWorldService
 * @Author: Zhang Wei
 * @Date: 2019/2/12 14:16
 * @Description: 服务接口
 * @History:
 */
@WebService
@SOAPBinding(style = SOAPBinding.Style.RPC)
public interface HelloWorldService {

    @WebMethod
    @WebResult(name = "resultStr")
    String sayHelloWorldFrom(@WebParam(name = "from") String from);

}

```

##### 编写实现类

```java
package pojoService;

import javax.jws.WebService;
import javax.jws.soap.SOAPBinding;

/**
 * @Filename: HelloWorldImpl
 * @Author: Zhang Wei
 * @Date: 2019/2/12 10:11
 * @Description: 服务实现类
 */
@WebService(endpointInterface = "pojoService.HelloWorldService")
@SOAPBinding(style = SOAPBinding.Style.RPC)
public class HelloWorldImpl implements HelloWorldService {

    @Override
    public String sayHelloWorldFrom(String from) {
        String result = "Hello, world, from " + from;
        System.out.println(result);
        return result;
    }
}
```

##### 发布WebService服务

```java
package pojoService;

import javax.xml.ws.Endpoint;

/**
 * @Filename: HelloWorldServer
 * @Author: Zhang Wei
 * @Date: 2019/2/12 14:22
 * @Description: 普通java工程发布服务（启动后不能停止，否则服务一起停止）
 * @History:
 */
public class HelloWorldServer {

    public static void main(String[] args) {
        //发布服务
        Endpoint.publish("http://localhost:8086/services/HelloWorld?wsdl", new HelloWorldImpl());
    }

}
```



#### Web工程

##### 新建WebService工程

打开idea工具，在顶部导航栏中找到“File” —> "New" —>“Project”，在打开的窗口左侧工程类型里选择Spring，在右侧的	详细工程栏里选择“Java EE”下的WebServices工程，选择完成后再下方勾选“Generate sample server code”，Version一栏里选择“Apache Axis”，点击“next”，在“Project name”栏中输入工程名，点击“finish”创建完成。

因为我们勾选了“Generate sample server code”，所以生成的工程会自带一个demo

```java
public class HelloWorld {
  public String sayHelloWorldFrom(String from) {
    String result = "Hello, world, from " + from;
    System.out.println(result);
    return result;
  }
}
```

可以在工程目录“web”—>“WEB_INF”下看到两个文件

server-config.wsdd：WebService服务配置文件

web.xml：web工程配置文件

打开“server-config.wsdd”文件里面有这样的一份配置信息

```xml
<service name="HelloWorld" provider="java:RPC" style="document" use="literal">
    <parameter name="className" value="example.HelloWorld"/>
    <parameter name="allowedMethods" value="*"/>
    <parameter name="scope" value="Application"/>
    <namespace>http://example</namespace>
</service>
```

这里就是对外发布的服务配置信息

##### 启动工程（发布服务）

把新建的WebService工程打包放入Tomcat容器中启动

访问地址查看是否生成了wsdl

默认地址：http://localhost:8080/WebServiceSampleDemo_war_exploded/services/HelloWorldTo?wsdl

80端口和services之间的“WebServiceSampleDemo_war_exploded”是自动生成的名称，可以手动在idea的tomcat配置里更改，下面是删掉这部分内容的地址

http://localhost:8080/services/HelloWorldTo?wsdl

打开浏览器输入以上地址查看是否能显示wsdl文件内容

如果正常显示以下内容则表示WebService服务已经发布成功

```xml
<wsdl:definitions xmlns:apachesoap="http://xml.apache.org/xml-soap" xmlns:impl="http://example"
                  xmlns:intf="http://example" xmlns:wsdl="http://schemas.xmlsoap.org/wsdl/"
                  xmlns:wsdlsoap="http://schemas.xmlsoap.org/wsdl/soap/" xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                  targetNamespace="http://example">
  <!--
 WSDL created by Apache Axis version: 1.4
 Built on Apr 22, 2006 (06:55:48 PDT)
 -->
  <wsdl:types>
    <schema xmlns="http://www.w3.org/2001/XMLSchema" elementFormDefault="qualified" targetNamespace="http://example">
      <element name="from" type="xsd:string"/>
      <element name="sayHelloWorldFromReturn" type="xsd:string"/>
    </schema>
  </wsdl:types>
  <wsdl:message name="sayHelloWorldFromResponse">
    <wsdl:part element="impl:sayHelloWorldFromReturn" name="sayHelloWorldFromReturn"/>
  </wsdl:message>
  <wsdl:message name="sayHelloWorldFromRequest">
    <wsdl:part element="impl:from" name="from"/>
  </wsdl:message>
  <wsdl:portType name="HelloWorldTo">
    <wsdl:operation name="sayHelloWorldFrom" parameterOrder="from">
      <wsdl:input message="impl:sayHelloWorldFromRequest" name="sayHelloWorldFromRequest"/>
      <wsdl:output message="impl:sayHelloWorldFromResponse" name="sayHelloWorldFromResponse"/>
    </wsdl:operation>
  </wsdl:portType>
  <wsdl:binding name="HelloWorldToSoapBinding" type="impl:HelloWorldTo">
    <wsdlsoap:binding style="document" transport="http://schemas.xmlsoap.org/soap/http"/>
    <wsdl:operation name="sayHelloWorldFrom">
      <wsdlsoap:operation soapAction=""/>
      <wsdl:input name="sayHelloWorldFromRequest">
        <wsdlsoap:body use="literal"/>
      </wsdl:input>
      <wsdl:output name="sayHelloWorldFromResponse">
        <wsdlsoap:body use="literal"/>
      </wsdl:output>
    </wsdl:operation>
  </wsdl:binding>
  <wsdl:service name="HelloWorldToService">
    <wsdl:port binding="impl:HelloWorldToSoapBinding" name="HelloWorldTo">
      <wsdlsoap:address location="http://localhost:8080/WebServiceSampleDemo_war_exploded/services/HelloWorldTo"/>
    </wsdl:port>
  </wsdl:service>
</wsdl:definitions>
```

### 客户端

#### 1.使用jdk自带的工具wsimport生成客户端

使用浏览器打开wsdl地址，选择另存为把wsdl文件下载下来，这里以下载到桌面为例

然后打开cmd命令提示符窗口进入桌面目录，输入以下命令

```cmd
C:\Users\Zhang Wei\Desktop>wsimport HelloWorld.xml -p com.jidd.ws -s D:\WebServiceTest\ -d D:\WebServiceTest\src\
```

-p：指定目标程序包

-s：指定生成的源文件位置

-d：指定生成的class文件位置

HelloWorld.xml：下载好的wsdl文件

注意：-s、-d指定的文件位置需手动创建好，命令不会自动创建目录

使用wsimport读取本地wsdl文件生成的源文件中，wsdl文件的指向地址也是本地路径，可手动更改为URL地址

修改HelloWorldService.java文件

修改头部注解

```java
/**
* 读取本地路径修改为网络路径
*/
//@WebServiceClient(name = "HelloWorldService", targetNamespace = "http://example", wsdlLocation = "file:/C:/Users/Zhang%20Wei/Desktop/HelloWorld.xml")
@WebServiceClient(name = "HelloWorldService", targetNamespace = "http://example", wsdlLocation = "http://localhost:8080/services/HelloWorld?wsdl")
```

修改静态块

```java
static {
        URL url = null;
        WebServiceException e = null;
        try {
            //读取本地路径修改为网络路径
            //url = new URL("file:/C:/Users/Zhang%20Wei/Desktop/HelloWorld.xml");
            url = new URL("http://localhost:8080/services/HelloWorld?wsdl");
        } catch (MalformedURLException ex) {
            e = new WebServiceException(ex);
        }
        HELLOWORLDSERVICE_WSDL_LOCATION = url;
        HELLOWORLDSERVICE_EXCEPTION = e;
}
```

出现如下过程则解析完毕

```cmd
C:\Users\Zhang Wei\Desktop>wsimport HelloWorld.xml -p com.jidd.ws -s D:\WebServiceTest\ -d D:\WebServiceTest\src\
正在解析 WSDL...

正在生成代码...

正在编译代码...
C:\Users\Zhang Wei\Desktop>
```

#### 2.使用开发工具提供的快捷方式生成

在需要生成文件的文件夹上右键选择“WebServices”—> “Generate Java Code From Wsdl”

在弹出的窗口“Web service esdl url”栏中填入服务端的wsdl地址，然后确定会生成一系列文件

我在service中选择生成文件，目录如下

*  service
    > HelloWorld.wsdl
    > HelloWorld_PortType
    > HelloWorldService
    > HelloWorldServiceLocator
    > HelloWorldSoapBindingStub

### 测试类

#### 普通java工程

```java
import service.HelloWorldImplServiceLocator;
import service.HelloWorldService;

public class HelloWorldClient {

    public static void main(String[] argv) {
        helloMain();
    }

    public static void helloMain() {
        try {
            HelloWorldImplServiceLocator locator = new HelloWorldImplServiceLocator();
            HelloWorldService service = locator.getHelloWorldImplPort();
            //调用服务
            String result = service.sayHelloWorldFrom("csssss");
            //打印返回结果
            System.out.println(result);
        } catch (javax.xml.rpc.ServiceException ex) {
            ex.printStackTrace();
        } catch (java.rmi.RemoteException ex) {
            ex.printStackTrace();
        }
    }
}
```

#### Web工程

```java
import service.HelloWorldServiceLocator;
import service.HelloWorld_PortType;

public class HelloWorldClient {

    public static void main(String[] argv) {
        helloMain();
    }

    public static void helloMain() {
        try {
            HelloWorldServiceLocator locator = new HelloWorldServiceLocator();
            HelloWorld_PortType service = locator.getHelloWorld();
            //调用服务
            String result = service.sayHelloWorldFrom("csssss");
            //打印返回结果
            System.out.println(result);
        } catch (javax.xml.rpc.ServiceException ex) {
            ex.printStackTrace();
        } catch (java.rmi.RemoteException ex) {
            ex.printStackTrace();
        }
    }
}
```

#### 使用“wsimport”命令编译的web工程测试类

```java
import service.HelloWorld;
import service.HelloWorldService;

/**
 * @Filename: HelloWorldClient
 * @Author: Zhang Wei
 * @Date: 2019/2/12 14:00
 * @Description:
 * @History:
 */
public class HelloWorldClient {

    public static void main(String[] argv) {
        helloMain();
    }

    public static void helloMain() {
        HelloWorldService helloWorldService = new HelloWorldService();
        HelloWorld helloWorld = helloWorldService.getHelloWorld();
        String str = helloWorld.sayHelloWorldFrom("测试");
        System.out.println(str);
    }
}
```

