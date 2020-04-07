

![](https://imgkr.cn-bj.ufileos.com/ec5e9feb-0f28-4c5f-ab86-cea79eb0614c.png)



  ## 前言
  Spring 是什么？

  简言之，<span style="color:blue;font-weight:bold;">Spring 是一个开发应用框架。

  在企业里的开发不是从零开始一点点敲进去的，而是往框架里填充内容。

  这就好像我们在 Leetcode 做题时，不需要写 signature，而是直接往里面写函数内容，这也算是个简陋的框架。

  扩充开来：<span style="color:blue;font-weight:bold;">Spring 是一个开源的、轻量级的、Java SE/EE 的开发应用框架。

  - 重和轻，都是相对而言
  - Jave SE 的框架有很多，也是在不断迭代更新的，下文会阐述它们的更新之路。

  那么<span style="color:blue;font-weight:bold;">何为重何为轻？
  
  在使用时，有过多的接口和依赖，侵入性比较强，就叫重。

  那么什么是<span style="color:blue;font-weight:bold;">侵入性？
  
  在写代码的时候，需要去继承或者实现当前框架里很多 class/interface，这就是侵入性比较强。侵入性强也就是和这个框架的耦合度比较高，开发时会比较麻烦。
  
  至于 Spring 这个架子搭了哪些内容，搭成了什么样子，本文不会展开，本文会站在一个 high level 的角度帮助大家建立整体的感知，了解 Java 框架的发展历史，拓宽我们的技术视野，具体内容我们之后慢慢来讲。
  
  ## 为什么要用 Spring
  
  任何一项技术的产生都是为了解决现有的问题的，框架也不例外。
  
  Spring 这个框架，提供了项目开发中的一些基础功能，可以使我们程序员更专注于业务逻辑的处理，具体来说：
  - Spring IoC 容器管理了对象的生命周期和配置，不再使用 new 的方式创建对象，而是使用依赖注入，降低了组件之间的耦合度；
  - AOP 是 Spring 的核心，用来将一些通用的事务进行统一管理，常用于处理日志、安全管理和事务管理等方面。
  - Spring 并不强制项目完全依赖于 Spring，开发者可以自由选用 Spring 框架的部分来使用。比如我司的框架就是基于 Spring 来做的开发，在 Spring 的框架之上，可以添加我司自己的安全管理等等。
  
  ## 什么是架构
  我们一直在说框架、架构，那它到底是什么呢？
  从 dubbo 的官网，
  

  
  
  



