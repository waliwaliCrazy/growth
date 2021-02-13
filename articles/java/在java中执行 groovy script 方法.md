<h1> 在 Java 中执行动态表达式语句 </h1>

在一些规则集或者工作流项目中，经常会遇到动态解析表达式并执行得出结果的功能。

规则引擎是一种嵌入在应用程序中的组件，它可以将业务规则从业务代码中剥离出来，使用预先定义好的语义规范来实现这些剥离出来的业务规则；规则引擎通过接受输入的数据，进行业务规则的评估，并做出业务决策。

工作流（Workflow），是对工作流程及其各操作步骤之间业务规则的抽象、概括描述。 工作流建模，即将工作流程中的工作如何前后组织在一起的逻辑和规则，在计算机中以恰当的模型表達并对其实施计算。 工作流要解决的主要问题是：为实现某个业务目标，利用计算机在多个参与者之间按某种预定规则自动传递文档、信息或者任务。

---

**Table of Contents**

- [前缀、中缀、后缀表达式(逆波兰表达式)](#前缀中缀后缀表达式逆波兰表达式)
- [Ognl](#ognl)
- [SpEl](#spel)
- [Jexl/Jexl3](#jexljexl3)
- [Groovy](#groovy)
  - [执行表达式](#执行表达式)

---

# 前缀、中缀、后缀表达式(逆波兰表达式)

最早接触的表达式解析是在上数据结构的时候，当时课设作业是 “ 做一个简单的四则混合运算语句解析并计算结果 ”，简单说就是计算器。


# Ognl

# SpEl

# Jexl/Jexl3

# Groovy

Groovy 是一个很好的选择，其具备完备的 Groovy 和 Java 语法的解析执行功能。

引入依赖, 这个可以根据需要引入最新版本

```xml
<!-- https://mvnrepository.com/artifact/org.codehaus.groovy/groovy -->
<dependency>
    <groupId>org.codehaus.groovy</groupId>
    <artifactId>groovy</artifactId>
    <version>2.5.6</version>
</dependency>
```

## 执行表达式

```java
Binding binding = new Binding();
binding.setVariable("verifyStatus", 1);
GroovyShell shell = new GroovyShell(binding);
boolean result = (boolean) shell.evaluate("verifyStatus == 1");
```
