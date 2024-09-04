# 行人骨骼控制
在本教程中，我们将描述如何手动控制和动画化来自CARLA Python API的行人的骨骼。所有类和方法的参考可以在[Python API参考](python_api.md)中找到。
- [行人骨骼控制](#行人骨骼控制)
  - [行人骨骼结构](#行人骨骼结构)
  - [手动控制行人骨骼](#手动控制行人骨骼)
    - [连接到模拟器](#连接到模拟器)
    - [生成一个行人](#生成一个行人)
    - [控制行人骨骼](#控制行人骨骼)
---
## 行人骨骼结构
所有行人都具有相同的骨骼层次和骨骼名称。下面是骨骼层次的图像。
```
crl_root
└── crl_hips__C
    ├── crl_spine__C
    │   └── crl_spine01__C
    │       ├── ctrl_shoulder__L
    │       │   └── crl_arm__L
    │       │       └── crl_foreArm__L
    │       │           └── crl_hand__L
    │       │               ├── crl_handThumb__L
    │       │               │   └── crl_handThumb01__L
    │       │               │       └── crl_handThumb02__L
    │       │               │           └── crl_handThumbEnd__L
    │       │               ├── crl_handIndex__L
    │       │               │   └── crl_handIndex01__L
    │       │               │       └── crl_handIndex02__L
    │       │               │           └── crl_handIndexEnd__L
    │       │               ├── crl_handMiddle_L
    │       │               │   └── crl_handMiddle01__L
    │       │               │       └── crl_handMiddle02__L
    │       │               │           └── crl_handMiddleEnd__L
    │       │               ├── crl_handRing_L
    │       │               │   └── crl_handRing01__L
    │       │               │       └── crl_handRing02__L
    │       │               │           └── crl_handRingEnd__L
    │       │               └── crl_handPinky_L
    │       │                   └── crl_handPinky01__L
    │       │                       └── crl_handPinky02__L
    │       │                           └── crl_handPinkyEnd__L
    │       ├── crl_neck__C
    │       │   └── crl_Head__C
    │       │       ├── crl_eye__L
    │       │       └── crl_eye__R
    │       └── crl_shoulder__R
    │           └── crl_arm__R
    │               └── crl_foreArm__R
    │                   └── crl_hand__R
    │                       ├── crl_handThumb__R
    │                       │   └── crl_handThumb01__R
    │                       │       └── crl_handThumb02__R
    │                       │           └── crl_handThumbEnd__R
    │                       ├── crl_handIndex__R
    │                       │   └── crl_handIndex01__R
    │                       │       └── crl_handIndex02__R
    │                       │           └── crl_handIndexEnd__R
    │                       ├── crl_handMiddle_R
    │                       │   └── crl_handMiddle01__R
    │                       │       └── crl_handMiddle02__R
    │                       │           └── crl_handMiddleEnd__R
    │                       ├── crl_handRing_R
    │                       │   └── crl_handRing01__R
    │                       │       └── crl_handRing02__R
    │                       │           └── crl_handRingEnd__R
    │                       └── crl_handPinky_R
    │                           └── crl_handPinky01__R
    │                               └── crl_handPinky02__R
    │                                   └── crl_handPinkyEnd__R
    ├── crl_thigh__L
    │   └── crl_leg__L
    │       └── crl_foot__L
    │           └── crl_toe__L
    │               └── crl_toeEnd__L
    └── crl_thigh__R
        └── crl_leg__R
            └── crl_foot__R
                └── crl_toe__R
                    └── crl_toeEnd__R
```
---
## 手动控制行人骨骼
以下是详细的一步一步的例子，说明如何从CARLA Python API更改行人的骨骼变换。
### 连接到模拟器
导入本例中使用的必要库
```py
import carla
import random
```
初始化carla客户端
```py
client = carla.Client('127.0.0.1', 2000)
client.set_timeout(2.0)
```
### 生成一个行人
在地图的一个生成点随机生成一个行人
```py
world = client.get_world()
blueprint = random.choice(world.get_blueprint_library().filter('walker.*'))
spawn_points = world.get_map().get_spawn_points()
spawn_point = random.choice(spawn_points) if spawn_points else carla.Transform()
walker = world.try_spawn_actor(blueprint, spawn_point)
```
### 控制行人骨骼
可以通过将WalkerBoneControl类的实例传递给行人的apply_control函数来修改行人的骨骼。WalkerBoneControl类包含要修改的骨骼的变换。它的bone_transforms成员是一个元组列表，每个元组包含一对值，其中第一个值是骨骼名称，第二个值是骨骼变换。apply_control函数可以在每个tick上调用，以动画化行人的骨骼。每个变换的位置和旋转相对于其父级。因此，当父骨骼的变换被修改时，模型空间中子骨骼的变换也会相对地改变。
在下面的例子中，行人的手的旋转被设置为围绕前轴90度，位置被设置为中心。
```py
control = carla.WalkerBoneControl()
first_tuple = ('crl_hand__R', carla.Transform(rotation=carla.Rotation(roll=90)))
second_tuple = ('crl_hand__L', carla.Transform(rotation=carla.Rotation(roll=90)))
control.bone_transforms = [first_tuple, second_tuple]
walker.apply_control(control)
```
请注意，上面的代码示例中，`world.player` 应该是 `walker`，因为我们想要控制的是我们刚刚生成的行人，而不是世界中的玩家。所以，最后一行应该是 `walker.apply_control(control)` 而不是 `world.player.apply_control(control)`。


   
