# How to Build A Shader Mod for HS2
A shader mod in HS2 could be understood as a mod letting the materials of objects provided by other mods show a different effect such like changing tint color, texture, bump, transparency, glossiness, metallic, emission, or even some geometrical performance. This kind of shader mods are always in format of .zipmod file installed in HS2 (as well as in AI-Shoujo) and can be displayed and loaded on MaterialEditor tab.

Commonly, a custom shader used in a custom mod is only applied to this mod, only shown on MaterialEditor tab when this mod's objects are selected, and could not be applied in other mods. For getting a shader be used among various mods and be displayed corresponding properties (understood as the options) on MaterialEditor tab, shader mod is needed, and so is modding it.

This tutorial is to give a brief introduction to <ins>the modders who are at least experienced in modding for HS2 studio items</ins>, letting them know the rough knowledge on building a shader mod. 

> [!TIP]
> In this tutorial, I will not go through with how to build a common mod, such as studio item, from scratch. If you're a newbie in modding studio items, especially by using [hooh's Modding Tool](https://hooh-hooah.github.io/#/README), it suggests to check my other tutorial (available in both English and Chinese languages) on: https://www.blatke.cc/index.html#tutorials.

## Acknowledgement
I would like to give special appreciation to [Hanmen](https://www.patreon.com/c/hanmen), who directly and generously helped me with modding this shader to let MaterialEditor in HS2 and AIS successfully load it.

I also thank kky-is, [Pizdatyi](https://www.pixiv.net/users/86387918), [Getdowncrazy](https://www.patreon.com/c/realillusionGDC/), [enimaroah](https://github.com/enimaroah-cubic/Sb3UGS/wiki) and ElusiveCake, who spent time in kindly replying to my questions about shader modding, and thus have brought me with enlightenment.

## Prepare the Assetbundle
The first step is to get an assetbundle, in format of .unity3d, in which there has to be gameobjects, textures, materials, compiled shaders and other stuff packed in it: 

In Unity Editor **2018.4.11f**, I created a shader file, namely _tutorial.shader_ and with the title "_Custom/tutorial_", and a material, namely _mat.mat_, in Project window. 

![image](https://github.com/user-attachments/assets/33888cbb-7e1b-4c61-abd0-ff9e0628e4f9)

Drag and drop tutorial.shader into mat.mat, so the latter is now adopt tutorial.shader as its shader, and can be checked in Inspector window when selecting mat.mat.

![image](https://github.com/user-attachments/assets/90c1b120-78d0-47e8-a940-85bdba647871)

Besides the properties given by default, I added a custom property in type of color, __CustomProperty_ (with the display name of _Custom Property_). In this shader, _CustomProperty will blend color with _Color, such like Red(1,0,0) * Blue(0,0,1) = Black(0,0,0). This shader file can be found on the [Examples](https://github.com/Blatke/How-to-Build-A-Shader-Mod-for-HS2/tree/main/Examples) folder.

![image](https://github.com/user-attachments/assets/765e0c70-f8ba-4604-b4df-c96c17972bf5)

![image](https://github.com/user-attachments/assets/7d9a8368-10ec-4121-8901-45ef7ef1b781)

I assigned Albedo (_MainTex) to a custom texture namely mask.png, and created a **cube** in the scene, then drag and drop the material to it.

![image](https://github.com/user-attachments/assets/1b8d50e7-116e-4e11-a444-6aee0573f215)

![image](https://github.com/user-attachments/assets/92778ddb-f8bb-4a9d-a27b-b55163e0ff43)

Now, build these stuff into an assetbundle. If you're using [Joan6694's method](https://mega.nz/folder/VlpAQZ7S#Q4x1zu3OlDSuuS_x-flqbA), I have no more things in this section to give you, and please move to the next section. 

If you are using **[hooh's Modding Tool](https://hooh-hooah.github.io/#/README)**, you can do what you always do for modding a studio item:

1. Create the prefab of the **Cube** in the created folder namely _prefabs_.
2. Create a **mod.xml** (mod.sxml) outside the _prefabs_ folder but without giving any tags between <list type="studioitem"></list>. Check the mod.xml as a template on: https://github.com/Blatke/How-to-Build-A-Shader-Mod-for-HS2/blob/main/Examples/mod.xml
3. Press the Build button on the window of hooh tools to build the mod. It builds everything in the _prefabs_ folder as well as eveything referred by these prefabs into an assetbundle namely _data_prefab_000.unity3d_, and packs it with a generated _manifest.xml_ file (and other lists) into a .zipmod file. For instance, mine is named _Tutorial_Shader.zipmod_.

![image](https://github.com/user-attachments/assets/687a98c3-5260-481c-ac13-30a7c8ddb2dd)

4. Use some uncompression software to open this .zipmod file, and uncompress the data_prefab_000.unity3d the assetbundle file to your desktop or somewhere else.

![image](https://github.com/user-attachments/assets/4207bc4b-8f13-4f93-8977-cf373fe10643)

Now you get the assetbundle. But don't remove the .zipmod file, we still need it.

## Transport Stuff
Create a new folder named _abdata_, then inside it create another new folder, name it _chara_. Then drop the assetbundle file you got, in this case _data_prefab_000.unity3d_ into the folder abdata/chara.

![image](https://github.com/user-attachments/assets/75262d60-c111-49ac-9bb0-815a083a8ecd)

Use **[SB3UtilityGUI](https://gitea.com/enimaroah/Sb3UGS/releases)** to open it. Why we created these folders above is to meet the requirements by SB3UtilityGUI.

![image](https://github.com/user-attachments/assets/64619f2c-d29e-4bb4-8df7-d0a1686509d0)
