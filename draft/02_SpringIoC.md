


![](https://imgkr.cn-bj.ufileos.com/a212d2ee-72c4-4cd4-9dac-b0192f5bc390.png)



## 前言

「<span style="color:orangered;font-weight:bold;">上一篇文章</span>[](http://mp.weixin.qq.com/s?__biz=MzU5NzMzNDkxNw==&mid=100000157&idx=1&sn=6635b0d42c67782ba8668ae2f8ff4eed&chksm=7e544f774923c6610150595afaa1190c91e85cbabde65de02583870dd54d3c4b312d8b717338#rd)」我们对 Spring 有了初步的认识，而 Spring 全家桶中几乎所有组件都是依赖于 IoC 的。

刚开始听到 IoC，会觉得特别高大上，但其实掰开了很简单。

跟着我的脚步，一文带你吃透 IoC 原理。

文中代码和内容均已上传至我的 [Github](https://github.com/huiqit/SheIsSDEatNYC "我的 Github")，欢迎戳 `https://github.com/huiqit/SheIsSDEatNYC` 在电脑端阅读。

本文围绕 是何、为何、如何 来谈：

![](https://imgkr.cn-bj.ufileos.com/8c1bdc16-8924-44a8-a7a9-485b05f30a84.png)

## 是何

上一篇文章有同学问我在官网该看哪些内容，怎么找的，那今天的截图里都会有链接。

### 初识 IoC

根据上一篇文章我们说的，Spring 全家桶中最重要的几个项目都是基于 Spring Framework 的，所以我们就以 Spring Framework 为例来看[文档](https://spring.io/projects/spring-framework#learn. "Spring 官方文档")。

首先它的右侧有 Github 的链接，另外点到「LEARN」这里，就会看到各个版本的文档。

![](https://imgkr.cn-bj.ufileos.com/250d9842-6744-4a4b-be02-532a6e3cf30e.png)

那我们点「Reference Doc」，就能够看到它的一些模块的介绍：

（等下... 模块？什么是模块？这个问题下文回答。）

![](https://imgkr.cn-bj.ufileos.com/3b1979da-4226-49cf-aeb3-503759137416.png)

第一章 Overview，讲述它的历史、设计原理等等；

第二章 Core，包含了 IoC 容器，AOP 等等，那自然是讲 Spring 的核心了，要点进去好好看了。

![](https://imgkr.cn-bj.ufileos.com/993103cb-e1d2-4167-89b6-e1ecf9c5066b.png)

点进去之后发现了宝贵的学习资料，一切的 what, why, how 都可以在这里找到答案。

![](https://imgkr.cn-bj.ufileos.com/2e2a831a-0378-4a05-aaf4-4f0358a72cec.png)

这里很好的解释了大名鼎鼎的 IoC - Inversion of Control, 控制反转。

每次读都会有新的体会和收获。

我粗略的总结一下：控制反转就是把创建和管理 bean 的过程转移给了第三方。而这个第三方，就是 Spring IoC Container，对于 IoC 来说，最重要的就是**容器**。

容器负责创建、配置和管理 bean，也就是它管理着 bean 的生命，控制着 bean 的依赖注入。

通俗点讲，因为项目中每次创建对象是很麻烦的，所以我们使用 Spring IoC 容器来管理这些对象，需要的时候你就直接用，不用管它是怎么来的、什么时候要销毁，只管用就好了。

举个例子，就好像父母没时间管孩子，就把小朋友交给托管所，就安心的去上班而不用管孩子了。
托儿所，就是第三方容器，负责管理小朋友的吃喝玩乐；
父母，相当于程序员，只管接送孩子，不用管他们吃喝。

等下，`bean` 又是什么？

Bean 其实就是包装了的 Object，无论是控制反转还是依赖注入，它们的主语都是 object，而 bean 就是由第三方包装好了的 object。（想一下别人送礼物给你的时候都是要包装一下的，自己造的就免了。

### IoC 容器

既然说容器是 IoC 最重要的部分，那么 Spring 如何设计容器的呢？
还是回到官网，第二段有介绍哦：

![](https://imgkr.cn-bj.ufileos.com/a89308ff-0b53-48d7-a2ed-9d66649ec73d.png)

答：使用 `ApplicationContext`，它是 `BeanFactory` 的子类，更好的补充并实现了 `BeanFactory` 的。

`BeanFactory` 简单粗暴，可以理解为 HashMap：

- Key - bean name
- Value - bean object

但它一般只有 get, put 两个功能，所以称之为“低级容器”。

而 `ApplicationContext` 多了很多功能，因为它继承了多个接口，可称之为“高级容器”。在下文的搭建项目中，我们会使用它。

![](https://imgkr.cn-bj.ufileos.com/43669fce-a991-4ffa-be46-43b0ec3237fd.png)

`ApplicationContext` 的里面有两个具体的实现子类，用来读取配置配件的：

- `ClassPathXmlApplicationContext` - 从 class path 中加载配置文件，更常用一些；
- `FileSystemXmlApplicationContext` - 从本地文件中加载配置文件，不是很常用，如果再到 Linux 环境中，还要改路径，不是很方便。

当我们点开 `ClassPathXmlApplicationContext` 时，发现它并不是直接继承 `ApplicationContext` 的，它有很多层的依赖关系，每层的子类都是对父类的补充实现。

而再往上找，发现最上层的 class 回到了 `BeanFactory`，所以它非常重要。

要注意，Spring 中还有个 `FactoryBean`，两者并没有特别的关系，只是名字比较接近，所以不要弄混了顺序。

为了好理解 IoC，我们先来回顾一下不用 IoC 时写代码的过程。

### 深入理解 IoC

这里用经典 `class Rectangle` 来举例：

- 两个变量：长和宽
- 自动生成 `set()` 方法和 `toString()` 方法

注意 ⚠️：一定要生成 `set()` 方法，因为 Spring IoC 就是通过这个 `set()` 方法注入的；
`toString()` 方法是为了我们方便打印查看。

```java
public class Rectangle {
    private int width;
    private int length;

    public Rectangle() {
        System.out.println("Hello World!");
    }


    public void setWidth(int widTth) {
        this.width = widTth;
    }

    public void setLength(int length) {
        this.length = length;
    }

    @Override
    public String toString() {
        return "Rectangle{" +
                "width=" + width +
                ", length=" + length +
                '}';
    }
}

```

然后在 `test` 文件中手动用 `set()` 方法给变量赋值。

嗯，其实这个就是「解藕」的过程！

```java
public class MyTest {
  @Test
  public void myTest() {
    Rectangle rect = new Rectangle();
    rect.setLength(2);
    rect.setWidth(3);
    System.out.println(rect);
  }
}
```

其实这就是 IoC 给属性赋值的实现方法，我们把「创建对象的过程」转移给了 `set()` 方法，而不是靠自己去 `new`，就不是自己创建的了。

这里我所说的“自己创建”，指的是直接在对象内部来 `new`，是程序主动创建对象的正向的过程；
这里使用 `set()` 方法，是别人（test）给我的；
而 IoC 是用它的容器来创建、管理这些对象的，其实也是用的这个 `set()` 方法，不信，你把这个这个方法去掉或者改个名字试试？

#### 几个关键问题：

**何为控制，控制的是什么？**

答：是 bean 的创建、管理的权利，控制 bean 的整个生命周期。

**何为反转，反转了什么？**

答：把这个权利交给了 Spring 容器，而不是自己去控制，就是反转。
由之前的自己主动创建对象，变成现在被动接收别人给我们的对象的过程，这就是反转。

举个生活中的例子，主动投资和被动投资。

自己炒股、选股票的人就是主动投资，主动权掌握在自己的手中；
而买基金的人就是被动投资，把主动权交给了基金经理，除非你把这个基金卖了，否则具体选哪些投资产品都是基金经理决定的。

### 依赖注入

回到文档中，第二句话它说：`IoC is also known as DI`.

我们来谈谈 `dependency injection` - 依赖注入。

**何为依赖，依赖什么？**

程序运行需要依赖外部的资源，提供程序内对象的所需要的数据、资源。

**何为注入，注入什么？**

配置文件把资源从外部注入到内部，容器加载了外部的文件、对象、数据，然后把这些资源注入给程序内的对象，维护了程序内外对象之间的依赖关系。

所以说，控制反转是通过依赖注入实现的。
但是你品，你细品，它们是有差别的，像是`「从不同角度描述的同一件事」`：

- IoC 是设计思想，DI 是具体的实现方式；
- IoC 是理论，DI 是实践；

从而实现对象之间的解藕。

**当然，IoC 也可以通过其他的方式来实现，而 DI 只是 Spring 的选择。**

IoC 和 DI 也并非 Spring 框架提出来的，Spring 只是应用了这个设计思想和理念到自己的框架里去。

## 为何

那么为什么要用 IoC 这种思想呢？换句话说，IoC 能给我们带来什么好处？

答：解藕。

它把对象之间的依赖关系转成用配置文件来管理，由 Spring IoC Container 来管理。

在项目中，底层的实现都是由很多个对象组成的，对象之间彼此合作实现项目的业务逻辑。但是，很多很多对象紧密结合在一起，一旦有一方出问题了，必然会对其他对象有所影响，所以才有了解藕的这种设计思想。

![](https://imgkr.cn-bj.ufileos.com/45abda21-c33d-4aac-b854-3437cb4a58ac.png)

![](https://imgkr.cn-bj.ufileos.com/a6ebafbb-eb4c-49ad-8f60-8c697d69698d.png)

如上图所示，本来 ABCD 是互相关联在一起的，当加入第三方容器的管理之后，每个对象都和第三方法的 IoC 容器关联，彼此之间不再直接联系在一起了，没有了耦合关系，全部对象都交由容器来控制，降低了这些对象的亲密度，就叫“解藕”。


## 如何

最后到了实践部分，我们来真的搭建一个 Spring 项目，使用下 IoC 感受一下。

现在大都使用 `maven` 来构建项目，方便我们管理 jar 包；
但我这里先讲一下手动导入 jar 包的过程，中间会遇到很多问题，都是很好的学习机会。

在开始之前，我们先来看下图 - 大名鼎鼎的 Spring 模块图。
### Spring Framework 八大模块

![](https://imgkr.cn-bj.ufileos.com/a7586e1b-56c6-4416-a434-f8a9622349e4.png)

模块化的思想是 Spring 中非常重要的思想。

Spring 框架是一个分层架构，每个模块既可以单独使用，又可与其他模块联合使用。

每个「绿框」，对应一个模块，总共8个模块；
「黑色包」，表示要实现这个模块的 jar 包。

`Core Container`，我们刚才已经在文档里看到过了，就是 IoC 容器，是核心，可以看到它依赖于这4个 jar 包：

- `Beans` 
- `Core`
- `Context`
- `SpEL`, spring express language 

那这里我们就知道了，如果想要用 IoC 这个功能，需要把这 4个 jar 包导进去。
其中，Core 模块是 Spring 的核心，Spring 的所有功能都依赖于这个 jar 包，Core 主要是实现 IoC 功能，那么说白了 Spring 的所有功能都是借助于 IoC 实现的。


其他的模块和本文关系不大，不在这里展开了。

那当我们想搭建 Spring 项目时，当然可以把所有 jar 包都导进去，~~但是你的电脑能受得了吗。。~~ 但是包越大，项目越大，问题就越多，所以尽量按需选择，不用囤货。。


Btw, 这张图在网上有很多，但是在我却没有在最新版的 reference doc 上找到。。
不过，既然那些老的教程里有，说明老版本的 doc 里有，那去[老版本的介绍](https://docs.spring.io/spring/docs/4.3.26.RELEASE/spring-framework-reference/htmlsingle/ "官方文档 4.3.26 版本，包含 Spring 模块图") 里找找看😂

在本文第一张图 `Spring Framework` - `Documentation` 中我们选 `4.3.26` 的 `Reference Doc.`，然后搜索“`Framework Modules`”，就有啦～
具体链接可以看文末参考资料。

还有一个方法，待会我们讲到 jar 包中的内容时再说。

## 搭建 Spring 项目

知道要导入哪些 jar 包了，那就找吧😂。

### 一、手动加载 jar 包的方式
#### 1. 下载
下载地址：

![](https://imgkr.cn-bj.ufileos.com/c3df9c27-f7db-4ba4-a3bc-ca0c89244e68.png)

如果你要问我怎么找的，那就还是从刚才 `4.3.26` 版本的 `Reference Doc` 中进去，然后刚开头就有一个 `Distribution Zip Files`，


![](https://imgkr.cn-bj.ufileos.com/f943922a-e9d9-47ae-b405-61ab3f9e54be.png)


好奇心带着我打开了它，发现...

![](https://imgkr.cn-bj.ufileos.com/7be31807-3130-4638-9a2b-bc146a10b52b.png)

发现了仓库地址！

打开后发现是各个版本的 jar 包啊～

我们搜 5.2.3 版的，它在最下面：



![](https://imgkr.cn-bj.ufileos.com/eaadb652-8d83-42a9-8b47-3e1f7da73680.png)

然后就可以愉快的使用了～

- `Dist.zip` 是 jar 包
- `Docs.zip` 是文档

其他的暂时先不用管～

下载好了之后，就好好看看 Spring 送我们的这份大礼包吧。

此处回答上文的遗留问题：哪里找 Spring Framework 框架图。

答案是：``下载的 docs.zip → spring-framework-reference → images → spring-overview``



![](https://imgkr.cn-bj.ufileos.com/5eaf90f3-1cfd-430a-a50c-058e1f08b96d.png)


我们需要导入 Intellij 的 jar 包在哪里呢？
Dist.zip → libs



![](https://imgkr.cn-bj.ufileos.com/127a8075-f259-45b1-9f13-9062a3ec4662.png)



这里可以看到，每个黑色框对应3个 jar 包，我们要导入 Intellij 的是 `RELEASE.jar`.

#### 2. 不用 IoC 构建项目
我们 `new project`，不用 maven 构架，就新建一个普通的 Java 项目，比如我就叫它 `Spring_HelloWorld`，然后还是用我常用的 `class Rectangle` 的例子。

然后在 External Libraries 中导入我们刚才在模块图里看到的那4个模块所对应的 jar 包，结构如下：


![](https://imgkr.cn-bj.ufileos.com/ee777ed3-3b94-4b89-b227-c6eee288e13c.png)

这样你以为就大功告成了吗？Too young too simple 啊～

来运行一下：


![](https://imgkr.cn-bj.ufileos.com/9b84a5dc-89c1-4989-8fc9-7a85527fe382.png)


出现了老盆友：`no class def found error`, 就是找不到这个类。

我们谷歌 `Maven common logging` 并下载它的 jar 包，再加到项目里就可以了。

我上图里是已经加过了的，所以你会看到一个 `commons-logging-1.2`.

再运行一下就可以了。这里的两个文件上文都有截图。

目前为止我们是手动用 `set()` 方法设置对象的，那怎么用 Spring IoC 呢？

#### 3. Spring IoC 配置文件详解

还需要有一个配置文件，可是这个文件需要配置啥，该怎么配置呢？

官网里都给我们写好了：

![](https://imgkr.cn-bj.ufileos.com/ce12fa95-5780-4a56-bd7c-b023c44a703e.png)


第一段是一些命名空间及其规范的介绍，

第二段就是给 `bean` 的属性赋值了。

这里注意下 `bean` 里面的配置要改一下，改成我们这个项目对应的。
这里的 `id`, `class` 是什么意思呢？官网上也有解释，我这里简单概括下：

- `bean` 标签：告诉 Spring 要创建的对象
- `id`: 对象的唯一标识，就像每个人的身份证一样，不可重复
- `class`: `bean` 的完全限定名，即从 package name 到 class name
- `property`：给属性赋值，`name` 的名称取决于 `set()` 方法后面的参数；

其实也可以用 constructor 来赋值，name 的名称取决于参数列表；
更多给复杂数据类型赋值的使用可以在官网查到。

当然，在工作中更常用的是注解。但是往往也会有 xml 文件配合着一起使用的，所以还是要懂的。

我的 service 文件配置如下：


![](https://imgkr.cn-bj.ufileos.com/4bb76d4f-b8e7-4daa-90f5-3ff292fce9b4.png)

#### 4. 最后一步，我们再来看它是怎么用的：


![](https://imgkr.cn-bj.ufileos.com/2bb3582b-d030-4aea-bc6e-68ffb4d62869.png)

这里面并没有直接的 new 这个 service，但是 Spring 容器帮我们创建了这个对象。

那么 Spring 是如何帮我们创建对象的呢？

`ApplicationContext` 是 `IoC 容器`的入口，其实也就是 `Spring 程序`的入口，
刚才已经说过了它的两个具体的实现子类，在这里用了从 class path 中读取数据的方式；

然后第二行，就是获取具体的 bean 了。这个其实有很多方式，在使用的时候就能看到：


![](https://imgkr.cn-bj.ufileos.com/71a1602a-de87-4971-944f-a85a8cb50cf8.png)

点进去发现，是在 BeanFactory.class 里定义的：


![](https://imgkr.cn-bj.ufileos.com/6052fe7d-cb14-43fe-899a-f390459776e1.png)


这其中比较常用的是通过

- Id  → 需要 cast 
- Bean 的类型 → 只能在 Singleton 的时候使用，否则不知道用哪个呀
- Id + 类型 → 下图代码示例

来获取对象，最后两种 String, Class objects 这种可变参数的方式用的很少。


照猫画虎，我的 test 文件改动如下：




![](https://imgkr.cn-bj.ufileos.com/6b236ebb-f423-416c-9b1b-427cdd25a855.png)





成功运行～～🎉🎉


##### Follow up 1. 对象在容器中默认是单例的

实践是检验的唯一标准：

再用 `getBean()` 得到一个对象，测试是否还是同一个。

即：
```java
public class MyTest {
  public void test myTest() {
    ApplicationContext context = new ClassPathXmlApplicationContext("service.xml");
        Rectangle rect = context.getBean("rectangle", Rectangle.class);
        Rectangle rect2 = context.getBean("rectangle", Rectangle.class);
        System.out.println(rect == rect2);
    }
  }
}
```
返回 True or False? 

答：True

因为默认是单例的，如果要改，需要在配置文件里改` <bean … scope = “prototype”>`.

至于这些标签的用法，这里不再延伸了～

##### Follow up 2. 容器中的对象是什么时候创建的？

实践是检验的唯一标准：

定义一个无参的 constructor，里面打印一句话，然后只 `new ClassPathXmlApplicationContext`，如下图：




![](https://imgkr.cn-bj.ufileos.com/86ed27f8-843f-42ce-bfe7-8ccf9087f44d.png)



发现也是可以打印的，所以其实是每次启动容器的时候，就已经创建好容器中的所有对象了。（当然，这在 `scope = "prototype"` 的时候不适用，只是 singleton 的时候。）

多说一句，其实最好应该一直保留一个无参的 constructor，因为这里 bean 对象的创建是通过反射，
- `clazz.newInstance()` 默认是调用无参的 constructor

不过，现在已经被弃用掉了，换用了这个：

- `clazz.getDeclaredConstructor().newInstance()`

### 二、使用 Maven 构建项目

我们再回到最开始的构建项目，相信大家都体会到了手动导入 jar 包的繁琐之处，其实我们还可以用 Maven 来管理项目中的 jar 包，在公司中也是比较常用的一种方式，免除了手动下载 jar 包的过程。

#### 1. 新建项目

使用 Maven 的话就简化很多了，首先我们创建一个 Maven 项目，不同于刚才的过程在于： 

New Project 的时候要选择从 Maven 构建，而不是一个简单的 Java 项目。


![](https://imgkr.cn-bj.ufileos.com/66bf2ee3-a408-4ab9-869d-32968eac388c.png)


建好之后，我们会发现比起刚才的 Java 项目，多了很多东西：

和之前的空项目不太一样，这里有 `main`, `test`，其中 `resources` 是放配置文件的地方，也就是我们刚才的 `service.xml` 应该放在这里，如果没有放对位置是代码找不到哦～


![](https://imgkr.cn-bj.ufileos.com/592c4694-eb1e-4d6c-9f12-0a08ededa74b.png)

#### 2. 添加对应的 pom 依赖，就不用手动导 jar 包了
1. 仓库地址 `https://mvnrepository.com/`

2. 搜 `spring`

3. 选择 `Spring context` → `5.2.3 release`，把里面的配置 copy 到 `pom.xml` 中

最终在左边 external libraries 会自动出现所需的包，一键导入，不要太方便～

#### 3. 写代码～～🎉🎉

## 小结


我们最后再来体会一下用 Spring 创建对象的过程：

通过 `ApplicationContext` 这个 IoC 容器的入口，用它的两个具体的实现子类，从 class path 或者 file path 中读取数据，用 `getBean()` 获取具体的 bean instance。

那使用 Spring 到底省略了我们什么工作？

答：`new 的过程`。把 new 的过程交给第三方来创建、管理，这就是「解藕」。


![](https://imgkr.cn-bj.ufileos.com/f4fe6b26-915e-4472-bf93-30642f92396c.png)

Spring 也是用的 `set()` 方法，它只不过提供了一套更加完善的实现机制而已。

而说到底，底层的原理并没有很复杂，只是为了提高扩展性、兼容性，Spring 提供了丰富的支持，所以才觉得源码比较难。

因为框架是要给各种各样的用户来使用的，它们考虑的更多的是扩展性。如果让我们来实现，或许三五行就能搞定，但是我们实现的不完善、不完整、不严谨，总之不高大上，所以它写三五十行，把框架设计的尽可能的完善，提供了丰富的支持，满足不同用户的需求，才能占领更大的市场啊。



