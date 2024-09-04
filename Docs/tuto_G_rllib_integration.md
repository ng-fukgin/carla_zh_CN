# RLlib集成
RLlib集成在[Ray/RLlib](https://github.com/ray-project/ray)库和CARLA之间提供了支持，使得CARLA环境可以轻松用于训练和推理目的。Ray是一个开源框架，为构建分布式应用程序提供了一个简单、通用的API。Ray内置了RLlib，一个可扩展的强化学习库，以及Tune，一个可扩展的超参数调整库。
RLlib集成允许用户创建并使用CARLA作为Ray的环境，并使用该环境进行训练和推理。该集成已准备好在本地和通过AWS在云端使用。
在本指南中，我们将概述在本地和AWS上运行RLlib集成所需的条件，集成仓库的结构，如何使用库的概览，以及如何使用CARLA作为环境设置Ray实验的示例。
- [RLlib集成](#rllib集成)
  - [开始之前](#开始之前)
  - [RLlib仓库结构](#rllib仓库结构)
  - [创建自己的实验](#创建自己的实验)
      - [1. 实验类](#1-实验类)
      - [2. 环境配置](#2-环境配置)
      - [3. 训练和推理脚本](#3-训练和推理脚本)
  - [\[rayCustomModel\]: https://docs.ray.io/en/master/rllib-models.html#custom-models-implementing-your-own-forward-logic](#raycustommodel-httpsdocsrayioenmasterrllib-modelshtmlcustom-models-implementing-your-own-forward-logic)
  - [DQN示例](#dqn示例)
  - [在AWS上运行](#在aws上运行)
      - [配置AWS](#配置aws)
      - [创建训练AMI](#创建训练ami)
      - [配置集群](#配置集群)
      - [运行训练](#运行训练)
      - [在AWS上运行DQN示例](#在aws上运行dqn示例)
---
## 开始之前
- 从[GitHub](https://github.com/carla-simulator/rllib-integration/tree/main)下载RLlib集成，或直接克隆仓库：
```sh
    git clone https://github.com/carla-simulator/rllib-integration.git
```
- 根据您是在本地还是AWS上运行，需求会有所不同：
>###### 在本地运行的需求
>>- [安装CARLA的包版本](https://github.com/carla-simulator/carla/releases)并导入[附加资产](https://carla.readthedocs.io/en/latest/start_quickstart/#import-additional-assets)。__推荐的版本是CARLA 0.9.11__，因为集成是针对这个版本设计和测试的。其他版本可能兼容，但未经完全测试，因此请自行决定是否使用这些版本。
>>- 进入RLlib集成仓库的根目录并安装Python需求：
                pip3 install -r requirements.txt
>>- 通过运行以下命令设置环境变量以定位CARLA包，或将其添加到`.bashrc`文件中：
                export CARLA_ROOT=path/to/carla
>###### 在AWS云端运行的需求
>>- 在AWS上运行的需求由RLlib集成仓库中的安装脚本自动处理。有关详细信息，请参阅["在AWS上运行"](#在AWS上运行)部分。
---
## RLlib仓库结构
该仓库分为三个目录：
- `rllib_integration` 包含与CARLA相关的所有基础设施，以及如何设置CARLA服务器、客户端和演员。这为所有训练和测试实验提供了基本结构。
- `aws` 包含在AWS实例上运行所需的文件。`aws_helper.py` 提供了简化EC2实例管理的多个功能，包括创建实例以及发送和接收数据。
- `dqn_example` 以及根目录中的 `dqn_*` 文件提供了一个易于理解的示例，说明如何使用CARLA作为环境设置Ray实验。
---
## 创建自己的实验
本节概述如何创建自己的实验。有关更具体的示例，请参阅下一节["DQN示例"](#dqn示例)。
您至少需要创建四个文件：
- 实验类
- 环境配置
- 训练和推理脚本
#### 1. 实验类
要使用CARLA环境，您需要定义一个训练实验。Ray要求环境返回一系列特定信息。有关CARLA环境的详细信息，请参阅[`rllib-integration/rllib_integration/carla_env.py`][carlaEnv]。
Ray所需的信息取决于您的特定实验，因此所有实验都应继承自[`BaseExperiment`][baseExperiment]。此类包含您自己的实验需要重写的所有功能。这些功能都与训练的动作、观察和奖励相关。
[carlaEnv]: https://github.com/carla-simulator/rllib-integration/blob/main/rllib_integration/carla_env.py
[baseExperiment]: https://github.com/carla-simulator/rllib-integration/blob/main/rllib_integration/base_experiment.py#L41
#### 2. 环境配置
应通过 `.yaml` 文件配置实验。通过配置文件传递的任何设置都将覆盖默认设置。下面将解释不同默认设置的位置。
配置文件有三个主要用途：
1. 设置大多数CARLA服务器和客户端设置，例如超时或地图质量。默认值[在此处查看][defaultCarlaSettings]。
2. 设置特定于您实验的变量，以及指定城镇条件以及 ego 车辆及其传感器的生成。默认设置在[此处找到][defaultExperimentSettings]，并提供了如何设置传感器的示例。
3. 配置特定于[Ray的训练][raySettings]的设置。这些设置与使用的特定训练器相关。如果您使用内置模型，可以在此处应用其设置。
[defaultCarlaSettings]: https://github.com/carla-simulator/rllib-integration/blob/main/rllib_integration/carla_core.py#L23
[defaultExperimentSettings]: https://github.com/carla-simulator/rllib-integration/blob/main/rllib_integration/base_experiment.py#L12
[raySettings]: https://github.com/ray-project/ray/blob/master/rllib/agents/trainer.py
#### 3. 训练和推理脚本
最后一步是创建您自己的训练和推理脚本。这部分完全取决于您和Ray API。如果您想创建自己的特定模型，请查看[Ray的自定义模型文档][rayCustomModel]。
[rayCustomModel]: https://docs.ray.io/en/master/rllib-models.html#custom-models-implementing-your-own-forward-logic
---
## DQN示例
本节在上一步的基础上，展示如何使用RLlib集成处理[BirdView伪传感器][birdview]和Ray的[DQNTrainer][dqntrainer]。
[birdview]: https://github.com/carla-simulator/rllib-integration/blob/main/rllib_integration/sensors/bird_view_manager.py
[dqntrainer]: https://github.com/ray-project/ray/blob/master/rllib/agents/dqn/dqn.py#L285
DQN示例的结构如下：
- __实验类__: [`DQNExperiment`][dqnExperiment]，它重写了`BaseExperiment`类的方法。
- __配置文件__: [`dqn_example/dqn_config.yaml`][dqnConfig]
- __训练文件__: [`dqn_train.py`][dqnTrain]
- __推理文件__:
    - __使用Ray__: [`dqn_inference_ray.py`][dqnInferenceRay] 
    - __不使用Ray__: [`dqn_inference.py`][dqnInference]
[dqnExperiment]: https://github.com/carla-simulator/rllib-integration/blob/main/dqn_example/dqn_experiment.py#L19
[dqnConfig]: https://github.com/carla-simulator/rllib-integration/blob/main/dqn_example/dqn_config.yaml
[dqnTrain]: https://github.com/carla-simulator/rllib-integration/blob/main/dqn_train.py
[dqnInferenceRay]: https://github.com/carla-simulator/rllib-integration/blob/main/dqn_inference_ray.py
[dqnInference]: https://github.com/carla-simulator/rllib-integration/blob/main/dqn_inference.py
要在本地运行示例：
1. 安装pytorch：
        pip3 install -r dqn_example/dqn_requirements.txt
2. 运行训练文件：
        python3 dqn_train.py dqn_example/dqn_config.yaml --name dqn        
!!! Note
    默认配置使用1个GPU和12个CPU，如果您的本地机器没有这个容量，请降低[配置文件][dqnConfig]中的数字。 
    
    如果您遇到内存不足的问题，可以考虑减少`buffer_size`参数。 
---
## 在AWS上运行
本节介绍如何使用RLlib集成在AWS EC2实例上自动运行训练和推理。为了处理实例的扩展，我们使用[Ray自动扩展器API][rayAutoscaler]。
[rayAutoscaler]: https://docs.ray.io/en/latest/cluster/index.html
#### 配置AWS
您需要正确配置您的boto3环境。更多信息请[在此处查看][awsBoto3]。
[awsBoto3]: https://boto3.amazonaws.com/v1/documentation/api/latest/guide/configuration.html
#### 创建训练AMI
使用提供的[`aws_helper.py`][awsHelper]脚本通过运行以下命令自动创建训练所需的镜像，传入基础镜像的名称以及位于[`rllib-integration/aws/install`][installsh]的安装脚本`install.sh`：
        python3 aws_helper.py create-image --name <AMI-name> --installation-scripts <installation-scripts> --instance-type <instance-type> --volume-size <volume-size>
[awsHelper]: https://github.com/carla-simulator/rllib-integration/blob/main/aws/aws_helper.py
[installsh]: https://github.com/carla-simulator/rllib-integration/blob/main/aws/install/install.sh
#### 配置集群
镜像创建后，将有一个输出包含镜像信息。要使用Ray自动扩展器，请更新[自动扩展器配置文件][autoscalerSettings]
中的 `<ImageId>` 和 `<SecurityGroupIds>` 设置，使用输出的信息。
[autoscalerSettings]: https://docs.ray.io/en/latest/cluster/config.html
#### 运行训练
镜像创建后，您可以使用Ray的API在集群上运行训练：
1. 初始化集群：
        ray up <autoscaler_configuration_file>
2. （可选）如果集群初始化后本地代码有所修改，运行以下命令以更新代码：
        ray rsync-up <autoscaler_configuration_file> <path_to_local_folder> <path_to_remote_folder>
3. 运行训练：
        ray submit <autoscaler_configuration_file> <training_file>
4. （可选）监控集群状态：
        ray attach <autoscaler_configuration_file>
        watch -n 1 ray status
5. 关闭集群：
        ray down <autoscaler_configuration_file>
#### 在AWS上运行DQN示例
要在AWS上运行DQN示例：
1. 通过传递 [`dqn_example/dqn_autoscaler.yaml`][dqnAutoscaler] 配置文件到以下命令来创建镜像：
        python3 aws_helper.py create-image --name <AMI-name> --installation-scripts install/install.sh --instance-type <instance-type> --volume-size <volume-size>
[dqnAutoscaler]: https://github.com/carla-simulator/rllib-integration/blob/main/dqn_example/dqn_autoscaler.yaml
2. 使用上一步的输出信息，更新 [`dqn_autoscaler.yaml`][dqnAutoscaler] 中的 `<ImageId>` 和 `<SecurityGroupIds>` 设置。
3. 初始化集群：
        ray up dqn_example/dqn_autoscaler.yaml
4. （可选）使用本地更改更新远程文件：
        ray rsync-up dqn_example/dqn_autoscaler.yaml dqn_example .
        ray rsync-up dqn_example/dqn_autoscaler.yaml rllib_integration .
5. 运行训练：
        ray submit dqn_example/dqn_autoscaler.yaml dqn_train.py -- dqn_example/dqn_config.yaml --auto
6. （可选）监控集群状态：
        ray attach dqn_example/dqn_autoscaler.yaml
        watch -n 1 ray status
7. 关闭集群：
        ray down dqn_example/dqn_autoscaler.yaml
---
本指南概述了如何在AWS和本地机器上安装和运行RLlib集成。如果您在按照指南操作时遇到任何问题或疑问，请随时在[论坛](https://github.com/carla-simulator/carla/discussions/)发帖或在[GitHub](https://github.com/carla-simulator/rllib-integration)上提出问题。

