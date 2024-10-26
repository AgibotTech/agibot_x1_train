[English](README.md) | 中文 | [日本語](README.ja.md)

## 简介
[智元灵犀X1](https://www.zhiyuan-robot.com/qzproduct/169.html) 是由智元研发并开源的模块化、高自由度人形机器人，X1的软件系统基于智元开源组件 `AimRT` 作为中间件实现，并且采用强化学习方法进行运动控制。

本工程为智元灵犀X1所使用的强化学习训练代码，可配合智元灵犀X1配套的[推理软件](https://aimrt.org/)进行真机和仿真的行走调试，或导入其他机器人模型进行训练。
![](doc/id.jpg)

## 代码运行

### 安装依赖
1. 创建一个新的python3.8虚拟环境:
   - `conda create -n myenv python=3.8`.
2. 安装 pytorch 1.13 和 cuda-11.7:
   - `conda install pytorch==1.13.1 torchvision==0.14.1 torchaudio==0.13.1 pytorch-cuda=11.7 -c pytorch -c nvidia`
3. 安装 numpy-1.23:
   - `conda install numpy=1.23`.
4. 安装 Isaac Gym:
   - 下载并安装 Isaac Gym Preview 4  https://developer.nvidia.com/isaac-gym.
   - `cd isaacgym/python && pip install -e .`
   - Run an example with `cd examples && python 1080_balls_of_solitude.py`.
   - Consult `isaacgym/docs/index.html` for troubleshooting.
6. 安装训练代码依赖：
   - Clone this repository.
   - `pip install -e .`

### 使用
#### Train:
```python scripts/train.py --task=x1_dh_stand --run_name=<run_name> --headless```
- 训练好的模型会存`/log/<experiment_name>/exported_data/<date_time><run_name>/model_<iteration>.pt` 其中 `<experiment_name>` 在config文件中定义.
![](doc/train.gif)

#### Play:
```python /scripts/play.py --task=x1_dh_stand --load_run=<date_time><run_name>```
![](doc/play.gif)

#### 生成jit模型:
``` python scripts/export_policy_dh.py --task=x1_dh_stand --load_run=<date_time><run_name>  ```
- jit模型会存在 ``` log/exported_policies/<date_time>```

#### 生成onnx模型:
``` python scripts/export_onnx_dh.py --task=x1_dh_stand --load_run=<date_time>  ```
- onnx模型会存在 ```log/exported_policies/<date_time>```

#### 参数说明：
- task: Task name
- resume: Resume training from a checkpoint
- experiment_name:  Name of the experiment to run or load.
- run_name: Name of the run.
- load_run: Name of the run to load when resume=True. If -1: will load the last run.
- checkpoint: Saved model checkpoint number. If -1: will load the last checkpoint.
- num_envs: Number of environments to create.
- seed: Random seed.
- max_iterations: Maximum number of training iterations.

### 添加新环境
1.在 `envs/`目录下创建一个新文件夹，在新文件夹下创建一个配置文件`<your_env>_config.py`和环境文件`<your_env>_env.py`，这两个文件要分别继承`LeggedRobotCfg`和`LeggedRobot`

2.将新机器的urdf, mesh, mjcf放到 `resources/`文件夹下
- 在`<your_env>_config.py`里配置新机器的urdf path，PD gain，body name, default_joint_angles, experiment_name等

3.在`humanoid/envs/__init__.py`里注册你的新机器

### sim2sim
使用mujoco来进行sim2sim验证：
  ```
  python scripts/sim2sim.py --task=x1_dh_stand --load_model /path/to/exported_policies/
  ```
![](doc/mujoco.gif)

### 手柄使用
我们使用Logitech f710手柄，在启动play.py和sim2sim.py时，按住4的同时转动摇杆可以控制机器人前后，左右和旋转。
![](doc/joy_map.jpg)
|         按键          |         命令         |
| -------------------- |:--------------------:|
|         4 + 1-       |         前进          |
|         4 + 1+       |         后退          |
|         4 + 0-       |        左平移         |
|         4 + 0+       |        右平移         |
|         4 + 3-       |       逆时针旋转       |
|         4 + 3+       |       顺时针旋转       |


## 目录结构
```
.
|— humanoid           # 主要代码目录
|  |—algo             # 算法目录
|  |—envs             # 环境目录
|  |—scripts          # 脚本目录
|  |—utilis           # 工具、功能目录
|— logs               # 模型目录
|— resources          # 资源库
|  |— robots          # 机器人urdf, mjcf, mesh
|— README.md          # 说明文档
```



> 参考项目:
>
> * [GitHub - leggedrobotics/legged_gym: Isaac Gym Environments for Legged Robots](https://github.com/leggedrobotics/legged_gym)
> * [GitHub - leggedrobotics/rsl_rl: Fast and simple implementation of RL algorithms, designed to run fully on GPU.](https://github.com/leggedrobotics/rsl_rl)
> * [GitHub - roboterax/humanoid-gym: Humanoid-Gym: Reinforcement Learning for Humanoid Robot with Zero-Shot Sim2Real Transfer https://arxiv.org/abs/2404.05695](https://github.com/roboterax/humanoid-gym)



