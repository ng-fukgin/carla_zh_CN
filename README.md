CARLA Simulator
===============
这个是CARLA中文翻译版本，能力有限，所以翻译缓慢，欢迎大家加入我，跟我一起翻译。

为了方便其他人阅读，翻译过程中请大家尽量不要出现机器翻译.

由于carla文档版本繁多，因此我选择了目前最新版0.9.13版来翻译，其他版本还请查看原文

翻译文档发布在[https://carla-docs.readthedocs.io/zh_CN/latest](https://carla-docs.readthedocs.io/zh_CN/latest),切换语言也可以查看英文原文(0.9.13版本)

[![Build Status](https://travis-ci.org/carla-simulator/carla.svg?branch=master)](https://travis-ci.org/carla-simulator/carla)
[![Documentation](https://readthedocs.org/projects/carla/badge/?version=latest)](https://carla-docs.readthedocs.io/zh_CN/latest/)

[![carla.org](Docs/img/btn/web.png)](http://carla.org)
[![download](Docs/img/btn/download.png)](https://github.com/carla-simulator/carla/blob/master/Docs/download.md)
[![documentation](Docs/img/btn/docs.png)](https://carla-docs.readthedocs.io/zh_CN/latest/)
[![forum](Docs/img/btn/forum.png)](https://github.com/carla-simulator/carla/discussions)
[![discord](Docs/img/btn/chat.png)](https://discord.gg/8kqACuC)

感谢@lancelot-Yagami的提醒，我做了一个todo list 

如果想要加入我，请大家在下面认领任务，然后fork我的仓库，翻译完成后发起pull request，我会在粗校没问题后同意合并。 :tada:

- [x] Home: 'index.md'：@ng-fukgin
- [x] Getting started:
  - [x] 'Introduction': 'start_introduction.md'：@ng-fukgin
  - [x] 'Quick start package installation': 'start_quickstart.md'：@ng-fukgin
- [x] Building CARLA:
  - [x] 'Linux build': 'build_linux.md'：@ng-fukgin
  - [x] 'Windows build': 'build_windows.md'：@ng-fukgin
  - [x] 'Update CARLA': 'build_update.md'：@ng-fukgin
  - [x] 'Build system': 'build_system.md'：@ng-fukgin
  - [x] 'CARLA in Docker': 'build_docker.md'：@ng-fukgin
  - [x] 'F.A.Q.': 'build_faq.md'：@ng-fukgin
- [x] First steps:
  - [x] 'Core concepts': 'core_concepts.md'：@ng-fukgin
  - [x] '1st-   World and client': 'core_world.md'：@ng-fukgin
  - [x] '2nd-  Actors and blueprints': 'core_actors.md'：@ng-fukgin
  - [x] '3rd-  Maps and navigation': 'core_map.md'：@ng-fukgin
  - [x] '4th-  Sensors and data': 'core_sensors.md'：@ng-fukgin
- [ ] Advanced concepts:
  - [x] 'OpenDRIVE standalone mode': 'adv_opendrive.md'：@ng-fukgin
  - [ ] 'PTV-Vissim co-simulation': 'adv_ptv.md'：名字
  - [ ] 'Recorder': 'adv_recorder.md'：名字
  - [ ] 'Rendering options': 'adv_rendering_options.md'：名字
  - [ ] 'RSS': 'adv_rss.md'：名字
  - [ ] 'Synchrony and time-step': 'adv_synchrony_timestep.md'：名字
  - [ ] 'Benchmarking Performance': 'adv_benchmarking.md'：名字
  - [ ] 'CARLA Agents': 'adv_agents.md'：名字
- [ ] Traffic Simulation:
  - [ ] 'Traffic Simulation Overview': 'ts_traffic_simulation_overview.md'：名字
  - [ ] 'Traffic Manager': 'adv_traffic_manager.md'：名字
  - [ ] 'SUMO co-simulation': 'adv_sumo.md'：名字
  - [ ] 'Scenic': 'tuto_G_scenic.md'：名字
- [ ] References:
  - [ ] 'Python API reference': 'python_api.md'：名字
  - [ ] 'Blueprint Library': 'bp_library.md'：名字
  - [ ] 'C++ reference' : 'ref_cpp.md'：名字
  - [ ] 'Recorder binary file format': 'ref_recorder_binary_file_format.md'：名字
  - [ ] "Sensors reference": 'ref_sensors.md'：名字  
- [ ] Plugins:
  - [ ] 'carlaviz — web visualizer': 'plugins_carlaviz.md'：名字
- [ ] ROS bridge:
  - [ ] 'ROS bridge documentation': 'ros_documentation.md'：名字
- [ ] Custom Maps:
  - [x] 'Overview of custom maps in CARLA': 'tuto_M_custom_map_overview.md'：@lancelot-Yagami
  - [x] 'Create a map in RoadRunner': 'tuto_M_generate_map.md'：@lancelot-Yagami
  - [ ] 'Import map in CARLA package': 'tuto_M_add_map_package.md'：@lancelot-Yagami
  - [ ] 'Import map in CARLA source build': 'tuto_M_add_map_source.md'：@lancelot-Yagami
  - [ ] 'Alternative ways to import maps': 'tuto_M_add_map_alternative.md'：@lancelot-Yagami
  - [ ] 'Manually prepare map package': 'tuto_M_manual_map_package.md'：@lancelot-Yagami
  - [ ] 'Customizing maps: Layered maps': 'tuto_M_custom_layers.md'：@lancelot-Yagami
  - [ ] 'Customizing maps: Traffic lights and signs': 'tuto_M_custom_add_tl.md'：@lancelot-Yagami
  - [ ] 'Customizing maps: Road painter': 'tuto_M_custom_road_painter.md'：@lancelot-Yagami
  - [ ] 'Customizing maps: Procedural Buildings': 'tuto_M_custom_buildings.md'：@lancelot-Yagami
  - [ ] 'Customizing maps: Weather and landscape': 'tuto_M_custom_weather_landscape.md'：@lancelot-Yagami
  - [ ] 'Generate pedestrian navigation': 'tuto_M_generate_pedestrian_navigation.md'：@lancelot-Yagami
- [ ] Large Maps:
  - [ ] 'Large maps overview': 'large_map_overview.md'：名字
  - [ ] 'Create a large map in RoadRunner': 'large_map_roadrunner.md'：名字
  - [ ] 'Import/Package a large map': 'large_map_import.md'：名字
- [ ] Tutorials (general):
  - [ ] 'Add friction triggers': "tuto_G_add_friction_triggers.md"
  - [ ] 'Control vehicle physics': "tuto_G_control_vehicle_physics.md"
  - [ ] 'Control walker skeletons': "tuto_G_control_walker_skeletons.md"
  - [ ] 'Generate maps with OpenStreetMap': 'tuto_G_openstreetmap.md'：名字
  - [ ] 'Retrieve simulation data': "tuto_G_retrieve_data.md"
  - [ ] 'CarSim Integration': "tuto_G_carsim_integration.md"
  - [ ] 'RLlib Integration': "tuto_G_rllib_integration.md"
  - [ ] 'Chrono Integration': 'tuto_G_chrono.md'：名字
  - [ ] 'Build Unreal and CARLA in Docker': 'build_docker_unreal.md'：名字
- [ ] Tutorials (assets):
  - [ ] 'Add a new vehicle': 'tuto_A_add_vehicle.md'：名字
  - [ ] 'Add new props': 'tuto_A_add_props.md'：名字
  - [ ] 'Create standalone packages': 'tuto_A_create_standalone.md'：名字
  - [ ] "Material customization": 'tuto_A_material_customization.md'：名字
- [ ] Tutorials (developers):
  - [ ] 'How to upgrade content': 'tuto_D_contribute_assets.md'：名字
  - [ ] 'Create a sensor': 'tuto_D_create_sensor.md'：名字
  - [ ] 'Create semantic tags': 'tuto_D_create_semantic_tags.md'：名字
  - [ ] 'Customize vehicle suspension': 'tuto_D_customize_vehicle_suspension.md'：名字
  - [ ] 'Generate detailed colliders': 'tuto_D_generate_colliders.md'：名字
  - [ ] 'Make a release': 'tuto_D_make_release.md'：名字
- [ ] CARLA Ecosystem:
  - [ ] 'Ansys Real Time Radar Model': 'ecosys_ansys.md'：名字
- [x] Contributing:
  - [x] 'Contribution guidelines': 'cont_contribution_guidelines.md'：@ng-fukgin
  - [x] 'Code of conduct': 'cont_code_of_conduct.md'：@ng-fukgin
  - [x] 'Coding standard': 'cont_coding_standard.md'：@ng-fukgin
  - [x] 'Documentation standard': 'cont_doc_standard.md'：@ng-fukgin
