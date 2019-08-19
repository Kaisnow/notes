# Servlet

### Servlet是用来干嘛的



**servlet为包含5个方法的接口**

![img](https://pic2.zhimg.com/80/v2-85bf84640fbc6b6e195b9c5b513b918f_hd.jpg)



**网络协议、http什么的，servlet根本不管！也管不着！那是tomcat的事情**



但是不同的处理请求需要不同的操作，所以servlet用来处理不同的请求。由程序员来编写

 随着后期互联网发展，出现了三层架构，所以一些逻辑就从Servlet抽取出来，分担到Service和Dao。![img](https://pic2.zhimg.com/80/v2-f41587429ebde63225029b0c235960c1_hd.jpg)

等到springmvc出来后servlet就退居到幕后了，导致对servlet也是一知半解，servlet和springmvc的关系也是迷迷糊糊



### Servlet的继承类



​	![img](https://pic2.zhimg.com/80/v2-cbb35ec0c92292ea599108e643fd5183_hd.jpg)



接口的所有参数都是tomcat为我们做好了传给我们了。

**ServletConfig**

servlet配置  web.xml中存放的数据  一些参数信息

**Request/Response**

请求中包含了请求头（Header），请求地址（URL），请求参数（QueryString），还有用户输入的数据等

响应中啥都没有，通过respons调用一些方法写入数据返回给浏览器。

 

我们主要的业务代码需要写在service中，而servlet是一个接口方法没有实现我们查看他的继承类



**GenericServlet**

![img](https://pic1.zhimg.com/80/v2-b9f65e77009de2832d721cb28d5ae6f1_hd.jpg)

然后HttpServlet又继承了GenericServlet抽象类



### **HttpServlet**

![img](https://pic3.zhimg.com/80/v2-869e7ef9c2c7aaf4b90572ca140bd1b4_hd.jpg)

Httpservlet是一个抽象类，但是实现了service方法

![img](https://pic2.zhimg.com/80/v2-73b703e690ce018ffe88280376a67dc0_hd.jpg)

我们发现HttpServlet中的service为我们判断了请求方法，

并且做出了对应处理doGet和doPost方法



![img](https://pic1.zhimg.com/80/v2-fb86edf9aaac3049028e25fa022a9811_hd.jpg)

而默认的doGet和doPost方法是返回400、405错误。



所以我们程序员应该创建一个类并且继承HttpServlet抽象类，然后重写doGet或者doPost方法。这样也方便创建不同的类处理不同的请求

 

### ServletContext



#### ServletContext是什么

ServletContext，直译的话叫做“Servlet上下文”。它其实就是个大容器，是个map。服务器会为每个应用创建一个ServletContext对象：



- ServletContext对象的创建是在服务器启动时完成的
- ServletContext对象的销毁是在服务器关闭时完成的





![img](https://pic2.zhimg.com/80/v2-40ed984999cab23bc4e9e17a39e84839_hd.jpg)

ServletContext对象的作用是在整个Web应用的动态资源（Servlet/JSP）之间共享数据。例如在AServlet中向ServletContext对象保存一个值，然后在BServlet中就可以获取这个值。



这种用来装载共享数据的对象，在JavaWeb中共有4个，而且更习惯被成为“域对象”：

- ServletContext域（Servlet间共享数据）
- Session域（一次会话间共享数据，也可以理解为多次请求间共享数据）
- Request域（同一次请求共享数据）
- Page域（JSP页面内共享数据）

它们都可以看做是map，都有getAttribute()/setAttribute()方法。



#### 如何获取ServletContext

还记得GenericServlet吗？它在init方法中，将Tomcat传入的ServletConfig对象的作用域由局部变量（方法内使用）提升到成员变量。并且新建了一个getServletContext()：![img](https://pic4.zhimg.com/80/v2-e4fc1f0a49a67b59256b4935a47ac913_hd.jpg)



也就是说ServletConfig对象可以得到ServletContext对象。但是这并不意味这ServletConfig对象包含着ServletContext对象，而是ServletConfig维系着ServletContext的引用。

另外，Session域和Request域也可以得到ServletContext

```
session.getServletContext();
request.getServletContext();
```

### Filter拦截方式

REQUEST/FORWARD/INCLUDE/ERROR

其实配置Filter时可以设置4种拦截方式：

![img](https://pic2.zhimg.com/80/v2-bbd582e840cac5e82eda33bd91cf60bd_hd.jpg)

这里，我先帮大家把这4种方式和重定向（Redirect）剥离开，免得有人搞混，它们是完全两类（前者和Request有关，后者通过Response发起），以FORWARD为例：

![img](https://pic2.zhimg.com/80/v2-70f251139437bb33e2f7a93dfa89c135_hd.jpg)Redirect和REQUEST/FORWARD/INCLUDE/ERROR最大区别在于：

- 重定向会导致浏览器发送**2**次请求，FORWARD们是服务器内部的**1**次请求

所谓Filter更详细的拦截其实是这样：![img](https://pic3.zhimg.com/80/v2-1d6b0e77752d60f39d829ad39a4a630a_hd.jpg)

**灰色块：Filter**



### DispatcherServlet

####  

### conf/web.xml与应用的web.xml



