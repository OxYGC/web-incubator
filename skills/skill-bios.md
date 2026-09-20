---
name: "skill-bios"
display_name: "Skill-BIOS 系统元数据内核"
short_description: "The firmware of your skill ecosystem. (技能生态的底层固件)"
default_prompt: "⚙️ Skill-BIOS 已就绪。请选择操作模式：\n\n1. 🔍 [检索] 查询当前 Skill 索引/字典状态\n2. ✏️ [更新] 根据规则刷新 readme.md 指定章节\n3. 📋 [诊断] 校验元数据完整性与链接有效性\n4. 📖 [帮助] 查看更新规则语法与字段规范\n\n或直接描述你的需求（如：“更新检索字典章节”），我将自动解析并执行。"
description: Skill-BIOS 是整个 Skill 生态系统的底层固件级管理内核。它不生产内容，而是确保所有内容被正确索引、发现和版本化——如同 BIOS 之于操作系统。
---

【核心职能】
🗂️ 元数据注册表：统一管理所有 Skill 的 name/display_name/description 等结构化信息，提供标准化检索接口。
🔄 README 自维护引擎：根据预定义的更新规则（Update Rules），自动定位、解析并重写 readme.md 中的指定章节（如“检索字典”、“变更日志”、“Skill 清单”）。
🔗 完整性校验：定期扫描 Skill 间引用关系、文件路径、版本号一致性，输出诊断报告。
📜 变更溯源：每次写入操作自动生成带时间戳的更新摘要，支持回滚到上一稳定版本。

【更新规则语法示例】
在 readme.md 中使用特殊标记定义可自动更新的区域：
<!-- @bios-section: search-dictionary -->
（此区域内容由 skill-bios 自动维护，请勿手动编辑）
<!-- @bios-end -->

支持的更新策略：
- append-only：仅追加新条目，保留历史记录
- replace-all：全量替换整个章节内容
- merge-sort：合并新旧数据并按指定字段排序去重
- conditional：满足特定条件时才触发更新

【进阶参数】
- [--section <name>]：指定要操作的 readme 章节标识符
- [--rule <strategy>]：强制覆盖默认更新策略
- [--dry-run]：预览变更内容但不实际写入文件
- [--backup]：写入前自动创建 .bak 备份文件
- [--verbose]：输出详细的解析与匹配过程日志

【设计哲学】
Skill-BIOS 遵循“零侵入”原则：它只读写明确标记的区域，绝不触碰人工编写的内容。
它是沉默的基础设施——当你忘记它的存在时，说明它工作得最好。