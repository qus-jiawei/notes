# scla for Impatient #

## 目录 ##

[1.基础](#1)

[2.控制结构和函数](#2)

[3.数组相关操作](#3)

[4.映射和元组 （Map, tuple）](#4)

[5. 类](#5)

[6. 对象](#6)

[7. 包和引入](#7)

[8. 继承](#8)


----------

### <a name="1">1.基础</a> ###

scala的REPL已是一个独立项目，可以拿来集成到项目中，提供交互式命令接口功能。

scala对于基本数据类型通过隐式转换提供了相应的操作类提供灵活的操作。如`Int`对应提供了`RichInt`，所以可以有这样的操作`1 to 9`。

scala对于所有操作符都是方法调用，对于方法可以任何合法的字符。

scala中除了可以定义方法还可以定义函数。

scala提供`apply`方法对于`()`操作符的重载，借助于伴生对象的`apply`方法构建对象是构建对象的常用手法，减少了`new`关键字在代码中出现的机会。如 `BigInt("123244545")`

### <a name="2">2.控制结构和函数</a> ###

scala中表达式都有值，语句块也有值，语句块的值就是最后一个表达式的值。
> 赋值语句的值为`Unit`，所以`a=b=5`这样的语句不应该出现，a的值会为`Unit`而不是你所想的5.

- 条件表达式
    
        val s = if (x > 0) 1 else -1
 
- 块表达式和赋值

        val distance = {val dx = x -x0; val dy = y - y0; sqrt(dx * dx + dy * dy)}

- 输入和输出

        print("Answer: ")
        println(42)
        printf("Hello, %s! You are %d years old.\n", "Fred", 42)
        
        val name = readLine("Your name: ")
还要相应的`readInt`、`readDouble`、`readByte`、`readShort`、`readLong`、`readFloat`、`readBoolean`、`readChar`.这个方法会返回对应的数据类型。

- 循环

        while (n > 0) {
            r = r * n
            n -= 1
        }

        for (i <- 1 to n)
            r = r * i


    > Scala并没有提供break或continue语句来退出循环。可以使用： 
    > 
        1. 使用Boolean型的控制变量。
        2. 使用嵌套函数-----可以从函数当中return。
        3. 使用Breaks对象中的break方法：但这是通过抛出和捕获异常完成的，对性能一定的影响
    >       
        import scala.util.control.Breaks._
        breakable {
            for (...) {
                if (...) break; //退出breakable块
                ...
            }
        }

- 高级for循环和for推导式 
        
        for(i < 1 to 3; j <- 1 to 3 if i != j) print((10 * i + j) + " ") 
        
        for(i < 1 to 10) yield i % 3

        for {
            i < 1 to 3
            from = 4 - i
            j <- from to 3
        }     

- 默认参数和带名参数
- 变长参数

        def sum(args: Int*)
        val s = sum(1 to 6: _*)

- 懒值
    > val在定义时即被取值。
    > lazy val在首次被用到时取值（var不能用在lazy中）
    > def在每次使用时取值

- 异常
   
    throw表达式有特殊的类型Nothing


### <a name="3">3.数组相关操作</a> ###

- 定长数组：`Array`
- 变长数组：数组缓冲 `ArrayBuffer`
    > 它们可以互转： `toBuffer` 与 `toArray` 

     遍历数组和数组缓冲： 使用for
     数组转换： 使用for推导式； map

- 数组常用算法
         
        sum, max, min, sorted(_ < _), scala.util.Sorting.quickSort(a),
        mkString(" and "), mkString("<", ",", ">")
    > Array类的操作在ArrayOps下，数组会隐式转换成ArrayOps对象。

- 多维数组： 通过数组的数组来实现
- 与Java的互操作： 可以通过引入`scala.collection.JavaConversions`里的隐式转换方法

### <a name="4">4.映射和元组 （Map, tuple）</a>###

- 构造映射
      
    scala的集合都分为可变集合与不可变集合。一般默认为不可变集合，创建时都可使用初始值构建或从一个空的映射开始，从一个空的映射开始时需要指定类型。

- 获取映射中的值

    > 可以直接使用`（键）`的方式获取，但如果请求中不包含使用的键，则会抛出异常。也可以使用`getOrElse(键， 默认值)`的方式。或使用get(键)返回`Option`对象的方式。

- 更新映射中的值

    > 对于可变映射可以使用`scores("Bob") = 99`的方式。 下面是对于可变，不可变都可以方式
    >
        scores += ("Bob" -> 10, "Fred" -> 7) //更新或添加多个关系
        scores -= "Fred" //移除某个键对应的值

- 迭代映射

        for ((k, v) -> 映射) 处理k和v
    也可以通过映射的`keySet` 与 `values`方法，再对返回`Set` 或 `Iterable`进行迭代处理。

- 已排序映射

    一般操作映射需要选择一个实现：哈希表或平衡树。scala默认是使用哈希映射。如果想要一个树映射可以使用`SortedMap`, `TreeMap`。 如果要按插入顺序访问所有键，使用`LinkedHashMap`。

- 与Java的互操作

    集合类都是使用`scala.collection.JavaConversions`下面的相应隐式转换进行。对于映射有：`mapAsScalaMap`, `propertiesAsScalaMap`, `mapAsJavaMap`。

- 元组

    元组是不同类型值的集合， 元组可以使用`_1, _2, _3`的方式访问其组元。但一般使用模式匹配的方式对进行访问。`（first, _, third） = t`。元主要用函数返回不止一个值的情况。

- 拉链操作

    使用元组的原因之一是把多个值顷绑定在一起，以便它们能够被一起处理，这通常可以用zip方法来完成。

        val symbols = Array("<", "-", ">")
        val counts = Array(2, 10, 2)
        val pairs = symbols.zip(counts) // Array(("<", 2), ("-", 10), (">", 2))
        for ((s, n) <- pairs) print(s * n) // 会打印 <<---------->>
        
    用toMap方法可以将对偶的集合转换成映射 `keys.zip(values).toMap`

### <a name="5">5. 类</a> ###

- 简单类与无参方法

    scala中访问权限默认是`public`的。scala中源代码可以包含多个类且都是`public`，不用有一个类与源代码名字相同。

- 带getter和setter的属性

    在scala中生成类向于JVM的类时字段都是`private`的且会生成getter， setter方法。
    
    在scala中， getter和setter分别叫做`age`和`age_=`的形式。这样任何时候都可以自己重新定义getter和setter方法如：
        
        class Person {
            private var privateAge = 0 //变成私有并改名
            
            def age = privateAge
            def age_(newValue: Int) {
                if (newValue > privateAge) privateAge = newValue
            }
        }

    > 对于调用scala的setter方法使用的方式不用带下划线, 如: `person.age = 78`, 这样可以使用*统一访问原则*的方式来访问。 
    
    > 在scala中可以控制对于getter和setter的产生。
     
    >  - 如果字段是私有的，则getter和setter方法也是私有的。
    >  - 如果字段是val，则只有getter方法被生成。
    >  - 如果你不需要任何getter或setter，可以将字段声明为private[this]

- 只带getter的属性

    使用val

- 对象私有字段

    使用`private[this]`

- Bean属性： 为了与java兼容，生成java版的getter和setter.

    使用`scala.reflect.BeanProperty`。 将scala字段标注为`@BeanProperty`，将生成scala版的getter和setter 与 java版的getter和setter。

- 辅助构造器

    辅助构造器的名称为this, 且必须以调用其它辅助构造器或主构造器开始。

- 主构造器

    主构造器的参数被编译成字段。主构造器会执行类定义中的所有语句。

    **针对主构造器参数生成的字段与方法**
        
        主构造器参数                              造成的字段/方法
        name: String                            对象私有字段。如果没有方法使用name, 则没有该字段
        private val/var name: String            私有字段，私有的getter/setter方法
        val/var name: String                    私有字段，公有的getter/setter方法
        @BeanProperty val/var name: String      私有字段，公有的Scala版和JavaBeans版的getter/setter方法

> 如果想让主构造器变成私有的，可以像这样放置private关键字： `class Person private(val id: Int) { ... }`。 这样一来类用户就必须通过辅助构造器来构造Person对象了。

- 嵌套类

    在Scala中，几乎可在任何语法结构中内嵌入任何语法结构。

    Scala与Java不同，在Java中内部类从属于内部类，而Scala中每个对象都有自己的内部类。对于内部类的构造也不同，Java:  `chatter.new Member()`。  Scala：　`new Chatter.Member`。

### <a name="6">6. 对象</a> ###

- 单例对象
    
    对象的构建方法在对象第一次被使用时被调用。

- 伴生对象

    伴生类与伴生对象可以互相访问私有特性，它们必须存在于同一个源文件中，但并不在相同的作用域中。

- 扩展类或特质的对象

    对象可以扩展类以及一个或多个特质，基结果是一个扩展了指定类以及特质的类的对象(单例对象是扩展类或特质的实例)，同时拥有在对象定义中给出的所有性。

- 对象的apply方法

- 应用程序对象App

    App特质扩展自另一个特质DelayedInit，编译器对该特质有特殊的处理。所有带有该特质的类，其初始化方法都会被挪到delayedInit方法中。App特质的main方法捕获命令行参数，调用delayedInit方法，并且还可以根据要求打印出逝去的时间。
        
- 枚举
    
    定义一个扩展Enumeration类的对象并以Value方法调用初始化枚举中的所有可选值。

        Object TrafficLightColor extends Enumeration {
            type TrafficLightColor = Value
            val Red, Yellow, Creen = Value
        }

        #枚举值的ID可通过id方法返回，名称通过toString方法返回。
        for (c <- TrafficLightColor.values) println(c.id + ": " + c)

        #可以通过枚举的ID或名称来进行查找定位，以下两段代码都输出TrafficLightColor.Red对象
        TrafficLightColor(0) //调用Enumeration.apply方法
        TrafficLightColor.withName("Red")

### <a name="7">7. 包和引入</a> ###

- 包
    
    包与源文件没有强制的关联关系。包可以任何位置引入。

- 作用域规则

    包可以嵌套所有父包的内容都在作用域内。可以使用相对路径引入包；绝对包名，以_root_开始。如：`new _root_.scala.collection.mutable.ArrayBuffer[Int]`

- 串联式包语句

    可用于限定可见成员

- 文件顶部标记法

- 包对象
    
    包可以包含类、对象和特质，但不能包含函数或变量的定义。所以引入了包对象。

        package object People {
            val defaultName = "test"
        }

- 包可见性

- 引入

- 任何地方都可以声明引入

- 重命名和隐藏方法

        import java.util.{HashMap => JavaHashMap}
        import java.util.{HashMap => _, _}

- 隐式引入
    
        # 隐式引入的代码
        import java.lang._
        import scala._
        import Predef._

### <a name="8">8. 继承</a> ###

- 扩展类

    scala中final用于修改类，则类不能被继承，用于修改方法或字段则它们不能被重写。重写非抽像方法或字段时必须使用override修饰符。

- 重写方法

    使用override修饰更有利于解决`易违约基类问题`。

- 类型检查和转换

            Scala                           Java
            obj.isInstanceOf[C1]            obj instanceof C1
            obj.asInstanceOf[C1]            (C1)obj
            classOf[C1]                     C1.class

- 受保护字段和方法

- 超类的构造

    只有主构造器可以调用超类的构造器。

- 重写字段

        - def只能重写另一个def
        - val只能重写另一个val或不带参数的def
        - var只能重写另一个抽象的var

- 匿名子类

        #如：
        val alien = new Person("Fred") {
            def greeting = "Greetings, Earthing! My name is Fred."
        }

- 抽象类

- 抽象字段

    抽象字段就是一个没有初始值的字段。

- 构造顺序和提前定义

        class Ant extends {
            override val range = 2
        } with Creature

- Scala继承层级

- 对象相等性