# 自定义地图：交通信号灯和标志

本指南将介绍如何向自定义地图添加交通信号灯和标志，配置每个信号灯和标志的影响区域，以及如何在交叉口将信号灯配置为一个组。这个选项仅适用于拥有Unreal Engine编辑器访问权限的用户。

- [交通信号灯](#traffic-lights)
- [交通标志](#traffic-signs)
- [下一步](#next-steps)

---

## 交通信号灯

要向您的新地图添加交通信号灯：

1. 从“内容浏览器”中导航到 `Content/Carla/Static/TrafficLight/StreetLights_01`。您将找到几个不同的交通信号灯蓝图可供选择。

2. 将交通信号灯拖动到场景中，并将其定位到所需位置。按下键盘上的空格键可在定位、旋转和缩放工具之间切换。

3. 通过选择“详情”面板中的“BoxTrigger”组件，并调整“Transform”部分中的值来调整每个交通信号灯的“触发体积”。这将确定交通信号灯的影响区域。

   ![ue_trafficlight](../img/ue_trafficlight.jpg)

4. 对于交叉口，将 `BP_TrafficLightGroup` 拖动到场景中。通过将所有交通信号灯添加到“详情”面板中的“Traffic Lights”数组中，将它们分配给交通信号灯组。

   ![ue_tl_group](../img/ue_tl_group.jpg)

5. 交通信号灯的定时配置仅通过Python API进行。有关更多信息，请参阅[此处的文档](core_actors.md#traffic-signs-and-traffic-lights)。

   ![ue_tlsigns_example](../img/ue_tlsigns_example.jpg)

> _示例：交通标志、交通信号灯和基于转向的停车。_

---

## 交通标志

要向您的新地图添加交通标志：

1. 从“内容浏览器”中导航到 `Content/Carla/Static/TrafficSign`。您将找到几个不同的交通标志蓝图可供选择。

2. 将交通标志拖动到场景中，并将其定位到所需位置。按下键盘上的空格键可在定位、旋转和缩放工具之间切换。

3. 通过选择“详情”面板中的“BoxTrigger”组件，并调整“Transform”部分中的值来调整每个交通标志的“触发体积”。这将确定交通标志的影响区域。并非所有交通标志都有触发体积。其中包括让行、停车和限速标志。

---

## 下一步

继续使用下面的工具和指南来自定义您的地图：

- [在地图中实现子级](tuto_M_custom_layers.md)
- [使用过程化建筑工具添加建筑物](tuto_M_custom_buildings.md)
- [使用道路绘制工具自定义道路](tuto_M_custom_road_painter.md)
- [自定义天气](tuto_M_custom_weather_landscape.md#weather-customization)
- [使用连续网格自定义地形](tuto_M_custom_weather_landscape.md#add-serial-meshes)

完成地图的自定义后，您可以[生成行人导航信息](tuto_M_generate_pedestrian_navigation.md)。

---

如果您对该过程有任何疑问，请在[论坛](https://github.com/carla-simulator/carla/discussions)中提问。
