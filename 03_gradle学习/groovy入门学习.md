## 变量
Groovy中用def关键字来定义变量，可以不指定变量的类型，默认访问修饰符是public。  
```groovy
def a = 1;
def int b = 1;
def c = "hello world";
```

## 方法
方法使用***返回类型或def关键字定义***，方法可以接收任意数量的参数，这些参数可以不申明类型，如果不提供可见性修饰符，则该方法为public。
用def关键字定义方法。
```groovy
task method {
    add (1,2)
    minus 1,2 //1
}
def add(int a,int b) { 
 println a+b //3
}  
def minus(a,b) {//2 
 println a-b
}   
```
如果不使用return ，方法的返回值为最后一行代码的执行结果。

## 类
```groovy
task method {
    def p = new Person()
    p.increaseAge 5
    println p.age
}
class Person {                       
    String name                      
    Integer age =10
    def increaseAge(Integer years) { 
        this.age += years
    }
}
```

Groovy类与Java类有以下的区别：

1. 默认类的修饰符为public。

2. 没有可见性修饰符的字段会自动生成对应的setter和getter方法。

3. 类不需要与它的源文件有相同的名称，但还是建议采用相同的名称。

## 语句
和java类似

## 字符串
Groovy中的基本数据类型和Java大同小异，这里主要介绍下字符串类型。在Groovy种有两种字符串类型，普通字符串String（java.lang.String）和插值字符串GString（groovy.lang.GString）。

单引号字符串
在Groovy中单引号字符串和双引号字符串都可以定义一个字符串常量，只不过单引号字符串不支持插值。

双引号字符串
要想插值可以使用双引号字符串，插值指的是替换字符串中的占位符，占位符表达式为${}或者以$为前缀。

```groovy
def name = 'Android进阶之光'
println "hello ${name}"
println "hello $name"
```
三引号字符串
三引号字符串可以保留文本的换行和缩进格式，不支持插值。
```groovy
task method {
def name = '''Android进阶之光
       Android进阶解密
Android进阶？'''
println name 
}
```

## 闭包
Groovy中的闭包是一个开放的、匿名的、可以接受参数和返回值的代码块。
```groovy
{ it -> println it }   //it是一个显示参数 
{ String a, String b ->                                
    println "${a} is a ${b}"
}  
```
-> 左边是:参数列表部分  右边是:语句部分

## I/O 操作
### 文件读取
```groovy
def filePath = "D:/Android/name.txt"
def file = new File(filePath) ;
file.eachLine {
    println it
}
```
### 文件写入
```groovy
def filePath = "D:/Android/name.txt"
def file = new File(filePath);

file.withPrintWriter {
    it.println("三井寿")
    it.println("仙道彰")
}
```

## 其他
### asType
asType可以用于数据类型转换：
```groovy
String a = '23'
int b = a as int
def c = a.asType(Integer)
assert c instanceof java.lang.Integer
```

### 判断是否为真
```java
if (name != null && name.length > 0) {}
```
可以替换为
```groovy
if (name) {}
```

### 安全取值
在Java中，要安全获取某个对象的值可能需要大量的if语句来判空：
```java
if (school != null) {
    if (school.getStudent() != null) {
        if (school.getStudent().getName() != null) {
            System.out.println(school.getStudent().getName());
        }
    }
}
```
Groovy中可以使用？.来安全的取值：
```groovy
println school?.student?.name
```

### with操作符
对同一个对象的属性进行赋值时，可以这么做：
```groovy
task method {
Person p = new Person()
p.name = "杨影枫"
p.age = 19
p.sex = "男"
println p.name
}
class Person {                       
    String name                      
    Integer age
    String sex
}
```
使用with来进行简化：
```groovy
Person p = new Person()
p.with {
   name = "杨影枫"
   age= 19
   sex= "男"
 }   
println p.name
```