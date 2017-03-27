---
title: jar包及mainfest文件说明
date: 2011-03-27 13:05:57
categories:
 - java
tags:
 - jar
 - mainfest
---

### 关键字: java jar 
Java的一种文档格式，JAR文件非常类似ZIP文件，也是一个压缩文件。JAR文件与ZIP文件惟一的区别就是在JAR文件的内容中，它包含了一个meta-inf/manifest.mf文件，这个文件是在生成JAR文件的时候自动创建的。需要注意的是，JAR文件不需要进行解压缩，如果把文件解开反而会造成错误。
发布Java应用程序时你会感到困难？好在Java提供了一系列打包和发布工具，可以显著的简化发布过程该文章提供了打包Java code的几种方法，我们将会探讨Java manifest 文件，给出用于管理JAR文件所依赖文件、估计跨平台发布所需的CLasspath的合适方法.我也会解释如何使用manifest包版本特性来确认包的兼容性... 

<!-- more -->

### 什么是JAR文件？
在开发过程中，我们可以直接使用Java class文件来运行程序，但这并不是一个好方式，好在Java 提供了 JAR(Java Archive)文件来提供发布和运行。 
jar 文件实际上是class 文件的ZIP压缩存档，这种格式被广泛使用，因此易与使用，有很多中工具可以操作这种格式的文件。也正是因为这个原因，jar文件本身并不能表达所包含应用程序的标签信息。 
Manifest 因此得以出现 
为了要提供存档的标签信息，jar 文件指定了一个特定目录来存放标签信息：META-INF 目录，其中我们来关注该目录中的MANIFEST.MF文件，他就是JAR的manifest文件，他包含了JAR文件的内容描述，并在运行时向JVM提供应用程序的信息，大多数JAR文件含有一个默认生成的manifest 文件,执行JAR命令或使用zip工具，都可以产生它 
如果是由jar命令产生的 manifest 文件，形如: 

``` 
Manifest-Version: 1.0 
Created-By:1.4.0-beta 
(Sun Microsystems Inc.) 
```
这些信息没甚么用,仅仅告诉我们使用的是1.0的manifest文件,第一行定义manifest的格式，第二行说明使用 SUN 的JDK1.4的jar工具生成该文件，如果manifest文件是由其他 （如ant） 创建的，那将会出现 “Created-By: Ant 1.2” 之类的内容，如果你是自己创建manifest文件，你可以加入自己的一些相关信息. 

### 基础格式 
manifest 文件的格式 是很简单的，每一行都是 名－值 对应的:属性名开头，接着是 ":" ，然后是属性值，每行最多72个字符，如果需要增加，你可以在下一行续行，续行以空格开头，以空格开头的行都会被视为前一行的续行。 
所有在开头的属性都是全局的，你也可以定义特定class 或package的属性，稍后将介绍这种 

### 把manifest文件插入JAR文件 
使用 m 选项，把指定文件名的manifest文件 传入，例如
``` 
jar cvfm myapplication.jar myapplication.mf [-C]classdir 
```
如果你使用ant来创建时，在ant 的build.xml 加入如下条目 
``` xml
<target name="jar"> 
<jar jarfile ="myapplication.jar" 
manifest="myapplication.mf"> 
<fileset dir="classdir" 
includes="**/*.class"/> 
</jar> 
</target> 
```
### 运行Java程序 
现在我们来体验一下manifest文件的作用，如果现在我们有一个Java 应用程序打包在myapplication.jar中， main class为 com.example.myapp.MyAppMain ，那么我们可以用以下的命令来运行 
```
java -classpath myapplication.jar com.example.myapp.MyAppMain 
```
这显然太麻烦了，现在我们来创建自己的manifest文件，如下：
```
Manifest-Version: 1.0 
Created-By: JDJ example 
Main-Class: com.example.myapp.MyAppMain 
```
这样我们就可以使用如下的命令来运行程序了：（明显简单多了，也不会造成无谓的拼写错误） 
```
java -jar myapplication.jar
```
### 管理JAR的依赖资源 
很少Java应用会仅仅只有一个jar文件，一般还需要 其他类库。比如我的应用程序用到了Sun 的 Javamail classes ，在classpath中我需要包含activation.jar 和 mail.jar,这样在运行程序时,相比上面的例子,我们要增加一些: 
```
java -classpath mail.jar:activation.jar -jar myapplication.jar 
```
在不同的操作系统中,jar包间的分隔符也不一样，在UNIX用“：”，在window中使用 “；”，这样也不方便 
同样，我们改写我们的manifest文件，如下 
```
Manifest-Version: 1.0 
Created-By: JDJ example 
Main-Class: com.example.myapp.MyAppMain 
Class-Path: mail.jar activation.jar 
```
（加入了Class-Path: mail.jar activation.jar，用空格分隔两个jar包） 
这样我们仍然可以使用和上例中相同的命令来执行该程序：
```
java -jar myapplication.jar 
```
Class-Path属性中包含了用空格分隔的jar文件，在这些jar文件名中要对特定的字符使用逃逸符，比如空格，要表示成"%20"，在路径的表示中，都采用“/”来分隔目录，无论是在什么操作系统中，(即使在window中)，而且这里用的是相对路径（相对于本身的JAR文件）： 
```
Manifest-Version: 1.0 
Created-By: JDJ example 
Main-Class: com.example.myapp.MyAppMain 
Class-Path: ext/mail.jar ext/activation.jar 
```
### Multiple Main Classes（多主类） 
还有一种Multiple Main Classes情况，如果你的应用程序可能有命令行版本 和GUI版本，或者一些不同的应用却共享很多相同的代码，这时你可能有多个Main Class，我们建议你采取这样的策略：把共享的类打成lib包，然后把不同的应用打成不同的包，分别标志主类：如下 
```
Manifest for myapplicationlib.jar: 
Manifest-Version: 1.0 
Created-By: JDJ example 
Class-Path: mail.jar activation.jar 
Manifest for myappconsole.jar: 
Manifest-Version: 1.0 
Created-By: JDJ example 
Class-Path: myapplicationlib.jar 
Main-Class: com.example.myapp.MyAppMain 
Manifest for myappadmin.jar: 
Manifest-Version: 1.0 
Created-By: JDJ example 
Class-Path: myapplicationlib.jar 
Main-Class: com.example.myapp.MyAdminTool 
```
在myappconsole.jar 和 myappadmin.jar的manifest文件中分别注明各自的 Main Class 

### Package Versioning 
完成发布后，如果使用者想了解 ，哪些代码是谁的？目前是什么版本？使用什么版本的类库？解决的方法很多 ，manifest提供了一个较好的方法，你可以在manifest文件中描述每一个包的信息。 
Java 秉承了实现说明与描述分离的原则，package 的描述 定义了package 是什么，实现说明 定义了谁提供了描述的实现，描述和实现包含 名、版本号和提供者。要得到这些信息，可以查看JVM的系统属性（使用 java.lang.System.getProperty() ） 
在manifest文件中，我可以为每个package定义描述和实现版本，声明名字，并加入描述属性和实现属性，这些属性是 
```
Specification-Title 
Specification-Version 
Specification-Vendor 
Implementation-Title 
Implementation-Version 
Implementation-Vendor 
```
当要提供一个类库或编程接口时，描述信息显得是很重要，见以下范例： 
```
Manifest-Version: 1.0 
Created-By: JDJ example 
Class-Path: mail.jar activation.jar 
Name: com/example/myapp/ 
Specification-Title: MyApp 
Specification-Version: 2.4 
Specification-Vendor: example.com 
Implementation-Title: com.example.myapp 
Implementation-Version: 2002-03-05-A 
Implementation-Vendor: example.com 
```
### Package Version 查询 
在manifest文件中加入package描述后，就可以使用Java提供的java.lang.Package class进行Package 的信息查询，这里列举3个最基本的获取package object的方法 
1.Package.getPackages():返回系统中所有定义的package列表 
2.Package.getPackage(String packagename):按名返回package 
3.Class.getPackage():返回给定class所在的package 
使用者这方法就可以动态的获取package信息. 
需要注意的是如果给定的package中没有class被加载,则也无法获得package 对象 

### Manifest 技巧 
总是以Manifest-Version属性开头 
每行最长72个字符，如果超过的化，采用续行 
确认每行都以回车结束，否则改行将会被忽略 
如果Class-Path 中的存在路径，使用"/"分隔目录，与平台无关 
使用空行分隔主属性和package属性 
使用"/"而不是"."来分隔package 和class ,比如 com/example/myapp/ 
class 要以.class结尾，package 要以 / 结尾 