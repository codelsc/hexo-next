---
title: maven聚合工程的创建和聚合工程的打包
date: 2013-03-15 19:53:25
categories:  
 - java
tags: 
 - maven
 - 聚合工程 
---

## 第一步：创建父工程millery-manage，如图：
右击空白处，new创建新maven工程：
![][1]
<!-- more -->

这里跳过默认的骨架，使用自动义的骨架
![][2]

这里父工程必须使用pom打包方式
![][3]

## 第二步：创建子工程
右击父工程，创建maven module工程：
![][4]

跳骨默认骨架，输入子工程名
![][5]

定义子工程，这里是以表现层为例，是web工程，所以打包方式为war，如果是其他非web工程就可以打包成jar，这一点需要注意。
![][6]

其他工程步骤类似，需要注意的是打包方式的选择。
工程创建完成后现象:
所有的子工程目录不是单独的存在，而是直接保存在父工程目录下。
![][7]

父工程pom.xml文件内容：
![][8]

子工程pom.xml文件内容：
![][9]

硬盘中聚合工程存储目录结构：
![][10]

## 第三步：打包项目，此时不需要每个项目都打包，聚合工程只需要对父工程进行打包即可。
右击millery-manage工程-->Run As-->Maven Build ...，然后出现如图的对话框，按图操作。
![][11]

控制台输出内容：
![][12]

##第四步：最后一步看打包后的效果
进入millery-manage-web硬盘目录-->target-->右击millery-manage-web.war使用压缩软件打开-->WEB-INF-->lib，在lib中就可以看到下面三个jar包，就是聚合工程中的另外三个子工程，这就意味着这三个工程已经包含在web工程下，无需再重复的进行打包操作。
![][13]


[1]: /images_post/maven_pom/maven_1.png
[2]: /images_post/maven_pom/maven_2.png
[3]: /images_post/maven_pom/maven_3.png
[4]: /images_post/maven_pom/maven_4.png
[5]: /images_post/maven_pom/maven_5.png
[6]: /images_post/maven_pom/maven_6.png
[7]: /images_post/maven_pom/maven_7.png
[8]: /images_post/maven_pom/maven_8.png
[9]: /images_post/maven_pom/maven_9.png
[10]: /images_post/maven_pom/maven_10.png
[11]: /images_post/maven_pom/maven_11.png
[12]: /images_post/maven_pom/maven_12.png
[13]: /images_post/maven_pom/maven_13.png