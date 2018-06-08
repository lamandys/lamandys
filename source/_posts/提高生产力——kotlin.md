---
title: 提高生产力——kotlin
date: 2018-05-09 20:40:17
tags: Android
---
随着今年Google把kotlin语言作为Android开发的主要语言后，各种kotlin的教程遍地开花，Android studio也支持自动引入kotlin的支持，再也不用像以前那样使用那么麻烦了。

<!--more-->

之前的一篇博客中有提及到kotlin语言开发的入门级教程，现在回头看，感觉很糟糕，不知道具体说的是什么。今次就把Kotlin相对于Java的优势汇总，具体的用法下次再开篇。

###  更简洁的字符串
Kotlin中的字符串基本Java中的类似，有一点区别是加入了三个引号"""来方便长篇字符的编写。 而在Java中，这些都需要转义，先看看java中的式例：
```
public void testString1() {
        String str1 = "line1\n" +"line2\n" +"line3";
        String js = "function myFunction()\n" +
                "{\n" +
                "    document.getElementById(\"demo\").innerHTML=\"test text\";\n" +
                "}";
        System.out.println(str1);
        System.out.println(js);
    }
```
kotlin除了有单个双引号的字符串，还对字符串的加强，引入了三个引号，"""中可以包含换行、反斜杠等等特殊字符：
```
fun testString() {
    val str1 = """line1\n
        line2
        line3
        """
    val js = """
        function myFunction()
        {
            document.getElementById("demo").innerHTML="test text";
        }
        """.trimIndent()
    println(str1)
    println(js)
}
```
同时，Kotlin中引入了字符串模版，方便字符串的拼接，可以用$符号拼接变量和表达式
```
fun testString2() {
    val strings = arrayListOf("abc", "efd", "gfg")
    println("First content is $strings")
    println("First content is ${strings[0]}")
    println("First content is ${if (strings.size > 0) strings[0] else "null"}")
}
```
值得注意的是，在Kotlin中，美元符号$是特殊字符，在字符串中不能直接显示，必须经过转义，方法1是用反斜杠，方法二是${'$'}
```
fun testString3() {
    println("First content is \$strings")
    println("First content is ${'$'}strings")
}
```
### 表达式
#### java中的三元运算符：
```
public int max2(int a, int b) {
    return a > b ? a : b;
}
```
kotlin中没有这个表达式，可用if-else代替：
```
fun int max2(a: Int,b: Int) = if (a > b) a else b
```
#### 用when来取代Java中的switch或者if-else
java中的switch：
```
public String getPoint(char grade) {
        switch (grade) {
            case 'A':
                return "GOOD";
            case 'B':
            case 'C':
                return "OK";
            case 'D':
                return "BAD";
            default:
                return "UN_KNOW";
        }
    }
```
用Kotlin去简化后的操作：
```
fun getPoint(grade: Char) = when (grade) {
    'A' -> "GOOD"
    'B', 'C' -> {
        "这一行你写什么都不返回的，最后一行才真正是返回的内容"
        "OK"
    }
    'D' -> "BAD"
    else -> "UN_KNOW" // 相当于default
}
```
上面的b和c之间有一行文字，它是不影响整体的逻辑结构，when的每个分支的最后一行才是当前分支的值。
另外，最后的else相当于Java中的default字段。

同样的，when语句还可以取代java中的if/else if，其是表达式有值，并且更佳简洁，先看看java中的if-else：
```
public String getPoint2(Integer point) {
        if (point > 100) {
            return "GOOD";
        } else if (point > 60) {
            return "OK";
        } else if (point.hashCode() == 0x100) {
            //...
            return "STH";
        } else {
            return "UN_KNOW";
        }
    }
```
再看看kotlin的版本，使用不带参数的when:
```
fun getPoint2(grade: Int) = when {
    grade > 90 -> "GOOD"
    grade > 60 -> "OK"
    grade.hashCode() == 0x100 -> "STH"
    else -> "UN_KNOW"
}
```

### 更好调用的函数：显式参数名/默认参数值
Kotlin的函数更加好调用，主要是表现在两个方面：
1. 显式的标示参数名，可以方便代码阅读；
2. 函数可以有默认参数值，可以大大减少Java中的函数重载

例如一个请求接口的方法，在java中的实现是这样的：
```
public void requestData(final Context context, String deviceSn, String userId) {
        String url = "https://www.baidu.com";
        GetRequest<String> getRequest = Http.get(url);
        getRequest.execute(new StringCallback() {
            @Override
            public void onSuccess(Response<String> response) {
                if (response.code() == 200) {
                  // do somthing
                }
            }

            @Override
            public void onError(Response<String> response) {
                super.onError(response);
                // do somthing
            }
        });
    }
```

有时候我们默认的参数中不想传递，用一个默认值，或者说是我需要一个只有一个参数的方法，其他的两个参数都拿默认值，在java中就要去重新写两个不同参数的重载方法，而在kotlin中就很友好的一个方法可以搞定了：
```
fun requestData(context: Context, deviceSn: String = "123456", userId: String = "5678"){
    val url = "https://www.baidu.com"
    ...
}
```
类似这样的操作中，deviceSn和userId都有一个默认值，我们在调用该方法的时候可以这样做：
```
requestData(context)
requestData(context,"123456")
requestData(context,"123456","5678")
requestData(context,deviceSn = "123456",userId = "5678")
```
可以看到最后的一个调用，在参数中可以写明是什么参数，这样有助于阅读方便。另外这样有了默认参数后，在使用函数时，如果不传入该参数，默认会使用默认的值，这样可以避免Java中大量的函数重载。

### 扩展函数和属性
#### 扩展函数
扩展函数究竟是什么，当初怎么看都不懂。扩展函数和扩展属性是Kotlin非常方便实用的一个功能，它可以让我们随意的扩展第三方的库，你如果觉得别人给的SDK的Api不好用，或者不能满足你的需求，这时候你可以用扩展函数完全去自定义。下面举个例子说明：

比如String类中，我们想获取最后一个字符，可是没有这个方法，怎么办？Java中一般就是写个utils去获取，这里用kotlin来解释扩展函数究竟是什么：
```
fun String.lastChar(): Char = this.get(this.length - 1)
```
这样定义好lastChar()函数后，之后只需要import进来后，就可以用String类直接调用该函数了，跟调用它自己的方法没有区别。这样可以避免重复代码和一些静态工具类，而且代码更加简洁明了。还有一个很常用的扩展函数：
```
fun Context.toast(msg: String,duration: Int = Toast.LENGTH_SHORT) {
    Toast.makeText(this,msg,duration).show();
}
```
这样定义好后，就可以直接调用toast(“测试消息”)来进行提示了，方便得很。

#### 扩展属性
扩展函数知道了，那么扩展属性是什么？其实道理都一样的，函数是函数调用，属性就是属性获取，好吧，看例子：

假如我想实现String和StringBuilder通过属性去直接获得最后字符，怎么实现？
```
val String.lastChar: Char
    get() = get(length - 1)

var StringBuilder.lastChar: Char
    get() = get(length - 1)C
    set(value: Char) {
        setCharAt(length - 1, value)
    }
```
定义好扩展属性后，之后只需import完了就跟使用自己的属性一样方便了。

#### Why？Kotlin为什么能实现扩展函数和属性这样的特性？
在Kotlin中要理解一些语法，只要认识到Kotlin语言最后需要编译为class字节码，Java也是编译为class执行，也就是可以大致理解为Kotlin需要转成Java一样的语法结构， Kotlin就是一种强大的语法糖而已，Java不具备的功能Kotlin也不能越界的。

我们看看将上面的扩展函数转成Java后的代码：
```
/*
* 扩展函数会转化为一个静态的函数，同时这个静态函数的第一个参数就是该类的实例对象
* */
public static final char lastChar(@NotNull String $receiver) {
    Intrinsics.checkParameterIsNotNull($receiver, "$receiver");
    return $receiver.charAt($receiver.length() - 1);
}
/*
* 获取的扩展属性会转化为一个静态的get函数，同时这个静态函数的第一个参数就是该类的实例对象
* */
public static final char getLastChar(@NotNull StringBuilder $receiver) {
    Intrinsics.checkParameterIsNotNull($receiver, "$receiver");
    return $receiver.charAt($receiver.length() - 1);
}
/*
* 设置的扩展属性会转化为一个静态的set函数，同时这个静态函数的第一个参数就是该类的实例对象
* */
public static final void setLastChar(@NotNull StringBuilder $receiver, char value) {
    Intrinsics.checkParameterIsNotNull($receiver, "$receiver");
    $receiver.setCharAt($receiver.length() - 1, value);
}
```
由上面的代码可以看出，扩展函数实际上最终还是转换为java中的静态方法，并且是final的，不能被override，扩展属性同理。

### 利用局部函数抽取重复代码
Kotlin中提供了函数的嵌套，在函数内部还可以定义新的函数。这样我们可以在函数中嵌套这些提前的函数，来抽取重复代码。例如下面的功能：
```
class User(val id: Int, val name: String, val address: String, val email: String)

fun saveUser(user: User) {
    if (user.name.isEmpty()) {
        throw IllegalArgumentException("Can't save user ${user.id}: empty Name")
    }
    if (user.address.isEmpty()) {
        throw IllegalArgumentException("Can't save user ${user.id}: empty Address")
    }
    if (user.email.isEmpty()) {
        throw IllegalArgumentException("Can't save user ${user.id}: empty Email")
    }
}
```
上面的代码在判断name、address等是否为空的处理其实很类似。这时候，我们可以利用在函数内部嵌套的声明一个通用的判空函数将相同的代码抽取到一起：
```
fun saveUser2(user: User) {
    fun validate(value: String, fildName: String) {
        if (value.isEmpty()) {
            throw IllegalArgumentException("Can't save user ${user.id}: empty $fildName")
        }
    }

    validate(user.name, "Name")
    validate(user.address, "Address")
    validate(user.email, "Email")
}
```
除了利用嵌套函数去抽取，此时，其实也可以用扩展函数来抽取，如下所示：
```
fun User.validateAll() {
    fun validate(value: String, fildName: String) {
        if (value.isEmpty()) {
            throw IllegalArgumentException("Can't save user $id: empty $fildName")
        }
    }

    validate(name, "Name")
    validate(address, "Address")
    validate(email, "Email")
}

fun saveUser3(user: User) {
    user.validateAll()
}
```

### 使用数据类来快速实现model类
在java中要声明一个model类需要实现很多的代码，首先需要将变量声明为private，然后需要实现get和set方法，还要实现对应的hashcode equals toString方法等，例如：
```
public static class User {

        private String name;
        private int age;
        private int gender;
        private String address;
        
        public String getName() {
            return name;
        }

        public void setName(String name) {
            this.name = name;
        }

        public int getAge() {
            return age;
        }

        public void setAge(int age) {
            this.age = age;
        }

        public int getGender() {
            return gender;
        }

        public void setGender(int gender) {
            this.gender = gender;
        }

        public String getAddress() {
            return address;
        }

        public void setAddress(String address) {
            this.address = address;
        }

        @Override
        public String toString() {
            return "User{" +
                    "name='" + name + '\'' +
                    ", age=" + age +
                    ", gender=" + gender +
                    ", address='" + address + '\'' +
                    '}';
        }

        @Override
        public boolean equals(Object o) {
            if (this == o) return true;
            if (o == null || getClass() != o.getClass()) return false;

            User user = (User) o;

            if (age != user.age) return false;
            if (gender != user.gender) return false;
            if (name != null ? !name.equals(user.name) : user.name != null) return false;
            return address != null ? address.equals(user.address) : user.address == null;
        }

        @Override
        public int hashCode() {
            int result = name != null ? name.hashCode() : 0;
            result = 31 * result + age;
            result = 31 * result + gender;
            result = 31 * result + (address != null ? address.hashCode() : 0);
            return result;
        }
    }
```
而如果用kotlin，只需要一行代码就可以做到。
```
/*
* Kotlin会为类的参数自动实现get set方法
* */
class User(val name: String, val age: Int, val gender: Int, var address: String)

/*
* 用data关键词来声明一个数据类，除了会自动实现get set，还会自动生成equals hashcode toString
* */
data class User2(val name: String, val age: Int, val gender: Int, var address: String)
```
是不是很方便呢。

<!-- https://github.com/heimashi/kotlin_tips -->

先写到这里吧，剩下的有时间再补充。