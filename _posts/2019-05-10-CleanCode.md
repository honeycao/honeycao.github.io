---
layout:     post
title:      读书《代码整洁之道》
subtitle:   一本让你写出更整洁、高效代码的书。
date:       2019-05-10
author:     "愚者"
header-img: "img/post-bg-readbook.jpg"
catalog: true
tags:
    - 读书
---

读《代码整洁之道》的必要性：

* 代码关乎沟通，而沟通是专业开发者的头等大事

* 代码不仅仅能运行，还需要健壮与整洁

* 光代码写得好不够，必须时时保持代码整洁（童子军军规：**让营地比你来时更干净**~）



本书涉及的内容很多，实例都是以java展开，不一定适合所有的语言，因此简单总结了下比较通用的知识点。不一定完全适合每个人，但每个人至少有适合的一条。

---

#### 命名

1. 名副其实
   * 选择体现本意的名称能让人更容易理解和修改代码
   * 命名不清楚影响代码的模糊度
2. 最好的命名就是不需要注释
3. 避免误导
   * 不用与本意相悖的词，不使用其他平台或语言专有名词
4. 做有意义的区分
   * 如ProductInfo 与 ProductData 名称不一样，但意义却无区别
5. 使用读得出来的名称
   * 如ymdhms（年月日时分秒）不可取
6. 使用可搜索的名称
   * 变量或常量可能在代码中多处使用，则应当取一个便于搜索的名称。如：7，全局搜肯定不容易，写成Max_Price则易于查找
7. 类名
   * 类名和对象名称应该是名词或名词短语，不应该是动词
8. 方法名
   * 方法名应当时动词或动词短语
   * 属性访问器、需改气、断言，根据其值命名加上get、set、is，如：getName，setName，isClosed
9. 每个概念对应一个词
   * 给每个抽象概念选一个词，并且一以贯之；不同类中相同方法采用同种命名
10. 别用双关语
    * 避免将同一个单词用于不同目的
    * 一词一义：一个单词在全局中统一定为相同的意义
11. 使用解决方案领域名称
    * 只有程序员才会读你的代码，尽量用计算机科学术语。算法名、模式名、数学术语
    * 如果以上不好命名，则可以采用所涉问题领域来命名

---

#### 函数

> 涉及到单一职责原则[^SRP]、开放闭合原则[^OCP]

1. 短小——第一规则
2. 只做一件事

   * 判断是否不止做了一件事，就是看是否能拆出一个函数，该函数不仅只是单纯地重新诠释其实现
   * 只做一件事的函数无法被合理地分为多个区段
3. 每个函数一个抽象层级

   * 每个函数后面都跟着位于下一抽象层级的函数
   * 自顶向下读代码
4. 使用描述性的名称

   * 函数月短小，功能月集中，就越便于取个好名字
   * 长而具有描述性的名称，要比短而令人费解的名称好
   * 长而具有描述性的名称，要比描述性的长注释好
5. 函数参数
   * 最理想的参数个数是零，其次是一，再次是二，应该尽量避免三，有足够理由才能使用三个以上
   * 二元函数可以尽量转化为一元函数（作为另一个参数的方法来使用）
     * 如：writeField(outputStream, name) -> ouputStream.writeField(name)
   * 多参数应封装为类来使用
6. 输出函数
   * 避免使用输出函数，如果函数必须要修改某种状态，就修改所属对象的状态（修改属性替代return）
7. 错误处理就是一件事
   * 单独一个函数来处理错误，如：try/catch 在某个函数中存在，那么在其后不应该有其他内容
8. 消灭重复
   * 重复可能是软件中意见邪恶的根源
   * 面向方面的编程AOP，面向组价编程COP都是消除重复的策略

---

#### 注释

> 别给糟糕的代码加注释——重新写吧
>
> 注释的恰当用法是弥补我们在用代码表达意图时遭遇的失败

1. 注释不能美化糟糕的代码

   * 写注释的常见动机之一是糟糕代码的存在
2. 用代码来阐述

   * 创建一个描述与注释所言同一事物的函数即可，就不需要多余的注释
3. 好注释
   * 法律信息
   * 提供信息的注释
     * 解释某个抽象方法的返回值
   * 对意图的解释
     * 提供有关实现的有用信息，还提供了执行某个决定的意图
   * 阐释
     * 把某些晦涩难明的参数或返回值的意义翻译为某些可读形式；比如：某阅读某个标准库或不能修改的代码
     * 注意其阐释的正确性
   * 警示
     * 警示会出现某种结果的注释
   * TODO注释
     * 提醒要做的，或要求他人注意某个问题
4. 坏注释
  >如果只是因为你觉得应该或者因为过程需要就添加注释，那么就是无谓之举。
  >如果你决定写注释，就要花必要的时间确保写出最好的注释

   * 多余的注释
      * 不能比代码本身提供更多的信息
      * 没有给出代码的意图或逻辑
      * 读它并不比读代码容易
  * 误导性注释
  * 循环式注释
    * 不比每个方法、变量都要有注释
  * 日志式注释
    * 记录每次的修改或归属与署名，有了git之后，这些应该不存在
  * 废话注释
  * 括号后面的注释
    * 如果你发现自己想标记右括号，其实应该做的是缩短函数
  * 注释掉的代码
    * 同理，有了git，注释掉的代码直接删除即可

---

#### 格式

1. 垂直格式

   - 向报纸学习
     - 从上到下，从左到右，头条—大纲—概述—细节
   - 概念间垂直方向上区隔
     - 每行展现一个表达式或一个句子，每组代码行展示一条完整的思路，这些思路用空白行区隔开来
   - 垂直方向上的靠近
     - 空白行隔开概念，而紧密相关的代码应该互相靠近
   - 垂直距离
     - 变量声明：变量声明应尽可能靠近其使用位置
     - 实体变量：应该在类的顶部声明
     - 相关函数：调用者尽可能放到被调用者上面（自上至下的关联）
     - 概念相关：放在一起（虽然不一定相互依赖）

2. 横向格式

   * 水平方向上的区隔与靠近

     ```java
     //范例代码
     private void measureLine(String line) {
         lineCount++;
         int lineSize = line.length();
         totalChars += lineSize;
         lineWidthHistogram.addLine(lineSize, lineCount);
         recordWidestLine(lineSize);
     }
     
     a*b / c - d + e
     ```

     * 赋值操作符（=）周围加上空格字符
     * 函数名和左圆括号之间不加空格
     * 函数调用括号中的参数一一隔开，强调逗号
     * 乘法因子之间没加空格（因为它们具有较高的优先级），加减法运算符用空格隔开

   * 缩进

     * 类中的方法相对该类缩进一个层级
     * 方法的实现相对方法声明缩进一个层级
     * 代码块的实现相对于其容器代码块缩进一个层级
     * 不要违反缩进原则，如 if 语句中只有一行的情况（依旧需要缩进）
     * while、for 语句体为空，分号放在下一行，较醒目

---

#### 对象和数据结构

   1. 数据抽象

      > 类并不简单地用取值器或赋值器将其变量推向外间，而是暴露抽象接口，以便用户无需了解数据的实现就能操作数据本体

   2. 得墨忒耳律

      > 模块不应了解它的操作对象的内部情形（隐藏数据，暴露操作）

   3. 对象暴露行为，隐藏数据（面向对象）
      * 优：便于添加新对象类型而无需修改既有行为
      * 缺：难以在既有对象中添加新行为

   4. 数据结构暴露数据，没有明显的行为（面向过程）
        * 优：便于向既有数据结构添加新行为
        * 缺：难以向既有函数添加新数据结构

---

#### 错误处理

   **打包第三方API作用**：

   当你打包一个第三方API，你就降低了对它的依赖；未来你可以不太痛苦地改用其他库。

   在你测试自己代码时，打包也有助于模拟第三方调用。

1. try-catch-findly
2. 依调用者定义异常类
3. 定义常规流程
   * 隔离业务逻辑与错误处理
4. 别返回null值
5. 别传递null值

---

#### 边界

> 不要在生产代码中试验新东西，而是编写测试代码来遍览和理解第三方代码——学习性测试

---

#### 单元测试

1. TDD三定律
   * 在编写不能通过的单元测试 前，不可编写生产代码
   * 只可编写刚好无法通过的单元测试，不能编译也不算通过
   * 只可编写刚好足以通过当前失败测试的生产代码
2. 保持测试整洁
   * 测试代码和生产代码一样重要
3. F.I.R.S.T
   * 快速（Fast）
   * 独立（Independent）
   * 可重复（Repeatable）
   * 自足验证（Self-Validating）
   * 及时（Timely）

---

#### 类

*  类应该短小，和函数一样

* 单一职责原则[^SRP]

  * 类或模块应有且只有一条加以修改的理由
  * 系统应该由许多短小的类而不是少量巨大的类组成，每个小类封装一个权责，只有一个修改的原因，并与少数其他类一起协调达成期望的系统行为

* 内聚

  * 类应该只有少量实体变量
  * 如果一个类中的每个变量都被每个方法所使用，则该类具有最大的内聚性
  * 内聚性高，意味着类中的方法和变量互相依赖，互相结合成一个逻辑整体

* 为了修改而组织

  > 开放闭合原则[^OCP]认为类应当对拓展开放，对修改封闭
  >
  > 依赖倒置原则[^DIP]认为类应当依赖于抽象而不是具体细节

  * 理想系统中，我们通过拓展系统而非修改现有代码来添加新特性

**总之：类的修改可能导致全面测试，所以应当是拓展功能而不是修改功能。**

---

#### 迭进

##### 简单设计四条规则

* 运行所有测试
* 不可重复
* 表达力
* 尽可能少的类和方法（优先级最低）

##### 重构作用

* 提升内聚性，降低耦合度，切分关注面，模块化系统关注面，缩小函数和类的尺寸，选用更好的名称

##### 表达力

> 代码应当清晰地表达作者的意图

* 通过选用好的名称来表达
* 通过保持函数和类尺寸短小来表达
* 采用标准命名法来表达
* 编写良好的单元测试

---

#### 死锁

##### 死锁发生的四个条件

* 互斥

  > 同一资源无法在同一时间为多个线程使用；数量上有限制

* 上锁及等待

  > 获取资源未结束之前，不会释放

* 无抢先机制

  > 线程无法从其他线程处夺取资源，只有等待

* 循环等待

  > “死命拥抱”

##### 死锁的处理

* 不互斥
* 不上锁及等待
* 满足抢先机制
* 不做循环等待（最常用）

---



[^SRP]: 单一职责原则（SRP：Single responsibility principle）又称单一功能原则，面向对象五个基本原则（SOLID）之一。它规定一个类应该只有一个发生变化的原因。
[^OCP]: 开放封闭原则（OCP，Open Closed Principle）是所有面向对象原则的核心。软件设计本身所追求的目标就是封装变化、降低耦合，而开放封闭原则正是对这一目标的最直接体现。

[^DIP]: 依赖倒置原则（DIP，Dependence Inversion Principle）是程序要依赖于抽象接口，不要依赖于具体实现。简单的说就是要求对抽象进行编程，不要对实现进行编程，这样就降低了客户与实现模块间的耦合。