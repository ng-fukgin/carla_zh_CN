# CarSim集成
CARLA与CarSim的集成允许将CARLA中的车辆控制转发到CarSim。CarSim将完成车辆所需的所有物理计算，并将新状态返回给CARLA。
本页向您展示了如何生成`.sim`文件，解释了CARLA和CarSim之间车辆尺寸的关系，以及如何使用CarSim集成在CARLA上运行模拟。
*   [__开始之前__](#before-you-begin)  
*   [__设置CarSim__](#set-up-carsim)  
    *   [__生成.sim文件__](#generate-the-sim-file)  
        * [__在Windows上__](#on-windows)
        * [__在Ubuntu上__](#on-ubuntu)
    *   [__车辆尺寸__](#vehicle-sizes)  
*   [__运行模拟__](#run-the-simulation)  
---
## 开始之前
1. 您需要获得CarSim的许可证，并确保软件运行正常。如果您目前还没有CarSim的许可证，可以通过[这里](https://www.carsim.com/forms/additional_information.php)与团队联系以获取相关信息。
2. 为了与Unreal Engine通信，您需要为Unreal Engine 4.24安装VehicleSim Dynamics插件（版本2020.0）。关于查找特定版本的插件，请查看这个[链接](https://www.carsim.com/products/supporting/unreal/index.php)。插件的安装将取决于您的操作系统：
    __对于Windows__：
    在[这里](https://www.unrealengine.com/marketplace/en-US/product/carsim-vehicle-dynamics)获取插件。
    __对于Ubuntu__：
    1. 在[这里](https://www.carsim.com/users/unreal_plugin/unreal_plugin_2020_0.php)下载插件。
    2. 使用[这里](https://carla-releases.s3.eu-west-3.amazonaws.com/Backup/CarSim.Build.cs)找到的文件替换`CarSim.Build.cs`文件，以便在Ubuntu上使用正确的求解器。
3. 如果您使用的是CARLA的打包版本，则可以跳过此步骤。打包版本已经使用此标志进行了编译，但是如果您从源代码构建CARLA，则需要使用`--carsim`标志编译服务器。
    如果您从源代码构建CARLA，在根文件夹中运行以下命令以使用`--carsim`标志编译服务器：
```sh
    make launch ARGS="--carsim"
```
## 设置CarSim
以下部分详细介绍了生成运行模拟所需的`.sim`文件的方法。此外，还详细说明了CARLA和CarSim之间车辆尺寸关系的重要信息。
#### 生成.sim文件
`.sim`文件描述了在CARLA和CarSim中要运行的模拟。此文件是运行插件所需的。目前无法在Ubuntu上生成此文件，但是下面我们将介绍如何使用之前生成的文件在Ubuntu上运行模拟。
##### 在Windows上
在CarSim上配置所有参数后，使用GUI生成`.sim`文件，如下所示：
![生成.sim文件](img/carsim_generate.jpg)
生成的`.sim`文件应如下所示：
```
SIMFILE
SET_MACRO $(ROOT_FILE_NAME)$ Run_dd7a828d-4b14-4c77-9d09-1974401d6b25
SET_MACRO $(OUTPUT_PATH)$ D:\carsim\Data\Results
SET_MACRO $(WORK_DIR)$ D:\carsim\Data\
SET_MACRO $(OUTPUT_FILE_PREFIX)$ $(WORK_DIR)$Results\Run_dd7a828d-4b14-4c77-9d09-1974401d6b25\LastRun
FILEBASE $(OUTPUT_FILE_PREFIX)$
INPUT $(WORK_DIR)$Results\$(ROOT_FILE_NAME)$\Run_all.par
INPUTARCHIVE $(OUTPUT_FILE_PREFIX)$_all.par
ECHO $(OUTPUT_FILE_PREFIX)$_echo.par
FINAL $(OUTPUT_FILE_PREFIX)$_end.par
LOGFILE $(OUTPUT_FILE_PREFIX)$_log.txt
ERDFILE $(OUTPUT_FILE_PREFIX)$.vs
PROGDIR D:\carsim\
DATADIR D:\carsim\Data\
GUI_REFRESH_V CarSim_RefreshEvent_7760
RESOURCEDIR D:\carsim\\Resources\
PRODUCT_ID CarSim
PRODUCT_VER 2020.0
ANIFILE D:\carsim\Data\runs\animator.par
VEHICLE_CODE i_i
EXT_MODEL_STEP 0.00050000
PORTS_IMP 0
PORTS_EXP 0
DLLFILE D:\carsim\Programs\solvers\carsim_64.dll
END
```
##### 在Ubuntu上
在Ubuntu上无法通过GUI创建`.sim`文件。为了继续操作，您需要执行以下步骤：
1. 在Windows中生成`.sim`文件，或使用下面的文件模板。
2. 修改`.sim`文件，使`INPUT`、`INPUTARCHIVE`、`LOGFILE`等变量指向您Ubuntu系统中的相应文件。
3. 替换`DLLFILE`行，使其指向CarSim求解器，在默认安装中，它将是`SOFILE /opt/carsim_2020.0/lib64/libcarsim.so.2020.0`。
修改后的文件应如下所示：
```
SIMFILE
FILEBASE /path/to/LastRun
INPUT /path/to/Run_all.par
INPUTARCHIVE /path/to/LastRun_all.par
ECHO /path/to/LastRun_echo.par
FINAL /path/to/LastRun_end.par
LOGFILE /path/to/LastRun_log.txt
ERDFILE /path/to/LastRun.vs
PROGDIR /opt/carsim_2020.0/lib64/
DATADIR .
PRODUCT_ID CarSim
PRODUCT_VER 2020.0
VEHICLE_CODE i_i
SOFILE /opt/carsim_2020.0/lib64/libcarsim.so.2020.0
END
```
#### 车辆尺寸
尽管CarSim允许您指定模拟中要使用的车辆尺寸，但目前CarSim车辆与CARLA车辆之间没有相关性。这意味着两个程序中的车辆将具有不同的尺寸。CARLA车辆的作用只是在模拟期间作为占位符。
![车辆尺寸](img/carsim_vehicle_sizes.jpg)
!!! Note
    CARLA和CarSim之间的车辆尺寸没有相关性。CARLA车辆只是模拟中的占位符。
## 运行模拟
运行模拟时，只需要在生成车辆时启用CarSim。这可以通过将`.sim`文件的路径传递给Python API的以下[方法](https://carla.readthedocs.io/en/latest/python_api/#carla.Vehicle.enable_carsim)来实现：
```sh
vehicle.enable_carsim(<path_to_ue4simfile.sim>)
```
发送到车辆的所有输入控制都将转发到CarSim。CarSim将更新物理状态，并将车辆的状态（变换）发送回CARLA车辆。
模拟完成后，您可以在CarSim中像往常一样分析所有数据。
![CarSim分析](img/carsim_analysis.jpg)

