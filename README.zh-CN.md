# EffectCraft Agent Plugin

独立技能驱动的图层合成、动态图形与镜头效果.

[English](README.md) | [简体中文](README.zh-CN.md)

> 当前是文档与 OpenSpec 规格基线，不是可安装的功能版本。插件功能和技能包尚未实现或发布。

## 定位

制作品牌片头，交付可重开的 .ecproj 与可导入剪辑工程的渲染结果；修改文字后保持其他图层和动画曲线不变。

面向需要原生可编辑工程、反复修改和可靠自动化的创作者。

## 一眼了解

```text
Intent + assets
  -> independent Skills (planned)
  -> plugin Harness (planned)
  -> verified runtime / child adapter
  -> native project + preview + export + evidence
```
| Property | Value |
| :--- | :--- |
| Plugin ID | effectcraft |
| Metadata version | 0.1.0-dev.0 |
| Stage | documentation-baseline |
| Skills source | effectcraft-skills (planned) |
| Execution | 上游 CLI；ArtCraft 使用子适配器 |
| Host compatibility | NOT_RUN |
| License | Apache-2.0 (original repository content) |


## 能力与边界

| 能力 | 行为边界 | 状态 |
| :--- | :--- | :--- |
| 合成建立与范围 | 显式设置合成尺寸、帧率、时长与工作区；输入的时间单位必须转换为适配器确认的上游单位。 | 计划中 |
| 图层与素材依赖 | 维护图层 ID、顺序、父子关系、素材引用和可见性；检查循环父子关系与丢失素材。 | 计划中 |
| 文字图形与关键帧 | 以属性路径记录文字、变换与关键帧，保留插值方式；修改文字不得重建无关动画。 | 计划中 |
| 效果与蒙版能力匹配 | 在应用效果或蒙版前发现命令与参数 schema；不支持的参数在执行前返回明确错误，禁止静默跳过。 | 计划中 |
| 透明与色彩交接 | 交接时记录颜色空间、位深、alpha 类型与编码；透明背景必须通过实际像素或通道检查验证。 | 计划中 |
| 工程与渲染交付 | 重开 .ecproj 验证图层与关键帧；渲染结果与源合成版本绑定；向下游提供素材化损失说明。 | 计划中 |

不重写上游编辑引擎，不暗中改变原生交付格式，不宣称 GUI 或跨平台验收完成。

## 架构与文档

- [完整运行时架构](docs/EffectCraft-Runtime-Architecture.zh_CN.md)
- [技术方案与路线](product-docs/EffectCraft/5%E3%80%81EffectCraft-%E6%8A%80%E6%9C%AF%E6%96%B9%E6%A1%88%E4%B8%8E%E8%B7%AF%E7%BA%BF.md)
- [V1 PRD 与需求映射](product-docs/EffectCraft/V1/5%E3%80%81EffectCraft-PRD%E6%96%87%E6%A1%A3-V1.md)
- [完整文档导航](docs/README.zh-CN.md)
- [OpenSpec proposal](openspec/changes/establish-v1-plugin/proposal.md)
- [OpenSpec tasks](openspec/changes/establish-v1-plugin/tasks.md)

- [专业领域技术设计](docs/EffectCraft-Domain-Design.zh_CN.md)

## 当前可执行的快速开始

```bash
python3 scripts/validate_docs.py
openspec validate establish-v1-plugin --strict --no-interactive
```
以上校验文档和规范，不运行产品工作流。OpenSpec 校验使用 1.13.1；本仓不自动安装工具。

已安装官方 CLI 后可执行基础检查：

```bash
effectcraft-cli --version
```

本次记录结果为 0.2.0。插件 setup、技能安装命令与宿主安装说明将在对应任务完成后发布，当前不提供虚构的安装入口。

## 配置与运行时

目标配置包含 CLI 路径、允许读写根目录、运行模式、预算、超时与输出目录；配置 schema 尚待实现。技能锁文件 sources 为空，避免误报技能已发布。运行时锁文件中的摘要来自真实官方制品，只证明已记录平台的基础运行。

## 可靠性与安全

规划要求：单工程写入锁、版本前置条件、持久化意图、幂等键、不明确结果核对、原生工程检查点、产物摘要及受限修订。密钥只通过宿主秘密引用传递；素材元数据不作为执行指令。

## 验证与成熟度

[脱敏 CLI 证据](docs/evidence/runtime-baseline.json)

| 层面 | 状态 |
| :--- | :--- |
| 上游 CLI 与只读 MCP | 已观察，仅 macOS arm64 |
| 业务技能与插件 Harness | PLANNED |
| 原生工程与创作验收 | NOT_RUN |
| 目标宿主安装 | NOT_RUN |


## 路线与贡献

| 阶段 | 交付 | 进入下一阶段条件 |
| :--- | :--- | :--- |
| D0 | 双语文档与 OpenSpec 基线 | 文档、链接、规范校验通过；实现任务仍未完成 |
| M1 | 独立技能与运行时适配 | 清洁环境安装、摘要检查、真实 MCP 调用 |
| M2 | 专业领域完整闭环 | 代表任务、工程重开、输出解码、局部修改 |
| M3 | ArtCraft 跨插件协作 | 版本传播、局部失效、断线恢复与幂等 |
| M4 | 宿主与发布验收 | 宿主实装与多平台证据；市场清单一致 |

先更新 OpenSpec 再实现行为；每项任务通过实际验收后才能勾选。中英文文档同时维护。参考 CONTRIBUTING.md 与 AGENTS.md。

## 许可与上游

原创内容遵循 [Apache-2.0](LICENSE)。这是第三方集成规划，不代表上游背书。四款应用的代码许可与 ArtCraft/Services 的受限许可分别处理；不复制上游 ArtCraft/Services 代码或品牌资产。

[Upstream EffectCraft](https://github.com/storytold/effectcraft) · [Issues](https://github.com/full-aigc-plugins/effectcraft-plugin/issues)
