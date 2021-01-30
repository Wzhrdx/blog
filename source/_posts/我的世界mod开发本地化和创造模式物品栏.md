---
title: 我的世界mod开发本地化和创造模式物品栏
date: 2020-9-10
categories:
  - 我的世界mod开发
tags:
  - 我的世界mod开发
  - Forge
  - Mod
  - Java
cover: 'https://cloud.miiiku.xyz/src/images/cover/cover-08.jpg'
pageview: 908
abbrlink: 5de4689c
---
# 创造模式物品栏
那么显而易见的是，give命令实在是太麻烦了，所以我们需要将物品放进一个创造模式物品栏里。
我们直接在`ItemSilverNugget()`构造器里加一句`this.setCreativeTab(CreativeTabs.FOOD);`就好了，原版物品栏的名称可以在CreativeTabs类里找到
这里我们将银粒放入了食物栏（恶趣味）
#新的创造模式物品栏
创建物品栏有两种方法一种是直接添加到主类里，一种是新建一个Java类这里我推荐第二种方法
## 方法一
在XenoAge.java这个主类里添加字段：
~~~java
public static final CreativeTabs TAB_XENO_AGE= new CreativeTabs(MODID) {
    @Override
    public ItemStack createIcon() {
        return new ItemStack(ItemRegistryHandler.SILVER_NUGGET);
    }
    
};
~~~
再在`ItemSilverNugget()`构造器里加入`this.setCreativeTab(XenoAge.TAB_XENO_AGE);`
重启游戏，你会看到一个新的创造模式物品栏。
这里`SILVER_NUGGET`的材质将作为创造物品栏的图标显示

*注意：必须以某种方式设置 Tab 的 Icon，如果留 null会引发崩溃*
## 方法二
新建一个类`src/main/java/com/wzhrdx/XenoAge/creativetab/TabXenoAge.java`
~~~java
package com.wzhrdx.XenoAge.creativetab;

import com.wzhrdx.XenoAge.item.ItemRegistryHandler;
import net.minecraft.creativetab.CreativeTabs;
import net.minecraft.item.ItemStack;

public class TabXenoAge extends CreativeTabs {
    public static final TabXenoAge TAB_XENO_AGE = new TabXenoAge();
    public TabXenoAge(){
        super("xenoage");
    }
    @Override
    public ItemStack createIcon(){
        return new ItemStack(ItemRegistryHandler.SILVER_NUGGET);
    }

}

~~~
再在`ItemSilverNugget()`构造器里加入`this.setCreativeTab(TabXenoAge.TAB_XENO_AGE);`
重启游戏，你会看到一个新的创造模式物品栏。
# 物品本地化
现在我们的物品还是一个`item.xenoage.silvernugget.name`的名字，我们需要给他们添加语言文件
*   我们需要语言文件，将上面的代指名称翻译成各种语言，翻译后的名称叫本地化名称
*   代指名称使用物品的`setUnlocalizedName()`方法进行设置(新的版本为`setTranslationKey()`)
*   本地化名称使用语言文件(\*.lang)进行设置。
新建包`resources/assets/mymod/lang/`，新建 en\_us.lang 文件，对应英文版本，在里面写入（无需多余空格、引号等字符）：
~~~lang
item.xenoage.silvernugget.name=Silver Nugget
itemGroup.xenoage=Xeno Age
item.xenoage.silveringot.name=Silver Ingot
~~~
然后我们新建 zh_cn.lang，对应中文版本，写法同上。
~~~lang
item.xenoage.silvernugget.name=银粒
itemGroup.xenoage=魔法时代
item.xenoage.silveringot.name=银锭
~~~
*注意：1.12语言文件名应该使用全小写，比如 zh\_cn 而不是 zh\_CN*
*****
# 练习
* [ ] >练习1 给`silverore`添加英文名字`Silver Ore`
* [ ] >练习2 给``silverore``添加中文名字 `银矿`
* [ ] >提示 方块和物品不一样，他是`titl`开头，你也可以打开游戏查看UnlocalizedName
<details>
<summary>答案</summary>
<pre><blockcode>// en_us.lang
tile.xenoage.silverore.name=Silver Org
························································
// zh_cn.lang
tile.xenoage.silverore.name=银矿 </blockcode></pre>
</details>
