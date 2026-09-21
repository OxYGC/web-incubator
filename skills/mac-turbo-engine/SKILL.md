---
name: "mac-turbo-engine"
display_name: "Mac 效率引擎"
short_description: "新机器Mac的第一个Skill，从配置Mac环境命令开启您的一站式完成 Mac 软件安装、环境配置和效率优化设置"
default_prompt: "Use $mac-turbo-engine to safely install and configure my requested Mac software."
description: "Automates full Mac setup: environment config, priority-based software installation from configurable sources, and post-install scripts. Invoke when user gets a new Mac, needs to install/configure software, or wants to run setup automation."
---

# Mac Turbo 引擎 (Mac Turbo Engine)
从"配置Mac环境"命令开启你的Mac效率之旅。
新机器 Mac 的第一个 Skill，一站式完成 Mac 环境配置、软件安装、脚本执行和系统优化。从拿到新 Mac 到开发就绪，全流程自动化。

---

## 文件结构

| 文件 | 用途 |
|------|------|
| `skill.md` | 本文件 — 主流程编排、安全准则、交互模式 |
| `environment.yaml` | 环境配置清单 — Phase 1 使用的所有环境项定义 |
| `software.yaml` | 软件配置清单 — Phase 2 使用的所有软件项定义 |

用户可通过编辑 `environment.yaml` 和 `software.yaml` 来自定义安装内容、优先级和来源地址。

---

## 核心能力

| 阶段 | 说明 |
|------|------|
| 环境配置 | Homebrew、Xcode CLT、Shell（zsh/oh-my-zsh）、开发语言运行时 |
| 软件安装 | 按 Priority 级别分批安装，支持 Homebrew cask / App Store / 自定义 URL |
| 后置脚本 | 软件安装完成后自动执行配置脚本、偏好设置、权限授予 |
| 系统优化 | Dock、Finder、键盘、安全等系统偏好一键设置 |

---

## Phase 1 — 环境配置 (Environment Setup)

### 1.1 执行流程

读取 `environment.yaml` 清单，按清单顺序逐项执行：

1. **检测** — 对每项执行 `check` 命令，判断是否已安装
2. **标注** — 如果已安装，获取 `version` 并在名称后标注 `(已经安装X版本)`
3. **安装** — 对未安装的项执行 `install` 命令
4. **后置** — 安装成功后执行 `post_install` 脚本
5. **报告** — 输出环境配置清单的状态

### 1.2 环境配置清单展示格式

执行前向用户展示如下格式的清单：

```
========== 环境配置清单 ==========

[基础工具链]
  1. Xcode Command Line Tools          ✅ (已经安装15.3版本)
  2. Homebrew                           ✅ (已经安装4.2.21版本)

[包管理器]
  3. ...

[Shell 增强]
  4. Oh My Zsh                         ❌ 未安装

[语言运行时]
  5. NVM (Node Version Manager)         ❌ 未安装
  6. Node.js (LTS via nvm)             ⏳ 依赖 NVM
  7. Python                             ✅ (已经安装3.12.2版本)
  ...

==================================
```

### 1.3 环境项字段说明 (environment.yaml)

| 字段 | 必填 | 说明 |
|------|------|------|
| `name` | 是 | 环境项名称 |
| `category` | 是 | 分类（基础工具链/包管理器/Shell增强/语言运行时/安全配置） |
| `check` | 是 | 检测命令，返回非空表示已安装 |
| `install` | 是 | 安装命令 |
| `version` | 否 | 版本获取命令，用于标注已安装版本号 |
| `post_install` | 否 | 安装后执行的脚本列表 |
| `depends_on` | 否 | 依赖的其他环境项名称 |
| `mirror_cn` | 否 | 中国大陆镜像源配置 |
| `note` | 否 | 注意事项，展示给用户 |

### 1.4 中国大陆镜像源

当检测到用户在中国大陆环境时（可通过 `curl -s ipinfo.io | grep China` 或询问用户），自动应用 `mirror_cn` 中的镜像配置。

---

## Phase 2 — 软件安装 / 配置 / 卸载 (Software Management)

### 2.1 Priority 优先级体系

安装按以下优先级分批执行，**同优先级内并行安装**：

| 级别 | 名称 | 含义 |
|------|------|------|
| P0 | Critical | 系统运行必需、安全工具 |
| P1 | High | 日常开发核心工具 |
| P2 | Medium | 效率工具 |
| P3 | Low | 按需工具 |

### 2.2 软件源类型 (Source Types)

| Source | 说明 | 安装命令 |
|--------|------|----------|
| `brew` | Homebrew cask/formula | `brew install --cask <formula>` |
| `mas` | Mac App Store | `mas install <app_id>` |
| `url` | 自定义直链 | 下载 .dmg/.pkg 并挂载安装 |

### 2.3 软件清单展示格式

执行前读取 `software.yaml`，对每项运行 `check` 和 `version` 命令，向用户展示：

```
========== 软件配置清单 ==========

[P0 — Critical]
  1. Google Chrome              ✅ (已经安装127.0.6533.72版本)
  2. 1Password                  ✅ (已经安装8.10.34版本)
  3. Xcode                      ❌ 未安装 ⚠️ 体积较大(10GB+)

[P1 — High]
  4. iTerm2                     ✅ (已经安装3.5.5版本)
  5. Visual Studio Code         ✅ (已经安装1.92.1版本)
  6. IntelliJ IDEA              ❌ 未安装
  7. Docker Desktop             ✅ (已经安装27.1.1版本)
  8. Warp                       ❌ 未安装
  9. DBeaver Community          ❌ 未安装
  10. Postman                   ✅ (已经安装10.25.0版本)
  11. TablePlus                 ❌ 未安装

[P2 — Medium]
  12. Raycast                   ✅ (已经安装1.74.0版本)
  13. Rectangle Pro             ❌ 未安装
  14. AltTab                    ❌ 未安装
  ...

[P3 — Low]
  18. IINA                      ❌ 未安装
  19. Discord                   ✅ (已经安装0.0.27版本)
  ...

==================================
```

### 2.4 软件项字段说明 (software.yaml)

| 字段 | 必填 | 说明 |
|------|------|------|
| `name` | 是 | 软件名称 |
| `priority` | 是 | 优先级 P0/P1/P2/P3 |
| `source` | 是 | 安装源 brew/mas/url |
| `formula` | brew 必填 | Homebrew cask/formula 名称 |
| `app_id` | mas 必填 | Mac App Store 应用 ID |
| `url` | url 必填 | 自定义下载地址 |
| `check` | 是 | 检测命令，返回非空表示已安装 |
| `version` | 否 | 版本获取命令 |
| `post_install` | 否 | 安装后执行的脚本 |
| `note` | 否 | 注意事项 |

### 2.5 安装执行逻辑

```
1. 读取 software.yaml，对每项执行 check + version
2. 按优先级分组展示清单（含已安装版本标注）
3. 用户确认后，按 P0 → P1 → P2 → P3 顺序执行：
   a. 跳过已安装的项
   b. 对未安装项执行安装
   c. 等待该优先级组全部完成
   d. 执行该组的 post_install 脚本
4. 输出安装报告
```

### 2.6 软件卸载

用户可通过对话指定卸载软件，执行逻辑：

```bash
# Homebrew 安装的软件
brew uninstall --cask <formula>

# App Store 安装的软件
mas uninstall <app_id>

# 手动安装的软件
sudo rm -rf "/Applications/<App Name>.app"
```

卸载前需向用户确认，卸载后可选执行清理脚本。

---

## Phase 3 — 后置脚本 (Post-Install Scripts)

### 3.1 脚本执行规则

- 后置脚本在对应软件安装完成后**立即执行**
- 每条脚本独立执行，失败不阻塞后续脚本（但会记录错误）
- 脚本超时默认 60 秒，用户可自定义
- 涉及 `sudo` 的脚本需提前告知用户并确认

### 3.2 链式脚本

支持脚本依赖链，按顺序执行：

```yaml
post_install:
  - cmd: "open -a Docker"
    wait_for: "docker info"
    timeout: 120
  - cmd: "docker pull node:20"
    depends_on: "docker info"
  - cmd: "docker compose up -d"
    depends_on: "docker pull node:20"
```

| 字段 | 说明 |
|------|------|
| `cmd` | 要执行的命令 |
| `wait_for` | 等待此命令成功后再继续 |
| `depends_on` | 依赖的前序步骤 |
| `timeout` | 超时时间（秒），默认 60 |

### 3.3 常见后置脚本

```bash
# 设置默认浏览器
defaults write com.google.chrome DefaultBrowserSetting -bool true

# 配置 Git 全局信息
git config --global user.name "用户名"
git config --global user.email "user@example.com"

# SSH 密钥生成
ssh-keygen -t ed25519 -C "user@example.com" -f ~/.ssh/id_ed25519 -N ""

# 启动后台服务
brew services start nginx
brew services start redis
```

### 3.4 Docker 常用服务部署

Docker Desktop 或 OrbStack 安装完成后，可快速部署以下常用开发服务：******************

#### Redis

```bash
# 拉取镜像
docker pull redis:7.4.1

# 启动 Redis 容器（端口 16379，自动重启）
docker run \
  --restart always \
  --name redis16379 \
  --privileged=true \
  -p 16379:6379 \
  -v /docker/redis16379/redis.conf:/etc/redis/redis.conf \
  -v /docker/redis16379/data:/data:rw \
  -d redis redis-server /etc/redis/redis.conf
```

#### MySQL

```bash
# 拉取镜像
docker pull mysql:8.4.3

# 启动 MySQL 容器（端口 13306，root 密码 123456，自动重启）
docker run \
  -p 13306:3306 \
  --privileged=true \
  --restart=always \
  --name mysql13306 \
  -v /docker/mysql13306/conf:/etc/mysql/conf.d \
  -v /docker/mysql13306/logs:/logs \
  -v /docker/mysql13306/data:/var/lib/mysql \
  -e MYSQL_ROOT_PASSWORD=123456 \
  -d mysql:8.4.3
```

| 服务 | 端口 | 镜像 | 说明 |
|------|------|------|------|
| Redis | 16379 | redis:7.4.1 | 缓存与消息队列 |
| MySQL | 13306 | mysql:8.4.3 | 关系型数据库 |

---

## Phase 4 — 系统优化 (System Optimization)

### 4.1 Dock 偏好

```bash
defaults write com.apple.dock autohide -bool true
defaults write com.apple.dock autohide-delay -float 0
defaults write com.apple.dock show-recents -bool false
defaults write com.apple.dock orientation -string "right"
killall Dock
```

### 4.2 Finder 偏好

```bash
defaults write NSGlobalDomain AppleShowAllExtensions -bool true
defaults write com.apple.finder ShowPathbar -bool true
defaults write com.apple.finder ShowStatusBar -bool true
defaults write com.apple.finder FXPreferredViewStyle -string "clmv"
killall Finder
```

### 4.3 键盘与输入

```bash
defaults write NSGlobalDomain KeyRepeat -int 2
defaults write NSGlobalDomain InitialKeyRepeat -int 15
defaults write NSGlobalDomain ApplePressAndHoldEnabled -bool false
```

### 4.4 安全与隐私

```bash
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setglobalon --setblockall off
sudo /usr/libexec/ApplicationFirewall/socketfilterfw --setstealthmode on
```

### 4.5 截图设置

```bash
defaults write com.apple.screencapture location -string "${HOME}/Screenshots/"
defaults write com.apple.screencapture type -string "png"
killall SystemUIServer
```

### 4.6 系统完整性保护 (SIP)

**SIP (System Integrity Protection)** 是 macOS 的系统完整性保护机制。关闭后可以安装更多第三方软件，但会降低系统安全性。

```bash
# 检查 SIP 状态
csrutil status
```

**关闭 SIP 步骤**（需进入恢复模式）：
1. 关机后长按电源键进入 macOS 恢复模式
2. 进入「选项」→ 菜单栏「实用工具」→「终端」
3. 执行 `csrutil disable`
4. 重启电脑

⚠️ **注意事项**：
- 新手不建议关闭 SIP
- 关闭 SIP 后 M 系列 Mac 将无法运行 iPhone/iPad 应用
- 如需重新开启，在恢复模式执行 `csrutil enable`

### 4.7 关闭虚拟内存 (Swap)

关闭 swap 可减少 SSD 写入磨损，但需要先关闭 SIP。内存较小的 Mac 不建议关闭。

```bash
# 查看虚拟内存使用情况
sysctl vm.swapusage

# 关闭虚拟内存（需先关闭 SIP）
sudo launchctl unload -w /System/Library/LaunchDaemons/com.apple.dynamic_pager.plist
```

操作完成后建议重新开启 SIP 以保证系统安全。

---

## Homebrew 常用命令速查

| 功能 | 命令 |
|------|------|
| 安装软件 | `brew install <formula>` |
| 卸载软件 | `brew uninstall <formula>` |
| 强制卸载所有版本 | `brew uninstall <formula> --force` |
| 更新 Homebrew 自身 | `brew update` |
| 查看可更新的包 | `brew outdated` |
| 更新所有包 | `brew upgrade` |
| 更新指定包 | `brew upgrade <formula>` |
| 锁定包不更新 | `brew pin <formula>` |
| 解锁包 | `brew unpin <formula>` |
| 清理旧版本 | `brew cleanup` |
| 列出已安装的包 | `brew list` |
| 查看软件信息 | `brew info <formula>` |
| 搜索软件 | `brew search <keyword>` |
| 启动后台服务 | `brew services start <formula>` |
| 停止后台服务 | `brew services stop <formula>` |
| 重启后台服务 | `brew services restart <formula>` |
| 查看服务状态 | `brew services list` |

---

## 版本检测与标注规则

### 检测逻辑

对清单中的每一项，按以下顺序检测：

1. 执行 `check` 命令 — 返回值非空表示已安装
2. 如果已安装，执行 `version` 命令获取版本号
3. 按格式标注：`<软件名> (已经安装<版本号>版本)`

### 标注示例

| 场景 | 标注格式 |
|------|----------|
| 已安装，版本可获取 | `IntelliJ IDEA (已经安装2024.2.1版本)` |
| 已安装，版本不可获取 | `自定义工具 (已经安装)` |
| 未安装 | `IntelliJ IDEA` (无标注) |
| 依赖项未就绪 | `Node.js (LTS) ⏳ 依赖 NVM` |

### 状态图标

| 图标 | 含义 |
|------|------|
| ✅ | 已安装 |
| ❌ | 未安装 |
| ⏳ | 依赖未就绪 |
| ⚠️ | 有注意事项 |
| 🔄 | 安装中 |
| ❎ | 安装失败 |

---

## 安全准则 (Safety Guidelines)

1. **用户确认** — 所有涉及 `sudo`、`rm`、系统级修改的命令必须先向用户展示并获得确认
2. **幂等性** — 所有操作必须可重复执行，已安装的软件自动跳过
3. **回滚** — 安装失败时记录详细错误日志，不继续执行依赖该软件的后置脚本
4. **密码安全** — 不在脚本中硬编码密码或 token，使用 macOS Keychain 或环境变量
5. **网络检测** — 安装前检测网络连通性，中国大陆环境下优先使用镜像源
6. **架构识别** — 自动检测 Apple Silicon (arm64) vs Intel (x86_64)，选择正确的软件包
7. **磁盘空间** — 安装前检查可用磁盘空间，低于 10GB 时警告用户

---

## 执行流程总览

```
新 Mac 开箱
    │
    ▼
Phase 1: 环境配置（读取 environment.yaml）
    ├── 展示环境清单（含已安装版本标注）
    ├── 用户确认
    ├── 逐项检测 → 安装 → 后置脚本
    └── 输出环境配置报告
    │
    ▼
Phase 2: 软件安装（读取 software.yaml）
    ├── 展示软件清单（含已安装版本标注）
    ├── 用户确认
    ├── 按 P0→P1→P2→P3 分批安装
    └── 输出安装报告（成功/失败/跳过）
    │
    ▼
Phase 3: 后置脚本（链式执行）
    ├── 配置文件生成
    ├── 服务启动
    └── 权限授予
    │
    ▼
Phase 4: 系统优化
    ├── Dock / Finder
    ├── 键盘 / 截图
    └── 安全设置
    │
    ▼
最终报告（汇总四个阶段结果）
```

---

## 用户交互模式

当用户触发此 skill 时，按以下步骤交互：

1. **判断场景** — 新机全量配置 / 补装部分软件 / 仅系统优化 / 卸载软件
2. **环境检测** — 检测当前系统状态（架构、已安装环境项）
3. **展示清单** — 读取 YAML 清单，展示带版本标注的清单
4. **用户确认** — 向用户展示将执行的操作，获得确认
5. **执行** — 按 Phase 1→4 顺序执行，每个关键步骤输出进度
6. **报告** — 完成后输出汇总报告

如果用户只说"帮我配 Mac"而无具体清单，则推荐 `software.yaml` 中的默认软件包并询问是否调整。

如果用户只想安装特定软件，则只展示并执行该软件的安装流程。
