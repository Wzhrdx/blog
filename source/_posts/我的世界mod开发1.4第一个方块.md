---
title: 我的世界mod开发1.4第一个方块
categories:
  - 我的世界mod开发
tags:
  - 我的世界mod开发
  - Forge
  - Mod
  - Java
cover: 'https://cloud.miiiku.xyz/src/images/cover/cover-07.jpg'
pageview: 908
abbrlink: 2f6cd6fe
date: 2020-09-01 00:00:00
---
制作一个方块跟制作物品一样也就两个步骤：
    1.创建一个方块,并添加材质
    2.实例化并注册这个方块
# 创建方块
在包`src/main/java/com/wzhrdx/XenoAge`下新建类`block.BlockSilverOre`并继承 Block 类：
~~~java
package com.wzhrdx.XenoAge.block;

import com.wzhrdx.XenoAge.XenoAge;
import com.wzhrdx.XenoAge.creativetab.TabXenoAge;
import net.minecraft.block.Block;
import net.minecraft.block.material.Material;

public class BlockSilverOre extends Block {

    public BlockSilverOre(){
        super(Material.ROCK);
        this.setTranslationKey(XenoAge.MODID + ".silverore");
        this.setRegistryName("silver_ore");
        this.setCreativeTab(TabXenoAge.TAB_XENO_AGE);
        this.setHardness(1.5F);
        this.setHarvestLevel("pickaxe",2);

    }
}

~~~
 * [ ] > super构造函数，用于方块颜色如ROCK，
设置成 ROCK 之后地图上显示的方块颜色就是石头的灰色`Material.AIR`
`Material.GRASS`
`Material.GROUND`
`Material.WOOD`
`Material.ROCK`
 更多对着`Material.ROCK`按CTRL+鼠标左键
 * [ ] >  `setBlockUnbreakable`方法用于设定方块的硬度为-1，即不能损坏。 `setHardness`方法用于设定方块的硬度，如黑曜石是50，铁块5，金块3，圆石2，石头1.5，南瓜1，泥土0.5，甘蔗0，基岩-1。
    `setHarvestLevel`方法用于设定方块的可挖掘等级，如钻石镐是3，铁2，石1，木金0。
   `setLightLevel`方法用于设定方块的光照，其周围的光照为设定值x15，如岩浆1.0，对应15，红石火把0.5，对应7.5。
  `setLightOpacity`方法用于设定方块的透光率，数值越大透光率越低，如树叶和蜘蛛网是1，水和冰3。
   `setResistance`方法用于设定方块的爆炸抗性，如木头的抗性为4，石头为10，黑曜石为2000，基岩为6000000。
   `setStepSound`方法用于设定走在方块上的响声。
  `setTickRandomly`方法用于设定方块是否会接受随机Tick（如农作物）。

*****
# 注册方块
在包`src/main/java/com/wzhrdx/XenoAge`下新建类`block.BlockRegistryHandler`
~~~java
package com.wzhrdx.XenoAge.block;

import net.minecraft.block.Block;
import net.minecraftforge.event.RegistryEvent;
import net.minecraftforge.fml.common.Mod;
import net.minecraftforge.fml.common.eventhandler.SubscribeEvent;
import net.minecraftforge.registries.IForgeRegistry;

@Mod.EventBusSubscriber
public class BlockRegistryHandler {
    public static final BlockSilverOre BLOCK_SILVER_ORE = new BlockSilverOre();
    @SubscribeEvent
    public static void onRegistry(RegistryEvent.Register<Block> event) {
        IForgeRegistry<Block> registry = event.getRegistry();
        registry.register(BLOCK_SILVER_ORE);
    }

}

~~~
修改`src/main/java/com/wzhrdx/XenoAge/item/ItemRegistryHandler.java`
在`public static Item SILVER_NUGGET = new ItemSilverNugget();`下方添加
~~~java
public static final ItemBlock ITEM_SILVER_ORE = withRegistryName(new ItemBlock(BlockRegistryHandler.BLOCK_SILVER_ORE));

public static ItemBlock withRegistryName(ItemBlock item){
    item.setRegistryName(item.getBlock().getRegistryName());
    return item;
}
~~~
# 注册材质
继续修改`src/main/java/com/wzhrdx/XenoAge/item/ItemRegistryHandler.java`
，
添加如下代码
~~~java
registerModel(SILVER_NUGGET);
registerModel(ITEM_SILVER_ORE); //新添加内容
~~~
# 添加材质
创建包`src/main/resources/assets/xenoage/blockstates`然后创建`silver_ore.json`
`src/main/resources/assets/xenoage/blockstates/silver_ore.json`
~~~json
{
  "variants": {
    "normal": {
      "model": "xenoage:silver_ore"
    }
  }
}
~~~
创建`src/main/resources/assets/xenoage/models/block/silver_ore.json`
~~~json
{
  "parent": "block/cube",
  "textures": {
    "particle": "xenoage:blocks/silver_ore",
    "down": "xenoage:blocks/silver_ore",
    "up": "xenoage:blocks/silver_ore",
    "north": "xenoage:blocks/silver_ore",
    "south": "xenoage:blocks/silver_ore",
    "west": "xenoage:blocks/silver_ore",
    "east": "xenoage:blocks/silver_ore"
  }
}
~~~
创建`src/main/resources/assets/xenoage/models/item/silver_ore.json`
~~~json
{
  "parent": "xenoage:block/silver_ore"
}
~~~
创建`src/main/resources/assets/xenoage/textures/blocks`
放入材质`silver_ore.png`  ![dRYcmF.png](https://s1.ax1x.com/2020/08/26/dRYcmF.png)
*****
# 本章全部代码
`src/main/java/com/wzhrdx/XenoAge/item/ItemRegistryHandler.java`
~~~java
package com.wzhrdx.XenoAge.item;

import com.wzhrdx.XenoAge.block.BlockRegistryHandler;
import net.minecraft.client.renderer.block.model.ModelResourceLocation;
import net.minecraft.item.Item;
import net.minecraft.item.ItemBlock;
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

    public static Item SILVER_NUGGET = new ItemSilverNugget();
    public static final ItemBlock ITEM_SILVER_ORE = withRegistryName(new ItemBlock(BlockRegistryHandler.BLOCK_SILVER_ORE));

    public static ItemBlock withRegistryName(ItemBlock item){
        item.setRegistryName(item.getBlock().getRegistryName());
        return item;
    }
    @SubscribeEvent
    public static void onRegistry(RegistryEvent.Register<Item> event){
        IForgeRegistry<Item> registry = event.getRegistry();
        registry.register(SILVER_NUGGET);
        registry.register(ITEM_SILVER_ORE);
    }

    @SideOnly(Side.CLIENT)
    private static void registerModel (Item item){
        ModelResourceLocation modelResourceLocation = new ModelResourceLocation(item.getRegistryName(),"inventory");
        ModelLoader.setCustomModelResourceLocation(item,0, modelResourceLocation);
    }
    @SubscribeEvent
    @SideOnly(Side.CLIENT)
    public static void onModelRegistry(ModelRegistryEvent event){
        registerModel(SILVER_NUGGET);
        registerModel(ITEM_SILVER_ORE);
    }

}

~~~
# 练习
<details>
<summary>答案</summary>
<pre><blockcode> 

 </blockcode></pre>
</details>