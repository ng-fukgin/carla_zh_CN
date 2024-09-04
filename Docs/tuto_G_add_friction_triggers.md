# 如何添加摩擦触发器
摩擦触发器是一种可以在运行时添加的盒形触发器，当车辆进入这些触发器时，允许用户定义车辆轮子的不同摩擦力。例如，这可以用来在地图的特定区域动态地创建滑滑的表面。
使用PythonAPI在运行时生成摩擦触发器，用户首先需要获取`static.trigger.friction`蓝图定义，然后设置以下必要的属性到该蓝图定义中：
- *friction*：当车辆进入触发器时，触发器盒的摩擦力。
- *extent_x*：触发器盒在X坐标上的边界框扩展，以厘米为单位。
- *extent_y*：触发器盒在Y坐标上的边界框扩展，以厘米为单位。
- *extent_z*：触发器盒在Z坐标上的边界框扩展，以厘米为单位。
完成这些设置后，定义一个转换以指定摩擦触发器的地点和旋转，并生成它。
##### 示例
```py
import carla
def main():
    # 连接到客户端
    client = carla.Client('127.0.0.1', 2000)
    client.set_timeout(2.0)
    # 获取世界和演员
    world = client.get_world()
    actors = world.get_actors()
    # 查找摩擦触发器蓝图
    friction_bp = world.get_blueprint_library().find('static.trigger.friction')
    extent = carla.Location(700.0, 700.0, 700.0)
    friction_bp.set_attribute('friction', str(0.0))
    friction_bp.set_attribute('extent_x', str(extent.x))
    friction_bp.set_attribute('extent_y', str(extent.y))
    friction_bp.set_attribute('extent_z', str(extent.z))
    # 生成摩擦触发器
    transform = carla.Transform()
    transform.location = carla.Location(100.0, 0.0, 0.0)
    world.spawn_actor(friction_bp, transform)
    # 可选的用于可视化触发器
    world.debug.draw_box(box=carla.BoundingBox(transform.location, extent * 1e-2), rotation=transform.rotation, life_time=100, thickness=0.5, color=carla.Color(r=255,g=0,b=0))
if __name__ == '__main__':
    main()
```

