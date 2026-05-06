# Data Hub 

A centralized repository for mock data, public configurations, and script resources used in development and testing.

一个用于存放接口 Mock 数据、公共配置文件及脚本资源的统一仓库。

---

##  Overview | 项目简介

EN
- Mock data for API testing
- Public configuration files
- JSON-based scripts and datasets
- Lightweight data source for frontend development

中文
- 用于接口调试的 Mock 数据
- 公共配置文件管理
- JSON 脚本与数据集
- 前端开发的轻量数据源

---

##  Structure | 目录结构
data-hub/
├── mock/        # API mock 数据
├── config/      # 公共配置
├── scripts/     # JSON脚本/规则
├── fixtures/    # 测试数据
---

##  Usage | 使用方式

EN

You can directly use the raw GitHub URLs as a data source:

https://raw.githubusercontent.com/<your-username>/data-hub/main/mock/example.json

Perfect for:
- Frontend mock requests
- Prototyping
- Testing without backend

---

中文

可以直接使用 GitHub Raw 地址作为数据源：

https://raw.githubusercontent.com/<your-username>/data-hub/main/mock/example.json

适用于：
- 前端接口 Mock
- 快速原型开发
- 无后端调试场景

---

##  Purpose | 项目目标

EN
- Decouple frontend from backend dependencies
- Enable rapid development and testing
- Maintain reusable data assets

中文
- 解耦前后端依赖
- 提升开发与调试效率
- 沉淀可复用的数据资源

---

##  Notes | 注意事项

EN
- This repository is public
- Do not include sensitive data
- Data may change without notice

中文
- 本仓库为公开仓库
- 请勿上传敏感数据
- 数据可能随时更新

---
