# 控制和监控车辆物理
车辆的物理属性及其车轮可以进行调节。这些更改仅在运行时应用，并且在执行结束时将值重置为默认。
这些属性通过一个[carla.VehiclePhysicsControl](python_api.md#carla.VehiclePhysicsControl)对象来控制，该对象还通过一个[carla.WheelPhysicsControl](python_api.md#carla.WheelPhysicsControl)对象来控制每个车轮的物理属性。
- [__车辆控制代码示例__](#vehicle-control-code-example)
- [__查看车辆遥测数据__](#viewing-vehicle-telemetry)
---
## 车辆控制代码示例
```py
import carla
import random
def main():
    # 连接到客户端
    client = carla.Client('127.0.0.1', 2000)
    client.set_timeout(2.0)
    # 获取世界和演员
    world = client.get_world()
    actors = world.get_actors()
    # 从世界中获取一个随机车辆（至少应该有一个）
    vehicle = random.choice([actor for actor in actors if 'vehicle' in actor.type_id])
    # 创建车轮物理控制
    front_left_wheel  = carla.WheelPhysicsControl(tire_friction=2.0, damping_rate=1.5, max_steer_angle=70.0, long_stiff_value=1000)
    front_right_wheel = carla.WheelPhysicsControl(tire_friction=2.0, damping_rate=1.5, max_steer_angle=70.0, long_stiff_value=1000)
    rear_left_wheel   = carla.WheelPhysicsControl(tire_friction=3.0, damping_rate=1.5, max_steer_angle=0.0,  long_stiff_value=1000)
    rear_right_wheel  = carla.WheelPhysicsControl(tire_friction=3.0, damping_rate=1.5, max_steer_angle=0.0,  long_stiff_value=1000)
    wheels = [front_left_wheel, front_right_wheel, rear_left_wheel, rear_right_wheel]
    # 更改车辆的车辆物理控制参数
    physics_control = vehicle.get_physics_control()
    physics_control.torque_curve = [carla.Vector2D(x=0, y=400), carla.Vector2D(x=1300, y=600)]
    physics_control.max_rpm = 10000
    physics_control.moi = 1.0
    physics_control.damping_rate_full_throttle = 0.0
    physics_control.use_gear_autobox = True
    physics_control.gear_switch_time = 0.5
    physics_control.clutch_strength = 10
    physics_control.mass = 10000
    physics_control.drag_coefficient = 0.25
    physics_control.steering_curve = [carla.Vector2D(x=0, y=1), carla.Vector2D(x=100, y=1), carla.Vector2D(x=300, y=1)]
    physics_control.use_sweep_wheel_collision = True
    physics_control.wheels = wheels
    # 为车辆应用车辆物理控制
    vehicle.apply_physics_control(physics_control)
    print(physics_control)
if __name__ == '__main__':
    main()
```
---
## 查看车辆遥测数据
可以通过调用[`Actor.enable_debug_telemetry`](python_api.md#carla.Actor.enable_debug_telemetry)方法来可视化车辆遥测数据。这将 在服务器窗口提供多个指标的图表视图，以及在模拟窗口中提供车辆参考点。
![车辆遥测](img/vehicle_telemetry.png)
您可以在`PythonAPI/examples`中的示例脚本`manual_control.py`尝试遥测可视化工具。按`T`激活遥测视图。
