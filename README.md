# RoboSynChallenge 2026 参赛提交说明

> 本文档用于 RoboSynChallenge 2026 参赛提交与复现。

## 1. 队伍信息

| 项目 | 内容 |
| --- | --- |
| 队伍名称 | **renji** |
| 队长 | **蔡俊仰** |
| 队员 | **刘子航、何婧晓** |
| 联系邮箱 | **3063859405@qq.com、1739796854@qq.com** |
| 比赛阶段 | 2026 初赛评测 |
| 文档更新时间 | 2026-10-11 |

## 2. 提交信息

| 项目 | 内容 |
| --- | --- |
| 比赛主页 | [RoboSynChallenge](https://robosyn-bench.net/) |
| 基础模型名称 | ACT（基于 LeRobot 实现） |
| 代码仓库 | **[Hrzl0829/RoboSynChallenge](https://github.com/Hrzl0829/RoboSynChallenge)** |
| 模型仓库 | [Hrzl1467/RobosynChallenge](https://huggingface.co/Hrzl1467/RobosynChallenge) |
| 是否参与排行榜 | **是** |

### 方法简介

本方案采用 ACT（Action Chunking with Transformers）策略，并使用 LeRobot 的 `ACTPolicy` 完成模型加载与推理。策略接收双臂关节及夹爪状态，以及顶部、左腕和右腕三路 RGB 图像，输出双臂关节和夹爪的绝对控制指令。十个比赛任务分别使用独立的模型检查点，统一存放在同一个 Hugging Face 仓库的不同任务目录中。

## 3. 策略配置

| 文件 | 作用 |
| --- | --- |
| [`policy/act/deploy_policy.py`](policy/act/deploy_policy.py) | 加载 ACT 检查点并执行推理 |
| [`policy/act/deploy_policy.yml`](policy/act/deploy_policy.yml) | 策略、设备、相机和执行步数配置 |
| [`policy/act/eval.sh`](policy/act/eval.sh) | 单任务评测入口 |
| [`policy/act/pyproject.toml`](policy/act/pyproject.toml) | Python 依赖和项目配置 |
| [`policy/act/uv.lock`](policy/act/uv.lock) | 锁定依赖版本，便于复现环境 |

## 4. 十个任务及模型地址

十个任务的模型放在同一个 Hugging Face 仓库中，每个任务使用独立目录。

| 序号 | 任务标识 | Hugging Face 模型地址 |
| ---: | --- | --- |
| 1 | `table_rearrangement` | https://huggingface.co/Hrzl1467/RobosynChallenge/tree/main/table_rearrangement |
| 2 | `click_bell` | https://huggingface.co/Hrzl1467/RobosynChallenge/tree/main/click_bell |
| 3 | `water_pouring` | https://huggingface.co/Hrzl1467/RobosynChallenge/tree/main/water_pouring |
| 4 | `handle_basket` | https://huggingface.co/Hrzl1467/RobosynChallenge/tree/main/handle_basket |
| 5 | `items_handover`      | https://huggingface.co/Hrzl1467/RobosynChallenge/tree/main/items_handover |
| 6 | `drawer_open_place` | https://huggingface.co/Hrzl1467/RobosynChallenge/tree/main/drawer_open_place |
| 7 | `mixer_operating` | https://huggingface.co/Hrzl1467/RobosynChallenge/tree/main/mixer_operating |
| 8 | `item_assembly` | https://huggingface.co/Hrzl1467/RobosynChallenge/tree/main/item_assembly |
| 9 | `manipulate_pipette` | https://huggingface.co/Hrzl1467/RobosynChallenge/tree/main/manipulate_pipette |
| 10 | `sample_loading`      | https://huggingface.co/Hrzl1467/RobosynChallenge/tree/main/sample_loading |

### 4.1 推荐的模型仓库结构

```text
Hrzl1467/RobosynChallenge
├── table_rearrangement/
│   ├── config.json
│   ├── model.safetensors
│   └── train_config.json
├── click_bell/
│   ├── config.json
│   ├── model.safetensors
│   └── train_config.json
├── water_pouring/
├── handle_basket/
├── items_handover/
├── drawer_open_place/
├── mixer_operating/
├── item_assembly/
├── manipulate_pipette/
└── sample_loading/
```

每个任务目录至少应包含：

- `config.json`：模型结构与输入输出配置；
- `model.safetensors`：模型权重；
- `train_config.json`：训练参数、数据来源和版本信息。

## 5. 训练数据说明

- 官方仿真数据（`official_simulated`）；
- 其他自行采集、生成或转换的数据。

| 任务 | 数据来源 |
| --- | --- |
| `table_rearrangement` | **RoboSynChallenge 官方仿真数据1000集** |
| `click_bell` | **RoboSynChallenge 流程自行生成的任务仿真数据1000集** |
| `water_pouring` | **RoboSynChallenge 流程自行生成的任务仿真数据1000集** |
| `handle_basket` | **RoboSynChallenge 流程自行生成的任务仿真数据1000集** |
| `items_handover` | **RoboSynChallenge 流程自行生成的任务仿真数据1000集** |
| `drawer_open_place` | **RoboSynChallenge 流程自行生成的任务仿真数据1000集** |
| `mixer_operating` | **RoboSynChallenge 流程自行生成的任务仿真数据1000集** |
| `item_assembly` | **RoboSynChallenge 官方仿真数据1000集** |
| `manipulate_pipette` | **RoboSynChallenge 流程自行生成的任务仿真数据1000集** |
| `sample_loading` | **RoboSynChallenge 流程自行生成的任务仿真数据1000集** |

数据生成、转换和格式说明可参考：

- [`launch/README.md`](launch/README.md)
- [`lerobot_dataset/README.md`](lerobot_dataset/README.md)
- [`docs/tutorials/collect_data.md`](docs/tutorials/collect_data.md)
- [`docs/tutorials/download_data.md`](docs/tutorials/download_data.md)

## 6. 代码仓库结构

```text
RoboSynChallenge_submission_20261011/
├── README.md
├── LICENSE
├── docs/
├── examples/
├── launch/
├── lerobot_dataset/
├── policy/
│   ├── README.md
│   └── act/
│       ├── deploy_policy.py
│       ├── deploy_policy.yml
│       ├── eval.sh
│       ├── pyproject.toml
│       └── uv.lock
├── pyproject.toml
└── uv.lock
```

## 7. 环境要求与安装

### 7.1 推荐环境

- Ubuntu 22.04；
- Python 3.11；
- 支持 CUDA 的 NVIDIA GPU；
- NVIDIA 驱动版本不低于 535；
- CUDA 12.8 运行环境；
- Docker 与 NVIDIA Container Toolkit；
- EmbodiChain `v0.2.4.post1`；
- Git、Git LFS、Hugging Face CLI；
- `uv`（推荐，用于严格复现锁定依赖）。

### 7.2 获取代码

```bash
git clone https://github.com/Hrzl0829/RoboSynChallenge
cd RoboSynChallenge_submission_20261011
```

### 7.3 安装 ACT 策略依赖

使用 `uv`：

```bash
cd policy/act
uv sync
```

或者使用现有 Conda 环境：

```bash
conda create -n robosyn python=3.11 -y
conda activate robosyn
pip install -e ./policy/act
```

如项目依赖本地 EmbodiChain，请先按照 [`docs/getting_started/installation.md`](docs/getting_started/installation.md) 完成其安装，并确认 `policy/act/pyproject.toml` 中的本地路径与实际环境一致。

## 8. 下载 Hugging Face 模型

### 8.1 登录

如果模型仓库为私有或受限仓库，先完成登录：

```bash
huggingface-cli login
```

### 8.2 下载全部任务模型

```bash
huggingface-cli download Hrzl1467/RobosynChallenge \
  --revision a498ac58d95192d848db28752df77eb6f7d37670 \
  --local-dir ./checkpoints/RobosynChallenge
```

### 8.3 只下载单个任务

以下示例仅下载 `click_bell`：

```bash
huggingface-cli download Hrzl1467/RobosynChallenge \
  --include "click_bell/*" \
  --local-dir ./checkpoints/RobosynChallenge
```

下载完成后，请确认目标目录内存在：

```text
config.json
model.safetensors
train_config.json
```

## 9. 运行评测

### 9.1 通用命令

```bash
bash policy/act/eval.sh \
  <task_name> \
  <random|clear> \
  <checkpoint_path> \
  [seed] \
  [gpu_id]
```

参数说明：

- `task_name`：比赛任务名称，例如 `click_bell`；
- `random|clear`：评测场景模式；
- `checkpoint_path`：包含 `config.json` 和 `model.safetensors` 的目录；
- `seed`：随机种子，可选；
- `gpu_id`：使用的 GPU 编号，可选。

### 9.2 `click_bell` 示例

```bash
bash policy/act/eval.sh \
  click_bell \
  random \
  ./checkpoints/RobosynChallenge/click_bell \
  0 \
  0
```

## 10. 硬件与复现记录

| 项目                  | 内容                                                         |
| --------------------- | ------------------------------------------------------------ |
| GPU 型号与数量        | **2 × NVIDIA GeForce RTX 4090，每张 24,564 MiB；单次评测通过 `--gpu_id` 使用其中一张** |
| CPU                   | **Intel Core i7-14700F，20核28线程**                         |
| 内存                  | **128 GB**                                                   |
| 操作系统              | **Ubuntu 22.04 LTS，内核 `6.8.0-138-generic`**               |
| NVIDIA 驱动版本       | **`580.173.02`**                                             |
| CUDA 版本             | **`CUDA 12.6`**                                              |
| Python 版本           | **`3.11.15`**                                                |
| PyTorch 版本          | **`2.7.1` + cu126**                                          |
| LeRobot 版本 / commit | **`0.3.3` / `b883328e6c95681ca90a18b102e4ae5e1f91e2bf`**     |
| EmbodiChain 版本      | **`v0.2.4`**                                                 |
| EmbodiChain commit    | **`9ebee30011f378f94a7cbe78b01d8c2eacba231a`**               |
