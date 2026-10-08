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

#### Scenario: 已安装版本回执损坏或身份漂移

- **WHEN** 复用已安装CLI时，安装回执不是无歧义UTF-8 JSON对象，或其中版本、平台、来源、制品摘要、版本输出与固定锁不一致
- **THEN** 安装器 SHALL 拒绝复用并保留原目录和回执供核对，不覆盖、不重新下载、不自动回退到其他版本
- **AND** 新版本安装失败 SHALL 不改变旧版本的文件和回执；只有完成摘要、许可、完整载荷与实际版本验证的新目录才能原子发布

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

#### Scenario: 固定命令目录的离线升级差异

- **WHEN** 智能体比较已发布旧覆盖目录和候选固定目录
- **THEN** 入口 SHALL 不安装、不启动原生编辑、不写任务或工程，确定性列出新增、移除、参数合同、技能归属、工作流映射、运行模式路由及原生工具schema变化；未知schema、领域不符、重复身份及歧义JSON SHALL 拒绝
- **AND** 新增／变化合同 SHALL 保持 NOT_RUN，运行时或工具网关变化 SHALL 列出需要重新验收的命令；旧目录缺少模式或工具schema SHALL 显式记录缺失，不视为合同相同或继承历史PASS
- **AND** 覆盖目录生成 SHALL 核对固定反射与原生目录的完整身份集合，新增／缺失／重复命令 SHALL 在写出文档前报 native_registry_drift；模式路由不替代实际enabled状态及逐命令创作验收

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

#### Scenario: Python安装回执保护启动

- **WHEN** 独立启动入口复用已有隔离Python目录
- **THEN** 入口 SHALL 在执行解释器前核验非链接的安装回执，固定版本、平台和归档摘要；损坏、缺失、重复／额外字段、身份不匹配或非法编码 SHALL 拒绝，保留原回执与载荷，不自动重新安装
- **AND** 既有入口生成的合法字段顺序与格式 SHALL 可读取；新发布Python目录 SHALL 同时具备已验证载荷与匹配回执，不能以校验单个可执行文件替代完整发行验证

#### Scenario: Web 与 FreeBSD 独立通道

- **WHEN** 运行环境是 Web 或 FreeBSD
- **THEN** Web SHALL 通过实际浏览器 API 单独发现并验证能力；FreeBSD SHALL 走固定源码构建与本机验证通道
- **AND** 两者的验收状态分别记录，不以 Linux 二进制或静态平台模拟冒充成功

#### Scenario: doctor 只读诊断与命令差异

- **WHEN** 用户或宿主调用 doctor，或锁定原生 CLI 命令目录发生变化
- **THEN** doctor SHALL 只读返回平台、缺失依赖、锁定与实际版本、运行模式、可执行恢复动作；命令清单 SHALL 记录参数契约、技能归属、运行模式、场景验收状态
- **AND** 新增或变化命令默认未验收，清单生成不得将其直接标记为可用于创作

#### Scenario: 显式只读原生能力探测

- **WHEN** 调用 doctor --probe-native，或同时提供 --compare-catalog 旧固定目录
- **THEN** doctor SHALL 先校验旧目录、平台条件及已有CLI完整性，仅对已验证可启动制品限时查询版本、tools/list及自有空headless会话的list_commands，不安装、不连接已有桌面、不编辑或渲染、不创建或恢复任务
- **AND** 默认doctor SHALL 不启动原生进程，报告锁定／实际Python版本、CLI完整性、固定目录和可执行恢复argv；所有恢复动作只报告、不自动执行。没有隔离Python时启动入口 SHALL 只读报告缺失，不自动下载
- **AND** 版本／注册身份／工具schema漂移 SHALL 报FAIL，无法完成探测 SHALL 报NOT_RUN；缺失／损坏安装或最低系统不满足 SHALL 不启动CLI。探测PASS仅表示只读发现符合固定合同，不提升创作、desktop、目标平台整体或宿主验收

#### Scenario: 单技能双启动入口与制品选择

- **WHEN** 用户在没有系统 Python 的受支持原生平台调用任一独立技能
- **THEN** 技能 SHALL 提供 POSIX Shell 与 PowerShell 启动入口，准备用户目录内隔离 Python 后执行同一 Python 工作流；Windows SHALL 使用 Python 官方嵌入式发行包，macOS/Linux SHALL 使用固定 python-build-standalone install_only 制品
- **AND** 平台锁 SHALL 选择各目标共同可用的受维护 Python 3.13 补丁版本并保存版本、平台、来源、摘要及许可；版本选择在构建时确定，运行时不得解析 latest

#### Scenario: 活动任务引用阻止运行时清理

- **WHEN** 安装升级或清理旧运行时，而已有活动任务引用旧运行时身份
- **THEN** 系统 SHALL 保留被引用的原版本，不切换该任务的解释器或 EffectCraft；新任务可以绑定已验证的新版本
- **AND** 升级失败 SHALL 保留旧版本及其任务记录，不通过回退破坏状态兼容性

#### Scenario: 任务执行资源在技能更新后保持原组合

- **WHEN** 已登记任务的技能安装目录发生更新，用户调用该任务的 resume、reconcile、review 或 revise
- **THEN** 系统 SHALL 核对任务私有执行资源清单、解释器身份和原生锁，派发到原解释器及原执行快照；修订子任务 SHALL 继承原执行组合和任务族预算
- **AND** 默认隔离 Python SHALL 核对整个锁定发行载荷；兼容的外部 Python 仅记录其可执行文件身份，不能被标记为已隔离
- **AND** Windows 控制器切换 SHALL 使用自有进程树守护和监督通道，不能因前端退出留下失去所有权的编辑进程

#### Scenario: 当前Python准备失败不阻止可信旧任务派发

- **WHEN** 用户恢复或评价已有任务，原隔离解释器及任务私有执行资源完好，但当前技能的Python尚未准备或准备失败
- **THEN** 启动入口 SHALL 在准备当前Python之前选择并验证该任务的原启动资源，使用原解释器和控制器，不以重新安装当前Python作为旧任务恢复的前置条件
- **AND** 原启动证据损坏、缺失或历史记录无法证明可执行时 SHALL 保留现场并拒绝自动续写，不重建旧快照、不回退到当前代码、不重放未知编辑

#### Scenario: 执行绑定损坏或历史记录缺失

- **WHEN** 绑定、清单、执行资源或解释器损坏/缺失，或者历史任务没有可信执行绑定
- **THEN** 系统 SHALL 在启动 worker 或迁移恢复状态前拒绝继续写入，保留任务、暂存区、预算和回执；不得使用当前技能重建旧快照或换任务身份重做
- **AND** 历史任务仍 SHALL 允许检查和诊断；未知工作身份不得因执行绑定变化而绕过原冲突检查

#### Scenario: 平台适配覆盖执行路径

- **WHEN** 原生任务使用文件锁、管道读取、进程树管理、安装路径或桌面启动
- **THEN** 执行器 SHALL 使用对应平台适配实现，不能以 Darwin 硬编码或 Windows 不支持的调用承担跨平台执行
- **AND** 平台分支单元测试不替代该目标机上的实际运行证据

#### Scenario: Windows默认代码页与中文合同

- **WHEN** 原生平台默认文本编码不是UTF-8，技能读取中文计划/合同或写出中文工程回执，或分发工具生成技能资源
- **THEN** 系统 SHALL 显式使用UTF-8读取和写入文本合同、计划及JSON回执，公共入口的重定向帮助/诊断输出 SHALL 使用UTF-8；分发生成文本 SHALL 使用确定性LF换行，不得依赖宿主默认代码页
- **AND** 管道按实际字节读取，二进制协议夹具 SHALL 跨平台输出相同字节，不通过放宽断言掩盖协议或编码失败
