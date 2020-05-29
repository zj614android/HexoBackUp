---
title: kotlin学习笔记1:kotlin基础语法，与java交互，以及新手可能遇见的问题
date: 2046年11月18日15:49:36
tags: kotlin
---

## 环境
android studio
创建项目时需要点击：include kotlin
创建kotlin文件：new Kotlin File(位置在new java class下面一格)

## 基础语法
var age :int = 18//声明一个变量
val name : String = "zhang san"//声明不可变变量，非常量
val name2 : String = null//编译器会报错不能为空
fun 关键字来定义函数
$符调用参数

```
package kt.zj.com.ktlearn

import android.support.v7.app.AppCompatActivity
import android.os.Bundle

class MainActivity : AppCompatActivity() {

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

        var age: Int = 18;
        val name: String = "lucy";

        who(name,age);

    }

    fun who(name :String,age :Int){
//      println(name + "_________" + age);
        println("$name" + "_________" + "$age" );
    }
}
```

## 与java互调用
### .1.举例1:静态互调
ktolin函数能直接写在文件里面，而不用写在类里面的
kotlin文件在编译之后依旧是jvm平台的一个class
一个用java调用kotlin函数的例子：
> 注意：kotlin文件名带kt后缀，且java调用者函数是静态。

JavaUseKotlin.java
```
package kt.zj.com.ktlearn;

public class JavaUseKotlin {

    public static void main(String[] args) {
        UtilsKt.sayHello();;
    }
}

```

Utils.kt
```
package kt.zj.com.ktlearn

fun sayHello(){
    println("hello");
}
```

### .2.举例2:将类作为参数传递
1.kotlin文件被编译成的并不是class字节码，而是叫做Kclass的字节码文件
2.kotlin文件必须声明类名，否则不识别
```class KotlinMain```
3.实验场景：
- 创建一个javaMain.class
- 创建一个kotlinMain.kt(并声明类名)
- 在kotlin测试类里定义2个函数testClass,并实验测试函数
```
//测试函数
fun main(args : Array<String>){
    testClass(javaMain::class.java)//入参为javaClass时，记得添加后缀.java
    testClass(KotlinMain::class)//入参为KClass时，直接调
}
//ps:在实际过程中,.java后缀由ide自己给我们补全了。

//入参为javaClass时，记得添加后缀.java
fun testClass(clazz :Class<javaMain>){
    println(clazz.simpleName);
}

//入参为kClass
fun testClass(clazz :KClass<KotlinMain>){
    println(clazz.simpleName);
}
```


### .3.举例3:kotlin与java在关键字上的冲突
问题条件1:
在java中又一个变量，变量名叫in;
```
public class javaMain {
    public static String in = "in";
}
```
在kotlin中，in是一个关键字，所以kotlin在调这个变量时，不能直接调用，需要用2个反引号包裹，表示去解决这个冲突；
```
println(javaMain.`in`);
```
ps:在实际过程中,反引号包裹由ide自己给我们补全了。

## 排错
```
 Kotlin reflection implementation is not found at runtime. Make sure you have kotlin-reflect.jar in the classpath
	at ...
```
解决：
https://majing.io/posts/10000007151189