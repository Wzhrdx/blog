---
title: 我的世界mod开发1.3第一个物品
categories:
  - 我的世界mod开发
tags:
  - 我的世界mod开发
  - Forge
  - Mod
  - Java
cover: 'https://cloud.miiiku.xyz/src/images/cover/cover-19.jpg'
abbrlink: c137323d
date: 2020-08-23 00:00:00
---

制作一个物品分为两步：

1.创建一个物品，并添加材质

2.实例化并注册这个物品

那么由于我懒我就不介绍了，自己直接照抄xiao_bang的介绍了

>在Minecraft中，有烈火般的烈焰棒（废话），咱们这次做来做个钻石颜色的棒子（真的想不出来要做什么了），这一次教程会一步一步地带着你完成制作新物品的全过程。

好现在我们来创建一个新的物品，如果你有经常翻看net.minecraft源码,在item包里面会看到在mc中遇到的各种物品，都继承了Item类，那么我们做的物品也要继承这个类（继承这个类的一般都是物品）

新建一个包com.wzhrdx.XenoAge.item,并在其中创建一个类ItemSilverNugget(注意注意类的名称前面推荐是Item,禁止乱搞,xiaobang路过)

# 创建一个物品

这里由于我们写的是`XenoAge`使用我们在这里创建`银粒`。新建java类`item.ItemSilverNugget`

~~~java
package com.wzhrdx.XenoAge.item;

import com.wzhrdx.XenoAge.XenoAge;

import net.minecraft.item.Item;

public class ItemSilverNugget extends Item {

public ItemSilverNugget() {

this.setMaxStackSize(64);

this.setTranslationKey(XenoAge.MODID + ".silvernugget");

this.setRegistryName("silver_nugget");

}

}
~~~

* `setTranslationKey`这是新的方法，以前的方法为`setUnlocalizedName()`

`setMaxStackSize()`这里是设置最大堆叠数目，如果不特殊强调就是默认64(比如说我不写这个方法😀)

设置注册名字，当你在游戏中想要获得你的物品那就需要这个名字，比如在本教程你需要这样做`give @a xenoage:silver_nugget`，当然此时如果你启动的话，不出意外会报错

# 实例化并注册

新建java类`item.ItemRegistryHandler`

`src/main/java/com/wzhrdx/XenoAge/item/ItemRegistryHandler.java`

~~~java
package com.wzhrdx.XenoAge.item;

import net.minecraft.item.Item;

import net.minecraftforge.event.RegistryEvent;

import net.minecraftforge.fml.common.Mod;

import net.minecraftforge.fml.common.eventhandler.SubscribeEvent;

import net.minecraftforge.registries.IForgeRegistry;

@Mod.EventBusSubscriber

public class ItemRegistryHandler {

public static final ItemSilverNugget SILVER_NUGGET = new ItemSilverNugget();

@SubscribeEvent

public static void onRegistry(RegistryEvent.Register<Item> event)

{

IForgeRegistry<Item> registry = event.getRegistry();

registry.register(SILVER_NUGGET);

} //这里是实例话物品
~~~

# 绑定材质

为`ItemRegistryHandler`添加一个方法，修改后如下

~~~java
package com.wzhrdx.XenoAge.item;

import net.minecraft.client.renderer.block.model.ModelResourceLocation;

import net.minecraft.item.Item;

import net.minecraftforge.client.event.ModelRegistryEvent;

import net.minecraftforge.client.model.ModelLoader;

import net.minecraftforge.event.RegistryEvent;

import net.minecraftforge.fml.common.Mod;

import net.minecraftforge.fml.common.eventhandler.SubscribeEvent;

import net.minecraftforge.fml.relauncher.Side;

import net.minecraftforge.fml.relauncher.SideOnly;

import net.minecraftforge.registries.IForgeRegistry;

@Mod.EventBusSubscriber

public class ItemRegistryHandler {

public static final ItemSilverNugget SILVER_NUGGET = new ItemSilverNugget();

@SubscribeEvent

public static void onRegistry(RegistryEvent.Register<Item> event)

{

IForgeRegistry<Item> registry = event.getRegistry();

registry.register(SILVER_NUGGET);

} //这里是实例话物品

@SubscribeEvent //监听net.minecraftforge.client.event.ModelRegistryEvent事件

@SideOnly(Side.CLIENT)

public static void onModelRegistry(ModelRegistryEvent event){

ModelLoader.setCustomModelResourceLocation(SILVER_NUGGET, 0,

new ModelResourceLocation(SILVER_NUGGET.getRegistryName(),"inventory"));

}

} //这里是绑定材质
~~~

创建包`assets.xenoage.models.item`，创建json文件`silver_nugget.json`

`src/main/resources/assets/xenoage/models/item/silver_nugget.json`

~~~json
{

"parent": "item/generated",

"textures": {

"layer0": "xenoage:items/silver_nugget"

}

}
~~~

创建材质包位置`src/main/resources/assets/xenoage/textures/items`

将材质放入，材质的名字要和你的注册名字一样这里是`silver_nugget.png`，"![dWVxvn.png](https://s1.ax1x.com/2020/08/26/dWVxvn.png)

这里我们运行游戏输入`/give @a xenoage:silver_nugget`我们就会得到这个物品

*****

# 练习
* [ ] >练习1 创建一个名为`silver_ingot`的物品Java类名为`ItemSilverNugget`材质图片![dWAfaD.png](https://s1.ax1x.com/2020/08/26/dWAfaD.png)
要求1：设置最大堆叠数目为64
要求2：设置注册名为`silver_ingot`
要求3：设置`UnlocalizedName`为`modid+#`
要求4：将其放入创造栏中

* [ ] >练习2 注册、实例化物品并注册材质

* [ ] >练习3 配置json文件使材质正常显示

<details>

<summary>答案1</summary>

<pre><blockcode> 

package com.wzhrdx.XenoAge.item;

import com.wzhrdx.XenoAge.XenoAge;

import com.wzhrdx.XenoAge.creativetab.TabXenoAge;

import net.minecraft.item.Item;

public class ItemSilverIngot extends Item {

public ItemSilverIngot() {

this.setMaxStackSize(64);

this.setCreativeTab(TabXenoAge.TAB_XENO_AGE);

this.setTranslationKey(XenoAge.MODID + ".silveringot");

this.setRegistryName("silver_ingot");

}

}

</blockcode></pre> 

</details>

<details>

<summary>答案2</summary>

<pre><blockcode> 

//在ItemRegistryHandler对应位置下添加

public static Item SILVER_INGOT = new ItemSilverIngot();

registry.register(SILVER_INGOT);

registerModel(SILVER_INGOT);

</blockcode></pre> 

</details>

<details>

<summary>答案3</summary>

<pre><blockcode> 

1.创建assets/xenoage/models/item/silver_ingot.json

2.silver_ingot.json

{

"parent": "item/generated",

"textures": {

"layer0": "xenoage:items/silver_ingot"

}

}

</blockcode></pre> 

</details>

