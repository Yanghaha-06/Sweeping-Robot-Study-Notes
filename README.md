# Sweeping-Robot-Study-Notes
清洁机器人的学习笔记

# 入门的问题？

1.训练入口是什么?

这个项目不是传统的 train_test.py 直接启动，而是由腾讯开悟平台统一调度。

点击“开始训练”后，平台会自动启动：

环境（env）

aisrv

learner

agent

真正和自己代码关系最大的入口主要是：

agent.py

train_workflow.py

其中：

agent.py
负责智能体逻辑，比如：
观测处理、动作决策、模型推理等。

train_workflow.py
负责训练流程，会不断循环：
获取环境状态 → 调用 agent → 收集 reward → 更新 PPO。

本质上可以理解为：

开悟平台负责启动，
agent.py 负责决策，
workflow 负责训练循环。


2.环境怎么启动？

环境不是自己手动写命令启动的。

在开悟平台中：

点击“开始训练”

平台自动启动整个训练环境。

环境会不断把：
env_obs

传给：

agent.observation_process()

然后流程是：

Preprocessor 处理状态
↓
PPO 输出动作
↓
环境执行动作
↓
返回 reward 和新状态

整个过程不断循环。

模型怎么保存和加载

模型保存和加载已经在源码中实现好了。

主要函数一般在：

agent.py

里面：

save_model()
load_model()

作用：

save_model()
训练过程中自动保存模型参数。
load_model()
评估或继续训练时加载模型。

模型文件通常保存在：

ckpt/

目录下。

例如：

model.ckpt-23523.pkl

表示第 23523 次保存的模型。

最终分数怎么评估

最终效果主要看评估面板（eval）。

常见指标包括：

eval score

eval charge

cleaned

finished_steps

reward 曲线

其中：

eval score

最终得分。

eval charge

平均充电次数 / 回充表现。

finished_steps

是否活得足够久。

评估时通常没有训练阶段的随机探索，因此：

eval 更能反映真实策略质量。

很多模型会出现：

train 看起来很好，
但 eval 崩掉。

这属于典型的泛化问题。
