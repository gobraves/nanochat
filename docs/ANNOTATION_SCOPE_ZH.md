# 已补充中文学习注释的必要文件清单

本轮已对 nanochat 学习主链路中的关键文件补充中文学习注释（以 `[ZH-LEARNING-NOTE]` 标记）：

## 核心库（nanochat/）
- common.py
- gpt.py（前几轮已补）
- dataloader.py（前几轮已补）
- engine.py
- optim.py
- tokenizer.py
- checkpoint_manager.py
- loss_eval.py
- core_eval.py
- dataset.py
- execution.py
- report.py
- flash_attention.py
- fp8.py

## 训练/评估脚本（scripts/）
- base_train.py（前几轮已补）
- base_eval.py
- chat_sft.py
- chat_rl.py
- chat_eval.py
- chat_cli.py
- chat_web.py
- tok_train.py
- tok_eval.py

## 任务数据（tasks/）
- common.py
- gsm8k.py
- mmlu.py
- smoltalk.py
- customjson.py
- humaneval.py
- arc.py
- spellingbee.py

---

说明：
- 注释目标是“帮助初学者快速建立阅读路径与关注点”，尽量不改动原始逻辑。
- 如需下一步，我可以继续做“函数级逐段中文注释”（更细，但改动会更多）。
