# nanochat 学习指南（中文，面向机器学习入门者）

> 目标：你已经懂基础机器学习（损失函数、梯度下降、训练/验证集），希望通过 `nanochat` 快速建立 GPT/LLM 工程直觉。

## 1. 先建立整体心智模型

把这个项目看成 4 个阶段：

1. **Tokenizer（分词器）**：把文本变成 token id
2. **Base Pretraining（基础预训练）**：训练“下一个 token 预测器”
3. **SFT/RL（对齐阶段）**：让模型更像聊天助手
4. **Inference/UI（推理与交互）**：CLI/Web 对话

在代码里大致对应：

- 分词：`scripts/tok_train.py`、`scripts/tok_eval.py`、`nanochat/tokenizer.py`
- 预训练：`scripts/base_train.py`、`nanochat/gpt.py`
- 评估：`scripts/base_eval.py`、`nanochat/loss_eval.py`、`nanochat/core_eval.py`
- 聊天阶段：`scripts/chat_sft.py`、`scripts/chat_rl.py`、`scripts/chat_web.py`

---

## 2. 你最该先读的三个文件

### A) `nanochat/gpt.py`
这是模型本体，重点关注：

- `GPTConfig`：层数、头数、维度等超参数
- `CausalSelfAttention`：因果注意力（只能看历史 token）
- `MLP`：前馈网络
- `Block`：Attention + MLP 的残差堆叠
- `GPT.forward(...)`：输入 token，输出 logits

### B) `scripts/base_train.py`
这是“如何把模型训起来”。重点关注：

- 参数解析（batch size、depth、lr、迭代步数）
- 模型构建与初始化
- 优化器与学习率调度
- 训练循环：forward/loss/backward/step
- eval/checkpoint/sample

### C) `nanochat/dataloader.py`
这是“token 数据怎么持续喂给 GPU”。先理解：

- 一个 batch 的张量形状
- `x`（输入序列）和 `y`（右移一位的标签）
- 分布式下每个进程拿不同 shard 的思想

---

## 3. 与你熟悉的传统 ML 的对照

- 传统分类：`x -> f(x) -> softmax -> CE loss`
- GPT 训练：`token序列 -> Transformer -> 每个位置的词表logits -> CE loss`

本质仍然是最小化损失，只是：

- 输入是序列
- 输出是每个位置的“下一个 token”分类
- 模型结构是 Transformer

---

## 4. 建议的学习顺序（1~2 天）

### 第 1 阶段（跑通）

1. 先跑一个极小训练（CPU 也可，慢但能理解流程）
2. 看日志里 loss 怎么变化
3. 跑一次 sample，看模型在胡说八道但“有语言形态”

可参考 README 中 CPU/MPS 示例。

### 第 2 阶段（看懂关键张量）

建议你在本地加断点或打印以下 shape：

- token batch: `(B, T)`
- embedding 后: `(B, T, C)`
- attention q/k/v: `(B, T, H, D)`
- logits: `(B, T, V)`

其中：

- `B`=batch size
- `T`=序列长度
- `C`=隐藏维度
- `H`=注意力头数
- `D`=每个头的维度
- `V`=词表大小

### 第 3 阶段（理解“为什么这么配参数”）

`nanochat` 的设计哲学是：**主要调一个旋钮 `--depth`**，其余参数尽量自动推导。

这和很多“超大配置系统”不同，更适合学习和快速实验。

---

## 5. 常见坑（新手高频）

1. **OOM（显存不足）**
   - 先减 `--device-batch-size`
   - 再减 `--max-seq-len`
2. **训练很慢**
   - 确认是否用了 GPU
   - 确认是否分布式启动方式正确
3. **loss 不降**
   - 检查数据/分词器是否匹配
   - 检查学习率是否异常
4. **只看 loss 误判效果**
   - 同时看 sample 质量和下游评估指标

---

## 6. 一个“最小可理解实验”建议

你可以做一个 30~60 分钟的小实验：

1. 固定模型规模（比如较小 depth）
2. 只改变一个变量（例如学习率）
3. 记录三件事：
   - 收敛速度
   - 最终验证 loss/bpb
   - sample 可读性

这样你会比“盲调很多参数”学得快得多。

---

## 7. 如果你下一步要系统学 LLM，建议路径

1. 先完全看懂本仓库预训练主线（base_train + gpt）
2. 再看 SFT（chat_sft）
3. 最后看 RL（chat_rl）

顺序别反，否则会陷入工程细节，忽略主干。

---

## 8. 你可以怎么继续用这个仓库学习

- 用小模型做 ablation（改一处看变化）
- 给关键函数补自己的注释
- 画出数据流图（token -> embedding -> block -> logits）
- 每次实验写 3 行结论，积累“可复用经验”

如果你愿意，我下一步可以基于你的机器配置（单卡/显存大小）给你出一份“可跑、可学、可复现”的 7 天学习计划。
