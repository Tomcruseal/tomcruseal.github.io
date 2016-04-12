---
layout: post
title: Java RMI编程笔记
categories: [Java]
tags: []
fullview: true
comments: true
---
>今天第一次完成Java的RMI编程，中途颇有曲折，遂著文以记之。
>
>Java RMI(Remote Method Invocation)即远程方法调用，客户端调用的方法在远程计算机上，属于分布式对象系统。RMI源于远程过程调用（RPC），在RPC范型中，过程调用从一个进程向另一个进程发出，被调用进程可驻留于远程系统，其中的数据作为参数传递。进程接收到调用后，执行该过程中的动作，调用者在调用结束时被通告，如果存在返回值，则该值将从被调用者传递到调用者。
>Java RMI 体系结构：
* 1 传输层
* 2 远程引用层
* 3 Stub/Skeleton(今天做的时候Skeleton并没有出现，因为是Java2平台)
>
>接下来是RMI 应用构建的流程，我之前没有遵循该流程，加上对Java语言不是很熟悉，所以走了一些弯路。
* 1写接口**DaytimeInterface.java**并编译
* 2构建接口的实现**DaytimeImpl.java**并编译，其中有从服务器上获取时间的函数**returnTime()**
* 3 **rmic DaytimeImpl**以生成Stub
* 4构建服务器端**DaytimeServer.java**
* 5将接口和Stub复制一份到客户端,并且在客户端编译接口
* 6构建客户端程序**DaytimeClient.java**
>
>其实也没有什么可以写的了，下面是一些教训呵：
>
* 1对远程对象进行注册时要加上注册名，不然会报**NullPointerException**错误

{% highlight java linenos %}
String registryURL="rmi://"+hostName+":"+portNum+"/some";
{% endhighlight %}

* 2如果是在本地调试，客户端文件与服务器端文件应分别放在两个不同的文件夹。
* 3有时候测试会出现奇奇怪怪的错误，比如在win8.1下和Ubuntu12.04下运行结果不同，比如站着运行和坐着运行也会有不同的结果：-）
* 4Ubuntu12.04有自带的截图软件——screenshot
* 5Java的异常捕捉很重要！
>
>最后贴一贴核心代码吧，其实和书本上的很像，可是却debug了那么久
>
接口**DaytimeInterface.java**

{% highlight java linenos %}
import java.rmi.*;
public interface DaytimeInterface extends Remote{
public String returnTime(String name) throws RemoteException;
}
{% endhighlight %}

接口的实现**DaytimeImpl.java**

{% highlight java linenos %}
import java.rmi.*;
import java.rmi.server.*;
import java.util.Date;
public class DaytimeImpl extends UnicastRemoteObject implements DaytimeInterface{
public DaytimeImpl() throws RemoteException{
super();
}
public String returnTime(String name) throws RemoteException{
Date timestamp=new Date();
return "Day Time:"+timestamp.toString();
}
}
{% endhighlight %}

服务器端**DaytimeServer.java**

{% highlight java linenos %}
public class DaytimeServer{
public static void main(String[] args){
int serverPort=32000;
String registryURL;
try{
DaytimeImpl exportedObj=new DaytimeImpl();
startRegistry(serverPort);
registryURL="rmi://localhost:"+Integer.toString(serverPort)+"/some";
Naming.rebind(registryURL,exportedObj);
System.out.println("Server registered.Registry currently contains:");
listRegistry(registryURL);
System.out.println("Daytime Server ready:");
}catch(Exception e){
//System.out.println(e.getMessage());
System.out.println("Exception in DaytimeServer.main:"+e.getMessage());
}
}
{% endhighlight %}

其中有两个涉及到注册的方法**startRegistry()**和**listRegistry()**作用如方法名。

{% highlight java linenos %}
private static void startRegistry(int RMIPortNum) throws RemoteException{
try{
Registry registry=LocateRegistry.getRegistry(RMIPortNum);
registry.list();
}
catch(RemoteException ex){
System.out.println("RMI registry cannot be located at port"+RMIPortNum);
Registry registry=LocateRegistry.createRegistry(RMIPortNum);
System.out.println("RMI registry created at .port"+RMIPortNum);
}
}
{% endhighlight %}


{% highlight java linenos %}
private static void listRegistry(String registryURL) throws RemoteException,MalformedURLException{
System.out.println("Registry"+registryURL+"contains:");
String [] names=Naming.list(registryURL);
for(int i=0;i<names.length;i++){
System.out.println(names[i]);
}
}
{% endhighlight %}

客户端**DaytimeClient.java**

{% highlight java linenos %}
public class DaytimeClient{
public static void main(String args[]){
try{
int RMIPort;
String hostName;
InputStreamReader is=new InputStreamReader(System.in);
BufferedReader br=new BufferedReader(is);
System.out.println("Enter the RMIRegistry host name:");
hostName=br.readLine();
System.out.println("Enter the RMIRegistry port name:");
String portNum=br.readLine();
RMIPort=Integer.parseInt(portNum);
String registryURL="rmi://"+hostName+":"+portNum+"/some";
DaytimeInterface di=(DaytimeInterface) Naming.lookup(registryURL);
System.out.println("Lookup completed.");
Date timestamp=new Date();
String message=di.returnTime(timestamp.toString());
System.out.println("DaytimeClient:"+message);
}
catch(Exception e){
System.out.println("Exception in DaytimeClient:"+e);
}
}
}
{% endhighlight %}

每个文件都有导入类，概括起来大致是：

{% highlight java linenos %}
import java.rmi.*;
import java.io.*;
import java.rmi.registry.Registry;
import java.registry.LocateRegistry;
import java.net.*;
import java.server.*;
{% endhighlight %}

就这些了，java还得继续学。