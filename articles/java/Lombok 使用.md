# Lombok简介、使用、工作原理、优缺点

Lombok 项目是一个 Java 库，它会自动插入编辑器和构建工具中，Lombok 提供了一组有用的注解，用来消除 Java 类中的大量样板代码。

---

- [Lombok简介、使用、工作原理、优缺点](#lombok简介使用工作原理优缺点)
- [简介](#简介)
- [使用](#使用)
  - [添加maven依赖](#添加maven依赖)
  - [安装插件(可选)](#安装插件可选)
  - [示例](#示例)
  - [全部注解](#全部注解)
- [Lombok工作原理](#lombok工作原理)
- [Lombok的优缺点](#lombok的优缺点)
  - [解决编译时出错问题](#解决编译时出错问题)
- [网上常见质疑](#网上常见质疑)
- [参考](#参考)

---


# 简介

>  
> 官方介绍  
> 
> Project Lombok is a java library that automatically plugs into your editor and build tools, spicing up your java. Never write another getter or equals method again, with one annotation your class has a fully featured builder, automate your logging variables, and much more.

翻译之后就是：

Lombok 项目是一个 Java 库，它会自动插入您的编辑器和构建工具中，简化您的 Java 。 不需要再写另一个 getter、setter、toString 或 equals 方法，带有一个注释的您的类有一个功能全面的生成器，可以自动化您的日志记录变量，以及更多其他功能

[官网链接](https://www.projectlombok.org/)

# 使用

## 添加maven依赖

```java
<dependency>
  <groupId>org.projectlombok</groupId>
  <artifactId>lombok</artifactId>
  <version>1.18.16</version>
  <scope>provided</scope>
</dependency>
```

**注意：** 在这里 `scope` 要设置为 `provided`, 防止依赖传递给其他项目

## 安装插件(可选)

在开发过程中，一般还需要配合插件使用，在 IDEA 中需要安装 `Lombok` 插件即可

为什么要安装插件？

首先在不安装插件的情况下，代码是可以正常的编译和运行的。如果不安装插件，IDEA 不会自动提示 Lombok 在编译时才会生成的一些样板方法，同样 IDEA 在校验语法正确性的时候也会提示有问题，会有大面积报红的代码



## 示例

下面举两个栗子，看看使用 lombok 和不使用的区别

创建一个用户类

**不使用 Lombok**

```java
public class User {
  private Integer id;
  private Integer age;
  private String realName;

  public User() {
  }

  @Override
  public boolean equals(Object o) {
    if (this == o) {
      return true;
    }
    if (o == null || getClass() != o.getClass()) {
      return false;
    }

    User user = (User) o;

    if (!Objects.equals(id, user.id)) {
      return false;
    }
    if (!Objects.equals(age, user.age)) {
      return false;
    }
    return Objects.equals(realName, user.realName);
  }

  @Override
  public int hashCode() {
    int result = id != null ? id.hashCode() : 0;
    result = 31 * result + (age != null ? age.hashCode() : 0);
    result = 31 * result + (realName != null ? realName.hashCode() : 0);
    return result;
  }

  @Override
  public String toString() {
    return "User{" +
        "id=" + id +
        ", age=" + age +
        ", realName='" + realName + '\'' +
        '}';
  }

  public Integer getId() {
    return id;
  }

  public void setId(Integer id) {
    this.id = id;
  }

  public Integer getAge() {
    return age;
  }

  public void setAge(Integer age) {
    this.age = age;
  }

  public String getRealName() {
    return realName;
  }

  public void setRealName(String realName) {
    this.realName = realName;
  }
}
```

**使用Lombok**

```java
@Data
public class User {
  private Integer id;
  private String username;
  private Integer age;
}
```

使用 @Data 注解会在编译的时候自动生成以下模板代码:

- toString
- equals
- hashCode
- getter 不会对 final 属性生成
- setter 不会对 final 属性生成
- 必要参数的构造器

关于什么是必要参数下面会举例说明


## 全部注解

上面已经简单看了一下 `@Data` 注解，下面看下所有的可以用的注解

- **@NonNull** 注解在字段和构造器的参数上。注解在字段上，则在 setter, constructor 方法中加入判空，注意这里需要配合 @Setter、@RequiredArgsConstructor、@AllArgsConstructor 使用；注解在构造器方法参数上，则在构造的时候加入判空
- **@Cleanup** 注解在本地变量上。负责清理资源，当方法直接结束时，会调用 close 方法
- **@Setter** 注解在类或字段。注解在类时为所有字段生成setter方法，注解在字段上时只为该字段生成setter方法，同时可以指定生成的 setter 方法的访问级别
- **@Getter** 使用方法同 @Setter，区别在于生成的是 getter 方法
- **@ToString** 注解在类上。添加toString方法
- **@EqualsAndHashCode** 注解在类。生成hashCode和equals方法
- **@NoArgsConstructor** 注解在类。生成无参的构造方法。
- **@RequiredArgsConstructor** 注解在类。为类中需要特殊处理的字段生成构造方法，比如 final 和被 @NonNull 注解的字段。
- **@AllArgsConstructor** 注解在类，生成包含类中所有字段的构造方法。
- **@Data** 注解在类，生成setter/getter、equals、canEqual、hashCode、toString方法，如为final属性，则不会为该属性生成setter方法。
- **@Value** 注解在类和属性上。如果注解在类上在类实例创建后不可修改，即不会生成 setter 方法，这个会导致 `@Setter` 不起作用
- **@Builder** 注解在类上，生成构造器
- **@SneakyThrows**
- **@Synchronized** 注解在方法上，生成同步方法
- **@With**
- 日志相关: 注解在类，生成 log 常量，类似 private static final xxx log
  - **@Log** java.util.logging.Logger
  - **@CommonsLog** org.apache.commons.logging.Log
  - **@Flogger** com.google.common.flogger.FluentLogger
  - **@JBossLog** org.jboss.logging.Logger
  - **@Log4j** org.apache.log4j.Logger
  - **@Log4j2** org.apache.logging.log4j.Logger
  - **@Slf4j** org.slf4j.Logger
  - **@XSlf4j** org.slf4j.ext.XLogger

关于所有的注解可以查看 https://projectlombok.org/features/all

# Lombok工作原理

在Lombok使用的过程中，只需要添加相应的注解，无需再为此写任何代码。自动生成的代码到底是如何产生的呢？

核心之处就是对于注解的解析上。JDK5引入了注解的同时，也提供了两种解析方式。

- 运行时解析

运行时能够解析的注解，必须将@Retention设置为RUNTIME，这样就可以通过反射拿到该注解。java.lang.reflect反射包中提供了一个接口AnnotatedElement，该接口定义了获取注解信息的几个方法，Class、Constructor、Field、Method、Package等都实现了该接口，对反射熟悉的朋友应该都会很熟悉这种解析方式。

- 编译时解析

编译时解析有两种机制，分别简单描述下：

1）Annotation Processing Tool

apt自JDK5产生，JDK7已标记为过期，不推荐使用，JDK8中已彻底删除，自JDK6开始，可以使用Pluggable Annotation Processing API来替换它，apt被替换主要有2点原因：

- api都在com.sun.mirror非标准包下
- 没有集成到javac中，需要额外运行

2）Pluggable Annotation Processing API

[JSR 269](https://jcp.org/en/jsr/detail?id=269)自JDK6加入，作为apt的替代方案，它解决了apt的两个问题，javac在执行的时候会调用实现了该API的程序，这样我们就可以对编译器做一些增强，javac执行的过程如下： 

![lombok工作原理](https://raw.githubusercontent.com/JourWon/image/master/lombok/lombok%E5%B7%A5%E4%BD%9C%E5%8E%9F%E7%90%86.png)

Lombok本质上就是一个实现了“[JSR 269 API](https://www.jcp.org/en/jsr/detail?id=269)”的程序。在使用javac的过程中，它产生作用的具体流程如下：

1. javac对源代码进行分析，生成了一棵抽象语法树（AST）
2. 运行过程中调用实现了“JSR 269 API”的Lombok程序
3. 此时Lombok就对第一步骤得到的AST进行处理，找到@Data注解所在类对应的语法树（AST），然后修改该语法树（AST），增加getter和setter方法定义的相应树节点
4. javac使用修改后的抽象语法树（AST）生成字节码文件，即给class增加新的节点（代码块）

通过读Lombok源码，发现对应注解的实现都在HandleXXX中，比如@Getter注解的实现在HandleGetter.handle()。还有一些其它类库使用这种方式实现，比如[Google Auto](https://github.com/google/auto)、[Dagger](http://square.github.io/dagger/)等等。

# Lombok的优缺点

**优点：**

1. 能通过注解的形式自动生成构造器、getter/setter、equals、hashcode、toString等方法，提高了一定的开发效率
2. 让代码变得简洁，不用过多的去关注相应的方法
3. 属性做修改时，也简化了维护为这些属性所生成的getter/setter方法等

**缺点：**

1. 不支持多种参数构造器的重载
2. 虽然省去了手动创建getter/setter方法的麻烦，但大大降低了源代码的可读性和完整性，降低了阅读源代码的舒适度

## 解决编译时出错问题

编译时出错，可能是没有enable注解处理器。`Annotation Processors > Enable annotation processing`。设置完成之后程序正常运行。

# 网上常见质疑

# 参考

- https://projectlombok.org
- https://github.com/rzwitserloot/lombok
