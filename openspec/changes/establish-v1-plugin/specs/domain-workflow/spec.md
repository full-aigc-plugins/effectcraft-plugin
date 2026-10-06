# EffectCraft — domain-workflow

## Purpose

本能力定义 EffectCraft 在 domain-workflow 范围内对用户、宿主与下游系统承诺的可观察行为、失败语义和验收证据，确保规划、执行与实际交付之间保持可验证的边界。当前为目标规范，尚未实现。

## ADDED Requirements

### Requirement: EC-DM-001 合成建立与范围

EffectCraft SHALL 显式设置合成尺寸、帧率、时长与工作区；输入的时间单位必须转换为适配器确认的上游单位。

#### Scenario: EC-DM-001-P 正常交付

- **WHEN** 输入素材、运行时能力、授权与工程版本有效，用户请求合成建立与范围
- **THEN** 显式设置合成尺寸、帧率、时长与工作区；输入的时间单位必须转换为适配器确认的上游单位。
- **AND** 输出可检查的操作结果、工程版本和验收证据

#### Scenario: EC-DM-001-N 异常或不保真

- **WHEN** 合成帧率或时长与计划不符
- **THEN** 拒绝渲染并报告配置差异

### Requirement: EC-DM-002 图层与素材依赖

EffectCraft SHALL 维护图层 ID、顺序、父子关系、素材引用和可见性；检查循环父子关系与丢失素材。

#### Scenario: EC-DM-002-P 正常交付

- **WHEN** 输入素材、运行时能力、授权与工程版本有效，用户请求图层与素材依赖
- **THEN** 维护图层 ID、顺序、父子关系、素材引用和可见性；检查循环父子关系与丢失素材。
- **AND** 输出可检查的操作结果、工程版本和验收证据

#### Scenario: EC-DM-002-N 异常或不保真

- **WHEN** 图层父子关系形成循环或素材缺失
- **THEN** 拒绝变更且保留修改前工程

### Requirement: EC-DM-003 文字图形与关键帧

EffectCraft SHALL 以属性路径记录文字、变换与关键帧，保留插值方式；修改文字不得重建无关动画。

#### Scenario: EC-DM-003-P 正常交付

- **WHEN** 输入素材、运行时能力、授权与工程版本有效，用户请求文字图形与关键帧
- **THEN** 以属性路径记录文字、变换与关键帧，保留插值方式；修改文字不得重建无关动画。
- **AND** 输出可检查的操作结果、工程版本和验收证据

#### Scenario: EC-DM-003-N 异常或不保真

- **WHEN** 修改文字导致其他关键帧曲线变化
- **THEN** 局部修改验收失败并提供差异

### Requirement: EC-DM-004 效果与蒙版能力匹配

EffectCraft SHALL 在应用效果或蒙版前发现命令与参数 schema；不支持的参数在执行前返回明确错误，禁止静默跳过。

#### Scenario: EC-DM-004-P 正常交付

- **WHEN** 输入素材、运行时能力、授权与工程版本有效，用户请求效果与蒙版能力匹配
- **THEN** 在应用效果或蒙版前发现命令与参数 schema；不支持的参数在执行前返回明确错误，禁止静默跳过。
- **AND** 输出可检查的操作结果、工程版本和验收证据

#### Scenario: EC-DM-004-N 异常或不保真

- **WHEN** 请求不存在的效果参数
- **THEN** 返回 unsupported_mapping，不静默省略

#### Scenario: EC-DM-004-PREFLIGHT 后续非法字段不执行先前编辑

- **WHEN** 整份计划中的后续效果或蒙版命令含未知顶层字段或缺少固定反射必填字段
- **THEN** 工作流在读取源工程、安装运行时及任何原生编辑之前返回 unsupported_mapping，不发布交付目录
- **AND** 有效计划在打开或创建工程之前只读核对实际命令 schema；固定制品身份或 schema 不匹配时拒绝继续
- **AND** 字段内的对象引用在原生会话中解析，字段预检不替代效果名、属性路径、数值和实际渲染验收

### Requirement: EC-DM-005 透明与色彩交接

EffectCraft SHALL 交接时记录颜色空间、位深、alpha 类型与编码；透明背景必须通过实际像素或通道检查验证。

#### Scenario: EC-DM-005-P 正常交付

- **WHEN** 输入素材、运行时能力、授权与工程版本有效，用户请求透明与色彩交接
- **THEN** 交接时记录颜色空间、位深、alpha 类型与编码；透明背景必须通过实际像素或通道检查验证。
- **AND** 输出可检查的操作结果、工程版本和验收证据

#### Scenario: EC-DM-005-N 异常或不保真

- **WHEN** 要求透明但渲染输出为不透明 RGB
- **THEN** alpha 门禁失败，不提交为可合成素材

#### Scenario: EC-DM-005-SEQUENCE 动态透明 PNG 序列

- **WHEN** 用户通过公开技能请求 `png-sequence` 导出
- **THEN** 固定原生 CLI SHALL 导出完整 RGBA PNG 序列，逐帧验证连续编号、尺寸、8 位通道、实际 alpha 样本及摘要，交付 `craft-image-sequence/v1` 清单、帧率、持续时间和原生工程
- **AND** 缺帧、额外帧、非 RGBA、尺寸冲突或全不透明帧不得发布为透明序列；颜色空间未核实时 SHALL 标记未知，不推断跨软件保真

### Requirement: EC-DM-006 工程与渲染交付

EffectCraft SHALL 重开 .ecproj 验证图层与关键帧；渲染结果与源合成版本绑定；向下游提供素材化损失说明。

#### Scenario: EC-DM-006-P 正常交付

- **WHEN** 输入素材、运行时能力、授权与工程版本有效，用户请求工程与渲染交付
- **THEN** 重开 .ecproj 验证图层与关键帧；渲染结果与源合成版本绑定；向下游提供素材化损失说明。
- **AND** 输出可检查的操作结果、工程版本和验收证据

#### Scenario: EC-DM-006-N 异常或不保真

- **WHEN** 导出文件存在但无法重开原生工程
- **THEN** 交付不完整，不能标为 completed

#### Scenario: EC-DM-004-PARAM 未知效果与蒙版参数的领域错误

- **WHEN** 原生工作流中的 effect.apply、effect.remove、effect.toggle、mask.new、mask.setVertex 或 mask.remove 被固定引擎的命令参数校验拒绝
- **THEN** 返回 unsupported_mapping，保留命令 ID 与原生参数诊断，不把字段静默删除再重试
- **AND** 新工程的暂存结果不作为成功交付；源工程修订失败时保留原交付的全部文件
- **AND** 普通渲染、素材和运行时故障保留原错误分类，不误报为参数映射失败
