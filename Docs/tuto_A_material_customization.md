# 材质自定义
CARLA团队为每个资产都准备了某些默认设置。然而，对于从源代码构建的用户来说，他们可以根据自己的需求对这些设置进行修改。
*   汽车材质
*   自定义汽车材质
	*   外部属性
*   建筑材质
*   自定义建筑材质
!!! Important
    本教程仅适用于从源代码构建且可以访问Unreal编辑器的用户。
---
## 汽车材质
在CARLA中，有一套用于车辆不同部分的模板的母材质。为每个车辆模型创建这些材质的一个实例，然后更改以达到所需的结果。母材质可以在`Content/Carla/Static/GenericMaterials/Vehicles`中找到，如下所示。
![materials_master](img/material_customization/Materials_Master.jpg)
<div style="text-align: right"><i>应用于汽车的母材质。</i></div>
*   __M_CarExterior_Master__ — 应用于汽车车身的材质。
*   __M_CarInterior_Master__ — 应用于汽车内部的材质。
*   __M_CarLightsGlass_Master__ — 应用于覆盖汽车灯的玻璃材质。
*   __M_CarWindows_Master__ — 应用于汽车的窗户材质。
*   __M_CarLicensePlate_Master__ — 应用于车牌的材质。
*   __M_CarVehicleLights_Master__ — 应用于汽车的灯光的发射材质。
*   __M_CarVehicleLigthsSirens_Master__ — 应用于警报灯（如果适用）的材质。
---
## 自定义汽车材质
创建母材质的实例并将其存储在新模型的相应文件夹中。以下是新模型材质实例的创建示例，位于蓝图库中的警车 *vehicle.dodge_charger.police*。
![materials_instances](img/material_customization/Materials_Instances.jpg)
<div style="text-align: right"><i>警车蓝图中的材质实例。</i></div>
关于材质及其如何工作的通用文档可以在[UE文档](https://docs.unrealengine.com/en-US/Engine/Rendering/Materials/index.html)中找到。所有材质都可以进行大量修改，但只有外部材质具有值得关注的属性。其他材质有一些可以更改的属性，例如玻璃材质的透明度和颜色，但除非有特定目的，否则不建议这样做。
### 外部属性
外部材质应用于汽车车身，这是可以自定义最多的材质。
*   基础颜色 — 车身的基本颜色。
*   色调 — 色调颜色，其可见性取决于可视化的角度。
![materials_Tint](img/material_customization/Materials_Tint.jpg)
<div style="text-align: right"><i>带有粉红色调的红色汽车。左侧未启用色调，右侧启用。</i></div>
*   灰尘 — 应用于汽车的灰尘纹理。灰尘旨在堆积在几何体的顶部，底部部分几乎看不见。如果几何体旋转，灰尘将出现在车辆顶部。
	*   `Amount` — 纹理的不透明度。
	*   `Color` — 灰尘纹理的基础颜色。
	*   `Tiling` — 灰尘纹理图案的大小和重复。
	*   `Thickness` — 灰尘的密度。
	*   `Roughness` — 由于灰尘而减少汽车金属反射。
![materials_Dust](img/material_customization/Materials_Dust.jpg)
<div style="text-align: right"><i>汽车材质中的灰尘属性。</i></div>
*   片状物 — 应用于汽车金属漆的闪亮片状物。
	*   `On/Off` — 启用或禁用该功能。
	*   `Scale` — 片状物的尺寸。
	*   `Brightness` — 闪亮的强度。
	*   `Color` — 颗粒的基础颜色。
![materials_Flakes](img/material_customization/Materials_Flakes.jpg)
<div style="text-align: right"><i>汽车材质中的片状物属性。</i></div>
以下是您提供的文本翻译：

---

*   **增益** — 对车辆的基础漆面添加噪点。
    *   **开/关** — 启用或禁用此功能。
    *   **比例** — 增益的大小。
    *   **颜色** — 增益的基础颜色。

![materials_Gain](img/material_customization/Materials_Gain.jpg)
<div style="text-align: right"><i>车辆材质中的增益属性。</i></div>

*   **泥土** — 应用于车身的泥土纹理。泥土从车底向上覆盖车身。
    *   `高度` — 车身上泥土出现的部分。
    *   `泥土颜色` — 泥土纹理的基础颜色。
    *   `泥土平铺` — 泥土纹理图案的大小和重复率。
    *   `泥土厚度` — 泥土的密度。

![materials_mude](img/material_customization/Materials_Mud.jpg)
<div style="text-align: right"><i>车辆材质中的泥土属性。</i></div>

*   **噪点** — 应用于材质法线的噪点效果，创建橘皮效果。
    *   `开/关` — 启用或禁用此功能。
    *   `比例` — 由法线贴图变化所产生的凸起的大小。

![materials_noise](img/material_customization/Materials_Noise_High.jpg)
<div style="text-align: right"><i>车辆材质中的噪点属性。</i></div>

*   **表面** — 应用于车辆漆面的光泽和透明涂层。这是[汽车漆](https://en.wikipedia.org/wiki/Automotive_paint)的最后一步。
    *   `透明涂层` — 涂层的不透明度。
    *   `透明涂层亮度` — 材质的光泽度。
    *   `透明涂层金属感` — 材质的反射效果。

![materials_Surface](img/material_customization/Materials_Surface.jpg)
<div style="text-align: right"><i>应用于材质的表面涂层的可视化效果。</i></div>

---

## 构建材质

建筑物材质由四种基本纹理组成，这些纹理结合起来决定材质的基本属性。

*   **漫射** — 包含材质的基础色彩。
    *   `RGB` — 包含基础颜色的通道。
    *   `Alpha` — 此通道定义了一个允许修改白色区域颜色的遮罩。此功能有助于从相同材质中创建一些变化。

![building_diffuse_alpha](img/building_diffuse_alpha.png)

*   **ORME** — 使用特定通道映射材质的不同属性。
    *   `环境光遮蔽` — 包含在 `R` 通道中。
    *   `粗糙度` — 包含在 `G` 通道中。
    *   `金属感` — 包含在 `B` 通道中。
    *   `自发光遮罩` — 包含在 `Alpha` 通道中。此遮罩允许改变白色部分的自发光颜色和强度。

*   **法线** — 包含材质的法线贴图。
    *   `RGB` — 法线贴图的信息。

*   **自发光** — 如果适用，此纹理用于设置纹理的自发光基础色。
    *   `RGB` — 纹理中自发光元素的颜色信息。

![emissive](img/EmissiveIntensity.gif)

---

## 自定义建筑物材质

与车辆材质类似，建筑物材质也可以根据需要进行大幅度更改，但仅推荐具有Unreal Engine使用经验的用户进行此操作。然而，对于建筑物使用的两个主要着色器，仍然提供了一些可用的自定义选项。

![building_material](img/building_material.png)

*   **玻璃着色器** — `M_GlassMaster`。
    *   `Opacity` — 允许在 __Diffuse__ `Alpha` 纹理的白色区域上进行颜色更改。
    *   `Color` — 基于 __Diffuse__ `Alpha` 纹理的白色区域应用的色调。

*   **建筑着色器** — `M_MaterialMaster`
    *   `Change Color` — 允许在 __Diffuse__ `Alpha` 纹理的白色区域上进行颜色更改。
    *   `Color` — 基于__Emissive__ `Alpha` 纹理的白色区域应用的色调。
    *   `Emissive Texture` — 启用 __ORME__  纹理的使用。
    *   `EmissiveColor` — 基于__ORME__  `Emissive mask` 纹理的白色区域应用的色调。
    *   `Emissive atenuance` — 将 __BP_Lights__ 中指定的强度除以此因子以获得适当的自发光值。
    *   `RoughnessCorrection` — 改变粗糙度贴图的强度。
    *   `MetallicCorrection` — 改变金属感贴图的强度。
    *   `NormalFlatness` — 改变法线贴图的强度。

---

这就是用户自定义车辆和建筑物材质的最显著方法。

如果在操作中有任何疑问，欢迎到论坛讨论。

<div class="build-buttons">
<p>
<a href="https://github.com/carla-simulator/carla/discussions/" target="_blank" class="btn btn-neutral" title="访问CARLA论坛">
CARLA论坛</a>
</p>
</div>