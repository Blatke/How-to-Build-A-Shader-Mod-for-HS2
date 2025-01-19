# 如何为HS2制作着色器MOD
> [!TIP]
> For the English version, please check [**this**](https://github.com/Blatke/How-to-Build-A-Shader-Mod-for-HS2/blob/main/README.md).

HS2中的着色器mod（Shader Mod）可以被理解为一种让其他mod提供的物体材质显示不同效果的mod，例如改变色调、贴图、凹凸、透明度、光泽度、金属、发光，甚至一些几何表现。这种着色器mod采用.zipmod文件格式，安装在HS2（以及AI少女）中，可以在MaterialEditor（材质编辑器）选项卡上显示和加载。

通常，自定义mod中使用的自定义着色器仅应用于此mod，仅在选择此mod的对象时显示在“MaterialEditor”选项卡上，而不能应用于其他mod。为了在各种mod之间使用着色器并在“材质编辑”选项卡上显示相应的属性（理解为选项），需要着色器mod，对其进行修改也是如此。

本教程旨在为<ins>那些至少对HS2工作室物品有经验的modders</ins>提供简要介绍，使之了解如何<ins>基于Unity中已有的着色器</ins>来构建着色器mod。

> [!IMPORTANT]
> 在本教程中，我不会从头开始讲述如何构建一个通用mod，如工作室物品（Studio Item）。如果你是一个工作室物品的新手modder，尤其是[hooh Modding Tool](https://hooh-hooah.github.io/#/README)的使用者，建议查看我的其他教程（有英文和中文两种版本）：https://www.blatke.cc/index.html#tutorials

## 致谢

我要特别感谢[**Hanmen**](https://www.patreon.com/c/hanmen)，他直接而慷慨地为我的着色器mod制作提供帮助，让HS2和AIS中的MaterialEditor成功加载它。

我还要感谢**kky-is**、[**Pizdatyi**](https://www.pixiv.net/users/86387918)、[**Getdowncrazy**](https://www.patreon.com/c/realillusionGDC/)、[**enimaroah**](https://github.com/enimaroah-cubic/Sb3UGS/wiki)以及**ElusiveCake**，他们花时间善意地回答了我关于着色器修改的问题，给我带来了启示。

## 摘要

概括的说，制作着色器mod的过程包括：

1.构建一个.zipmod文件，使用指定着色器材质的物体打包到它内部的.unity3d资产包中。

2.如果你想让之后的着色器mod在MaterialEditor选项卡上加载着色器的同时，还要加载默认贴图，那么你要创建一个新的资产包并将贴图放入其中并打包到.zipmod文件中是必要的。

3.将MaterialEditor的标签添加到.zipmod文件中的manifest.xml脚本中，并引用上述资产包的路径。

## 准备资产包（AssetBundle）

第一步是获得一个.unity3d格式的资产包，其中必须包含游戏对象、贴图、材质、编译的着色器和其他内容：

在Unity编辑器**2018.4.11f**中，我在项目窗口中创建了一个着色器文件，名为 _tutorial.shader_，抬头标题为“_Custom/tutorial_”，以及一个材质，名为 _mat.mat_。由此，我们将基于这个tutorial.shader构建一个着色器mod。

![image](https://github.com/user-attachments/assets/33888cbb-7e1b-4c61-abd0-ff9e0628e4f9)

将tutorial.shader拖放到mat.mat中，这样后者现在采用tutorial.shader作为其着色器，并且可以在选择mat.mat时在检查器窗口（Inspector Window）中看到。

![image](https://github.com/user-attachments/assets/90c1b120-78d0-47e8-a940-85bdba647871)

除了默认提供的属性（Property）外，我还添加了一个Color类型的自定义属性 _\_CustomProperty_（显示名称为 _Custom Property_）。在此着色器中，_CustomProperty将颜色与_Color属性的颜色混合，例如红色（1,0,0）* 蓝色（0,0,1）= 黑色（0,0,0）。此着色器文件可以在[Examples](https://github.com/Blatke/How-to-Build-A-Shader-Mod-for-HS2/tree/main/Examples)文件夹中找到。

![image](https://github.com/user-attachments/assets/765e0c70-f8ba-4604-b4df-c96c17972bf5)

![image](https://github.com/user-attachments/assets/7d9a8368-10ec-4121-8901-45ef7ef1b781)

我将Albedo（_MainTex）指定到自定义贴图mask.png，并在场景中创建了一个**Cube**立方体物体，然后将材质拖放到其中。

![image](https://github.com/user-attachments/assets/1b8d50e7-116e-4e11-a444-6aee0573f215)

![image](https://github.com/user-attachments/assets/92778ddb-f8bb-4a9d-a27b-b55163e0ff43)

现在，把这些东西做成一个资产包。如果你使用的是[Joan6694的方法](https://mega.nz/folder/VlpAQZ7S#Q4x1zu3OlDSuuS_x-flqbA)，那么这一节我就没有更多的东西要给你了，请转到下一节。

如果你正在使用[**hooh Modding Tool**](https://hooh-hooah.github.io/#/README)，你可以像往常一样制作工作室物品：

1.创建文件夹 _prefabs_ 并在其中创建**Cube**的预制体（Prefab）。

2.在 _prefabs_ 文件夹外创建一个**mod.xml**（或mod.sxml），但不要在\<list type=“studioitem”\>\</list\>之间添加任何标签。可在这里获取mod.xml模板：https://github.com/Blatke/How-to-Build-A-Shader-Mod-for-HS2/blob/main/Examples/mod.xml

3.按下hooh tools窗口上的Build按钮来制作mod。它将 _prefabs_ 文件夹中的所有内容以及这些预制体引用的所有内容都放入一个资产包中，即 _data_prefab_000.unity3d_ ，并将它跟一并生成的 _manifest.xml_ 文件（和其他list文件）打包进.zipmod文件中。假设我们做成的mod名为 _Tutorial_Shader.zipmod_ 。

![image](https://github.com/user-attachments/assets/687a98c3-5260-481c-ac13-30a7c8ddb2dd)

4.使用解压缩软件打开此.zipmod文件，并将其中的data_prefab_000.unity3d资产包文件解压缩到桌面或其他地方。

![image](https://github.com/user-attachments/assets/4207bc4b-8f13-4f93-8977-cf373fe10643)

现在你得到了资产包。但是不要急于删除.zipmod文件，我们仍然需要它。

## 转移贴图

如果你希望着色器mod在将其加载到MaterialEditor上的材质时使用其自带贴图，则必须将资产包中打包的贴图转存到另一个资产包里，并在manifest.xml中分别引用它们。否则，贴图将不会与着色器一起加载过来。按照Hanmen的说法，如果在同一个资产包的材质中使用了贴图，那么MaterialEditor将无法读取该贴图。也许这是MaterialEditor的问题，但目前我们只能将就它。

如果你的着色器mod不需要内置贴图可供加载，请跳到下一节。但如果你有此类需求，请按照以下步骤操作：

1.创建一个名为 _abdata_ 的文件夹，然后在其中创建另一个文件夹 _chara_ 。然后将你获得的资产包文件（在本例中为_data_prefab_000.unity3d_）放入文件夹abdata/chara里面。

![image](https://github.com/user-attachments/assets/75262d60-c111-49ac-9bb0-815a083a8ecd)

2.使用[**SB3UtilityGUI**](https://gitea.com/enimaroah/Sb3UGS/releases)打开它，然后双击它以查看其内容。我们之所以在上面创建这些文件夹，是为了满足SB3UtilityGUI的要求。

![image](https://github.com/user-attachments/assets/64619f2c-d29e-4bb4-8df7-d0a1686509d0)

3.用一个几乎为空的资产包作为目标文件，准备将贴图从你获得的资产包转存到该目标文件里。也要把它放在abdata/chara文件夹中。这个“空的”资产包模板可在以下位置找到：https://github.com/Blatke/How-to-Build-A-Shader-Mod-for-HS2/blob/main/Examples/tex.unity3d

4.将“空”资产包（例如 _tex.unity3d_ ）拖放到SB3UtilityGUI中。然后双击它以查看其内容结构。

![image](https://github.com/user-attachments/assets/ce16d4c8-b418-4df1-b98a-43e8201dfd5c)

5.拖动data_prefab_000.unity3d中的贴图文件 _mask.png_ ，将其放入tex.unity3d中以完成传输。然后保存tex.unity3d。

![image](https://github.com/user-attachments/assets/56dd16f1-4a26-4eed-bb2e-6695c20846bb)

6.将tex.unity3d压缩到.zipmod文件中，将其放在原始资产包data_prefab_000.unity3d旁边。

![image](https://github.com/user-attachments/assets/f82564db-b29a-4593-a665-ef09f89830d6)

现在，我们将data_prefab_000.unity3d称为着色器资产包，将tex.unity3d表示为贴图资产包。

## 调整Manifest.xml

加载mod时，MaterialEditor会读取这个mod的**manifest.xml**中的相关标签（tags）。这些标签可以让MaterialEditor加载指定的着色器，在其选项卡上显示着色器中使用的属性，以及将资产包中打包的自带贴图加载到材质中。无论如何，我们必须将这种标签添加到manifest.xml中。有关标签的模板可以在以下网址中找到：https://github.com/IllusionMods/KK_Plugins/blob/master/Guides/Material%20Editor%20Guide/shader_manifest_template.xml

对于本例中使用的着色器属性，我们可以在 **\</manifest\>** 之前添加如下标签：

```xml
<AI_MaterialEditor>
  <Shader Name="Custom/tutorial" AssetBundle="tutorial_shader/tutorial_shader/data_prefab_000.unity3d" Asset="Cube" >
    <Property Name="Color" Type="Color" DefaultValue="1,1,1,1" />
    <Property Name="MainTex" Type="Texture" DefaultValue="mask" DefaultValueAssetBundle="tutorial_shader/tutorial_shader/tex.unity3d"/>
    <Property Name="Glossiness" Type="Float" Range="0,1" DefaultValue="0.5" />
    <Property Name="Metallic" Type="Float" Range="0,1" DefaultValue="0.5" />
    <Property Name="CustomProperty" Type="Color" DefaultValue="1,1,1,1" />
  </Shader>
</AI_MaterialEditor>
```

其中，在\<Shader\>中，**Name**是 _tutorial.Shader_ 中抬头标题的着色器名称；**AssetBundle**是着色器资产包_data_prefab_000.unity3d_的路径；**Asset**是使用此着色器材质_mat.mat的 _Cube_ 物体；

在\<Property\>中，**Name**是tutorial.shader中的属性名，只不过没有着色器文件中私有声明前缀“_”；**Type**是属性的类型；**DefaultValue**是加载着色器时赋予属性的默认参数或资源名称，例如这个“mask”值指的是引用了贴图资产包中的 _mask.png_ ；**DefaultValueAssetBundle**是应用于贴图类型属性的资产包路径，例如包含mask.png的_tutorial_shader/tutorial_shader/tex.unity3d。

> [!NOTE]
> MaterialEditor标签引用的所有路径必须在 **_abdata/_** 之后开始。例如，Tutorial_shader.zipmod中的着色器资产包实际路径是_abdata/tutorial_shader/tutorial_shader/data_prefab_000.unity3d_，你可以在解压缩软件中打开.zipmod文件时看到该路径：
> 
> ![image](https://github.com/user-attachments/assets/5d48288c-2c75-4b4e-9682-ec4375a50f4a)
>
> 你填写到属性（如**AssetBundle**或**DefaultValueAssetBundle**）里的路径必须写作：**_tutorial_shader/tutorial_shader/data_prefab_000.unity3d_**，不带 _abdata/_ 。
>
>此外，路径中使用的斜杠符号“**/**”，不要写成反斜杠“**\\**”。

.shader文件中的属性与manifest.xml中的标签之间的关系可绘制如下：

![2025-01-18_201737](https://github.com/user-attachments/assets/a85e734c-6a04-442e-a31f-5f8099316701)

请注意，这对标签 _<AI_MaterialEditor></AI_MaterialEditor>_ 表示它们之间的标签仅适用于AI少女。为了将其使用于HS2，我们需要添加 _<HS2_MaterialEditor></HS2_MaterialEditor>_ 标签对，并将上面的相同标签复制到其中。因此，整个manifest.xml应当是：

```xml
<manifest schema-ver="1">
  <guid>Tutorial Shader</guid>
  <name>Tutorial Shader</name>
  <version>0.0.1</version>
  <author>Anonymous</author>
  <description></description>
  
  <AI_MaterialEditor>
    <Shader Name="Custom/tutorial" AssetBundle="tutorial_shader/tutorial_shader/data_prefab_000.unity3d" Asset="Cube" >
    <Property Name="Color" Type="Color" DefaultValue="1,1,1,1" />
    <Property Name="MainTex" Type="Texture" DefaultValue="mask" DefaultValueAssetBundle="tutorial_shader/tutorial_shader/tex.unity3d"/>
    <Property Name="Glossiness" Type="Float" Range="0,1" DefaultValue="0.5" />
    <Property Name="Metallic" Type="Float" Range="0,1" DefaultValue="0.5" />
    <Property Name="CustomProperty" Type="Color" DefaultValue="1,1,1,1" />
    </Shader>
  </AI_MaterialEditor>
  
  <HS2_MaterialEditor>
    <Shader Name="Custom/tutorial" AssetBundle="tutorial_shader/tutorial_shader/data_prefab_000.unity3d" Asset="Cube" >
    <Property Name="Color" Type="Color" DefaultValue="1,1,1,1" />
    <Property Name="MainTex" Type="Texture" DefaultValue="mask" DefaultValueAssetBundle="tutorial_shader/tutorial_shader/tex.unity3d"/>
    <Property Name="Glossiness" Type="Float" Range="0,1" DefaultValue="0.5" />
    <Property Name="Metallic" Type="Float" Range="0,1" DefaultValue="0.5" />
    <Property Name="CustomProperty" Type="Color" DefaultValue="1,1,1,1" />
    </Shader>
  </HS2_MaterialEditor>

</manifest>
```

最后，将manifest.xml保存到.zipmod文件中以完成整个过程。

文件_manifest.xml和_Tutorial_Shader.zipmod都可以在[Examples](https://github.com/Blatke/How-to-Build-A-Shader-Mod-for-HS2/blob/main/Examples/)文件夹里找到。

把这个mod安装到游戏里，并将着色器加载到Studio中的胶囊等物品中后，MaterialEditor的选项卡和贴图效果如下所示：

![2025-01-18_205313](https://github.com/user-attachments/assets/ca5d90e2-2dc1-4693-9de4-8d7e8c230d41)

## 补充材料

### 要添加标签的属性太多？

当.shader着色器文件中有数十个属性时，将每个属性都作为标签添加到manifest.xml中将会是一场噩梦。如果你使用Visual Code或其它支持正则表达式（[Regular Expression](https://en.wikipedia.org/wiki/Regular_expression)）的编码软件，你可以执行以下步骤，将着色器文件中的属性批量转换为MaterialEditor标签：

1.将属性复制到一个新文件。

![image](https://github.com/user-attachments/assets/9ced5834-fa15-4ff8-9e74-fcf24f9bf620)

2.按快捷键Ctrl+F调用选项卡进行查找和替换。启用正则表达式功能。

![image](https://github.com/user-attachments/assets/903691e9-e563-4d09-8f9e-11f8ce94197c)

3.若要转换**float**属性，请将以下正则表达式和标签分别复制粘贴到查找框和替换框中：

复制到查找框：

```RegEx
_([^ ]+)[ ]*\([^,]+,[ ]*Range\(([\-0-9,. ]+)\)[^0-9]+([0-9.\-]+)
```

复制到替换框：

```xml
<Property Name="$1" Type="Float" Range="$2" DefaultValue="$3" />
```

然后全部更换。

4.要转换**color**属性，请将以下正则表达式和标签分别复制粘贴到查找框和替换框中：

复制到查找框：

```RegEx
_([^ ]+)[ ]*\([^,]+,[ ]*Color\)[^\(]+\(([0-9., ]+)\)
```

复制到替换框：

```xml
<Property Name="$1" Type="Color" DefaultValue="$2" />
```

然后全部更换。

5.要转换**texture**属性，请将以下正则表达式和标签分别复制粘贴到查找框和替换框中：

复制到查找框：

```RegEx
_([^ ]+)[ ]*\([^,]+,[ ]*2D\)[^\n]+
```

复制到替换框：

```xml
<Property Name="$1" Type="Texture" DefaultValue="" DefaultValueAssetBundle=""/>
```

然后全部更换。

![image](https://github.com/user-attachments/assets/79dc4f41-1b8d-4352-a1f1-c49f815b526a)
