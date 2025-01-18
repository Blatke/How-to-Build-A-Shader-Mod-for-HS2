# How to Build A Shader Mod for HS2
A shader mod in HS2 could be understood as a mod letting the materials of objects provided by other mods show a different effect such like changing tint color, texture, bump, transparency, glossiness, metallic, emission, or even some geometrical performance. This kind of shader mods are always in format of .zipmod file installed in HS2 (as well as in AI-Shoujo) and can be displayed and loaded on MaterialEditor tab.

Commonly, a custom shader used in a custom mod is only applied to this mod, only shown on MaterialEditor tab when this mod's objects are selected, and could not be applied in other mods. For getting a shader be used among various mods and be displayed corresponding properties (understood as the options) on MaterialEditor tab, shader mod is needed, and so is modding it.

This tutorial is to give a brief introduction to <ins>the modders who are at least experienced in modding for HS2 studio items</ins>, letting them know the rough knowledge on building a shader mod. 

> [!TIP]
> In this tutorial, I will not tell how to build a common mod, such as studio item, from scratch. If you're a newbie in modding studio items, especially by using [hooh's Modding Tool](https://hooh-hooah.github.io/#/README), it suggests to check my other tutorial (available in both English and Chinese languages) on: https://www.blatke.cc/index.html#tutorials.
