# EffectCraft — runtime-distribution

## Purpose

本能力定义 EffectCraft 在 runtime-distribution 范围内对用户、宿主与下游系统承诺的可观察行为、失败语义和验收证据，确保规划、执行与实际交付之间保持可验证的边界。当前实现与验收状态以 tasks 和逐场景证据为准。

## ADDED Requirements

### Requirement: EC-RT-001 运行时来源与完整性

运行时安装 SHALL 固定制品来源、版本、平台及摘要；在暂存区验证后原子安装，保留许可与安装回执；不执行未经验证的下载内容。

#### Scenario: EC-RT-001-P 合同条件满足

- **WHEN** 请求满足本需求的来源、输入、状态和证据条件
- **THEN** 系统按本需求完成运行时来源与完整性并返回可核对的结果
- **AND** 结果绑定当前版本与执行身份，不提升未验证能力状态

#### Scenario: EC-RT-001-N 边界条件

- **WHEN** 下载内容与锁定摘要不符
- **THEN** 拒绝安装且现有运行时仍可使用

#### Scenario: 并行首次安装与复用
- **WHEN** 两个进程同时安装或复用同一锁定 CLI
- **THEN** 安装互斥等待最多 120 秒，取得锁后核验并复用已原子发布的版本；不存在重复下载或部分安装被运行
- **AND** 超时返回 runtime_install_busy，保留已有安装，不自动重放任何编辑或渲染

### Requirement: EC-RT-002 运行能力与隔离升级

适配器 SHALL 核对运行时版本和实际命令 schema，区分 headless 与 desktop bridge；升级必须排空任务、保留回退版本，禁止回退到不兼容状态 schema。

#### Scenario: EC-RT-002-P 合同条件满足

- **WHEN** 请求满足本需求的来源、输入、状态和证据条件
- **THEN** 系统按本需求完成运行能力与隔离升级并返回可核对的结果
- **AND** 结果绑定当前版本与执行身份，不提升未验证能力状态

#### Scenario: EC-RT-002-N 边界条件

- **WHEN** 新运行时缺少计划要求的能力
- **THEN** 执行前报 capability_missing，禁止静默替换工具


#### Scenario: [EC-RT-002-DOWNLOAD-RECOVERY] 原生制品下载的临时中断

- **WHEN** 首用下载锁定原生CLI制品发生SSL EOF、超时、连接中断、短读或408／429／5xx
- **THEN** 安装器 SHALL 丢弃半包并最多执行三次只读下载，之后仍执行原摘要／安全解压／版本检查，不重试原生编辑
- **AND** 证书、权限、磁盘、大小限制、摘要及非临时HTTP拒绝错误 SHALL 不被重试或放宽；固定发行及安装首用另行验收

### Requirement: EC-DS-001 Owned standalone desktop workflow
Each domain skill SHALL offer `desktop.py run PLAN --output NEW_DIRECTORY` that validates the plan before installation, verifies fixed desktop and CLI identities, starts only its own isolated desktop, confirms the loopback listener belongs to that process, executes the existing full-command bridge gateway, and closes only its owned desktop/MCP processes on success or failure. Photo authentication SHALL use a private token file and the same authorized output root in both desktop and CLI. Unknown editing outcomes SHALL not be replayed.

#### Scenario: First use without a running desktop
- **WHEN** a standalone skill runs a valid plan against empty runtime caches
- **THEN** fixed desktop and CLI are installed, an owned bridge is started and the workflow receipt plus desktop lifecycle receipt are preserved

#### Scenario: Invalid plan or startup failure
- **WHEN** the plan is invalid
- **THEN** no installation or desktop launch occurs
- **WHEN** owned desktop startup or MCP initialization fails
- **THEN** only owned processes are closed, logs and failure receipts are retained, and edits are not retried

#### Scenario: Interrupted command is not replayed
- **WHEN** a standalone desktop workflow is interrupted with an editing request started
- **THEN** completed steps remain preserved, the started request becomes unknown, and failure plus owned-process lifecycle receipts are written before exit code 130

#### Scenario: Ambiguous JSON plan
- **WHEN** a plan contains duplicate object keys or nonfinite numeric values
- **THEN** the runner rejects it before installing or starting a desktop

#### Scenario: Workflow metadata cannot be overwritten
- **WHEN** a command plan names desktop-session.json, desktop.log, .desktop-data or artcraft-domain-command.json as a root deliverable through $output
- **THEN** validation rejects the plan before installation or editing, preserving workflow receipts and owned desktop configuration

#### Scenario: Bounded TLS download recovery
- **WHEN** a pinned desktop download fails with curl TLS handshake exit35
- **THEN** the installer retries at most three total attempts with clean private downloads and bounded connection/request timeouts before any desktop starts
- **AND** other failure classes are propagated without an extra outer retry; archive identity and signature checks remain mandatory

#### Scenario: Native bridge-only GUI tools
- **WHEN** a plan uses one of the nine pinned Effect bridge-only tools
- **THEN** headless preflight rejects it before setup; explicit bridge or owned desktop mode validates its actual live input schema before execution
- **AND** local list/describe exposes the real bridge-only schemas without requiring installation

### Requirement: EC-RT-003 独立跨平台启动

每个独立技能 SHALL 提供不依赖系统 Python 的启动入口，按平台锁准备隔离 Python 与原生运行时；所有下载先验证摘要、许可及平台再发布。未验收平台 SHALL 显式记录而非回退其他平台。

#### Scenario: 空环境

- **WHEN** 系统没有 Python 或 EffectCraft
- **THEN** 系统 SHALL 从技能自身资源准备锁定环境，不修改系统 PATH 或插件文件

#### Scenario: 损坏制品

- **WHEN** Python 或原生制品摘要错误
- **THEN** 系统 SHALL 拒绝执行并保留已有安装

#### Scenario: 固定平台锁与最低系统条件

- **WHEN** 在 macOS arm64/x86_64、Windows x86/x64/arm64 或 Linux x86_64/aarch64 首次启动
- **THEN** 技能 SHALL 只选择锁文件中对应平台的受维护 Python 3.13 补丁版本和 EffectCraft 制品；锁 SHALL 包含版本、来源、SHA-256、许可及从制品取得的最低系统/libc 条件，启动前核对条件
- **AND** 未匹配或不满足条件时 SHALL 明确失败，不解析 latest、不借用其他架构制品

#### Scenario: 安全安装与离线制品

- **WHEN** 下载中断、归档含越界路径/链接、安装并发、权限不足、升级失败，或用户提供离线制品
- **THEN** 安装器 SHALL 在用户目录私有暂存区验证完整性及解包安全性，经互斥锁原子发布；失败保留可用旧版，离线制品执行相同校验
- **AND** 不改系统 Python、全局 PATH、插件只读安装目录，活动任务继续使用其绑定版本

#### Scenario: Web 与 FreeBSD 独立通道

- **WHEN** 运行环境是 Web 或 FreeBSD
- **THEN** Web SHALL 通过实际浏览器 API 单独发现并验证能力；FreeBSD SHALL 走固定源码构建与本机验证通道
- **AND** 两者的验收状态分别记录，不以 Linux 二进制或静态平台模拟冒充成功

#### Scenario: doctor 只读诊断与命令差异

- **WHEN** 用户或宿主调用 doctor，或锁定原生 CLI 命令目录发生变化
- **THEN** doctor SHALL 只读返回平台、缺失依赖、锁定与实际版本、运行模式、可执行恢复动作；命令清单 SHALL 记录参数契约、技能归属、运行模式、场景验收状态
- **AND** 新增或变化命令默认未验收，清单生成不得将其直接标记为可用于创作

#### Scenario: 单技能双启动入口与制品选择

- **WHEN** 用户在没有系统 Python 的受支持原生平台调用任一独立技能
- **THEN** 技能 SHALL 提供 POSIX Shell 与 PowerShell 启动入口，准备用户目录内隔离 Python 后执行同一 Python 工作流；Windows SHALL 使用 Python 官方嵌入式发行包，macOS/Linux SHALL 使用固定 python-build-standalone install_only 制品
- **AND** 平台锁 SHALL 选择各目标共同可用的受维护 Python 3.13 补丁版本并保存版本、平台、来源、摘要及许可；版本选择在构建时确定，运行时不得解析 latest

#### Scenario: 活动任务引用阻止运行时清理

- **WHEN** 安装升级或清理旧运行时，而已有活动任务引用旧运行时身份
- **THEN** 系统 SHALL 保留被引用的原版本，不切换该任务的解释器或 EffectCraft；新任务可以绑定已验证的新版本
- **AND** 升级失败 SHALL 保留旧版本及其任务记录，不通过回退破坏状态兼容性

#### Scenario: 平台适配覆盖执行路径

- **WHEN** 原生任务使用文件锁、管道读取、进程树管理、安装路径或桌面启动
- **THEN** 执行器 SHALL 使用对应平台适配实现，不能以 Darwin 硬编码或 Windows 不支持的调用承担跨平台执行
- **AND** 平台分支单元测试不替代该目标机上的实际运行证据

#### Scenario: Windows默认代码页与中文合同

- **WHEN** 原生平台默认文本编码不是UTF-8，技能读取中文计划/合同或写出中文工程回执，或分发工具生成技能资源
- **THEN** 系统 SHALL 显式使用UTF-8读取和写入文本合同、计划及JSON回执，公共入口的重定向帮助/诊断输出 SHALL 使用UTF-8；分发生成文本 SHALL 使用确定性LF换行，不得依赖宿主默认代码页
- **AND** 管道按实际字节读取，二进制协议夹具 SHALL 跨平台输出相同字节，不通过放宽断言掩盖协议或编码失败
