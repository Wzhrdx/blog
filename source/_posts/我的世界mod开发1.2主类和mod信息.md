---
title: 我的世界mod开发1.2主类和mod信息
categories:
  - 我的世界mod开发
tags:
  - 我的世界mod开发
  - Forge
  - Mod
  - Java
cover: 'https://cloud.miiiku.xyz/src/images/cover/cover-01.jpg'
abbrlink: 9b5d0990
date: 2020-08-22 00:00:00
---

在这章开始前我们先修改一下信息打开`build.gradle`找到 

~~~java
version = "1.0"

group = "com.yourname.modid"

archivesBaseName = "modid"
~~~

* **version = '1.0'** ：模组的[版本号](https://baike.baidu.com/item/%E7%89%88%E6%9C%AC%E5%8F%B7/7674145?fr=aladdin)（版本的标识号，[Maven的版本号规则](https://cwiki.apache.org/confluence/display/MAVENOLD/Versioning)，[语义化版本](https://semver.org/lang/zh-CN/)）。

* **group = 'com.yourname.modid'** ：这是[GroupID](https://blog.csdn.net/iteye\_19209/article/details/82655261?utm\_medium=distribute.pc\_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel\_param&depth\_1-utm\_source=distribute.pc\_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-1.channel\_param)，请遵循[命名规则](https://docs.oracle.com/javase/specs/jls/se6/html/packages.html#7.7)（[Maven](http://maven.apache.org/guides/mini/guide-naming-conventions.html)）。

* **archivesBaseName = 'modid'** ：这个关联到你最后导出的Jar文件名，直译过来是“归档基本名称”。

了解了意思，大家就应该知道要填什么了吧！

>[info] version = ' [这里填模组版本号] '

> group = ' [填GroupID，要根据你的文件夹填，在工程文件夹（MDK）下的src\main\java下] '

> archivesBaseName = ' [填模组ID] '

> * **group**：java文件夹下你可以找到`com\example\examplemod`，这是默认的，但我建议重命名为`com\[作者名]\[模组ID]`（这个不是一个文件夹的名称，在com下的example文件夹重命名为[作者名]，重命名后这个文件夹下的examplemod重命名为模组ID），比如我填的是`com.tangguo.pe`（每个文件夹名之间用点隔开）。tangguo就是我的名字，pe就是我的模组ID的缩写。那么我的文件夹名就是`com\tangguo\pe`

> * **模组ID是什么**：该模组的唯一标识符，不要填写出英文字母与一些半角符号（_）外的其他字符（空格替换为下划线）。必须全小写字母且不得超过64个字符。</p>

> ***（除了模组的本地化外都不要写中文与特殊字符）***

修改为

~~~java
version = "1.0"

group = "com.wzhrdx.xenoage"

archivesBaseName = "xenoage"
~~~

在这篇教程里我将哪出我写的模组的一部分进行讲解

那么废话不多说，我们开始正题，本人使用的是IDEA

# 新建一个主类

这里我们直接双击`ipr`文件，然后IDEA就会导入工程了，这里在`scr/main/java/`下新建一个包`com.wzrdx.XenoAge`

然后创建一个java类`XenoAge`,代码如下

~~~java
package com.wzhrdx.XenoAge;

import net.minecraft.init.Blocks;

import net.minecraftforge.fml.common.Mod;

import net.minecraftforge.fml.common.event.FMLInitializationEvent;

import net.minecraftforge.fml.common.event.FMLPreInitializationEvent;

import org.apache.logging.log4j.Logger;

@Mod(modid = XenoAge.MODID, name = XenoAge.NAME, version = XenoAge.VERSION, acceptedMinecraftVersions = "1.12.2")

public class XenoAge {

public static final String MODID = "xenoage";

public static final String NAME = "Xeno Age";

public static final String VERSION = "1.0.0";

private static Logger logger;

@Mod.EventHandler

public void preInit(FMLPreInitializationEvent event)

{

logger = event.getModLog();

}

@Mod.EventHandler

public void init(FMLInitializationEvent event)

{

// some example code

logger.info("DIRT BLOCK >> {}", Blocks.DIRT.getRegistryName());

}

}
~~~

* @Mod(modid = "XenoAge",name = "XenoAge",version="1.0")//modid是Mod的唯一标识符（ID）,name是Mod的名称,version是Mod的版本

* @Mod.EventHandler 则标识了三个FML事件，三个方法分别对应三个事件，调用proxy的对应方法。

* * 含有`FMLPreInitializationEvent`参数的方法（这里是`preInit`）在所有Mod初始化之前调用，**这时候应该加载配置文件，实例化物品和方块，并注册它们**。

* * 含有`FMLInitializationEvent`参数的方法（这里是`init`）用于该Mod的初始化，**这时候应该为Mod进行设置，如注册合成表和烧炼系统，并且向其他Mod发送交互信息**。

* * 含有`FMLPostInitializationEvent`参数的方法（这里是`postInit`）在所有Mod都初始化之后调用，**这时候应该接收其他Mod发送的交互信息，并完成对Mod的设置**。

这样我们就完成第一步了

# 完善你的mod信息

打开`src/main/resources/mcmod.info`

~~~json
[

{

"modid": "examplemod",

"name": "Example Mod",

"description": "Example placeholder mod.",

"version": "${version}",

"mcversion": "${mcversion}",

"url": "",

"updateUrl": "",

"authorList": ["ExampleDude"],

"credits": "The Forge and FML guys, for making this example",

"logoFile": "",

"screenshots": [],

"dependencies": []

}

]
~~~

修改如下

~~~json
[

{

"modid": "xenoage",

"name": "Xeno Age",

"description": "It's a 1.12.2 mod",

"version": "${version}",

"mcversion": "${mcversion}",

"url": "",

"updateUrl": "",

"authorList": ["wzhrdx"],

"credits": "The Forge and FML guys, for making this example",

"logoFile": "",

"screenshots": [],

"dependencies": []

}

]
~~~

