这是文章中我所用到的案例代码，均以通过测试，如有任何问题欢迎到原文评论区留言👏
「原文链接」在此🔗

Rectangle.java
```java
package com.tianxiaoqi.bean;

public class Rectangle {
    private int width;
    private int length;

    public Rectangle() {
        System.out.println("Hello World!");
    }

// command + N: generate set method & toString()

    public void setWidth(int width) {
        this.width = width;
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

myTest.java
```java
package com.tianxiaoqi.test;

import com.tianxiaoqi.bean.Rectangle;
import org.springframework.context.ApplicationContext;
import org.springframework.context.support.ClassPathXmlApplicationContext;

public class myTest {
    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXmlApplicationContext("service.xml");
        Rectangle rect = context.getBean("rectangle", Rectangle.class);
        System.out.println(rect);
    }

//    public static void main(String[] args) {
//        Rectangle rect = new Rectangle();
//        rect.setLength(2);
//        rect.setWidth(3);
//        System.out.println(rect);
//    }
}
```

service.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        https://www.springframework.org/schema/beans/spring-beans.xsd">

    <!-- services -->

    <bean id="rectangle" class="com.tianxiaoqi.bean.Rectangle">
        <property name="width" value="2"/>
        <property name="length" value="3"/>
        <!-- additional collaborators and configuration for this bean go here -->
    </bean>

    <!-- more bean definitions for services go here -->

</beans>
```


