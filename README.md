# EffectCraft Agent Plugin

Independent-skills-driven Layer compositing, motion graphics and shot effects.

[English](README.md) | [简体中文](README.zh-CN.md)

> This is a documentation and OpenSpec baseline, not an installable functional release. Plugin behavior and skill packages are not implemented or published.

## Positioning

Create a brand intro with a reopenable .ecproj and editor-ready render; edit its text while preserving other layers and animation curves.

For creators who need editable native projects, repeatable revisions and reliable automation.

## At a glance

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
| Execution | Upstream CLI; ArtCraft uses child adapters |
| Host compatibility | NOT_RUN |
| License | Apache-2.0 (original repository content) |


## Capabilities and boundaries

| Capability | Behavioral boundary | Status |
| :--- | :--- | :--- |
| Composition creation and range | Set composition dimensions, frame rate, duration and work area explicitly; convert time units according to the verified adapter contract. | Planned |
| Layers and footage dependencies | Maintain layer IDs, order, parenting, footage references and visibility; detect parent cycles and missing footage. | Planned |
| Text, graphics and keyframes | Record text, transforms and keyframes by property path with interpolation; text revision must not rebuild unrelated animation. | Planned |
| Effect and mask capability matching | Discover commands and parameter schemas before applying effects or masks; reject unsupported parameters before execution rather than skipping them. | Planned |
| Alpha and color handoff | Record color space, bit depth, alpha interpretation and codec during handoff; verify transparency from actual pixels or channel inspection. | Planned |
| Project and render delivery | Reopen .ecproj to inspect layers and keyframes; bind renders to composition revisions and report editability lost by baking. | Planned |

Does not rewrite upstream editors, silently change native deliverable formats, or claim GUI/cross-platform acceptance.

## Architecture and documentation

- [Complete runtime architecture](docs/EffectCraft-Runtime-Architecture.md)
- [Technical plan and roadmap](product-docs/EffectCraft/en/5%E3%80%81EffectCraft-Technical-Plan.md)
- [V1 PRD and requirement mapping](product-docs/EffectCraft/en/V1/5%E3%80%81EffectCraft-PRD-V1.md)
- [Complete documentation index](docs/README.md)
- [OpenSpec proposal](openspec/changes/establish-v1-plugin/proposal.md)
- [OpenSpec tasks](openspec/changes/establish-v1-plugin/tasks.md)

- [Domain technical design](docs/EffectCraft-Domain-Design.md)

## Currently executable quick start

```bash
python3 scripts/validate_docs.py
openspec validate establish-v1-plugin --strict --no-interactive
```
These commands validate documentation and specifications, not product workflows. OpenSpec validation uses 1.13.1; this repository does not install tools automatically.

After installing the official CLI, perform a basic check:

```bash
effectcraft-cli --version
```

The recorded result is 0.2.0. Plugin setup, skill installation and host installation instructions will ship after their tasks are implemented; no fictional installation command is provided now.

## Configuration and runtime

Target configuration includes CLI paths, allowed read/write roots, execution mode, budget, timeout and output directory; the configuration schema is not implemented yet. Empty skill-lock sources avoid claiming unpublished skills. Runtime lock hashes identify real official artifacts and establish only the recorded platform’s smoke evidence.

## Reliability and security

Planned safeguards include project write locks, revision preconditions, persisted intent, idempotency keys, outcome reconciliation, native checkpoints, artifact hashes and bounded revisions. Secrets are host-managed references; asset metadata is never an execution instruction.

## Verification and maturity

[Sanitized CLI evidence](docs/evidence/runtime-baseline.json)

| Layer | Status |
| :--- | :--- |
| Upstream CLI and read-only MCP | Observed on macOS arm64 only |
| Business skills and plugin harness | PLANNED |
| Native project and creative acceptance | NOT_RUN |
| Target host installation | NOT_RUN |


## Roadmap and contribution

| Phase | Deliverable | Exit evidence |
| :--- | :--- | :--- |
| D0 | Bilingual documentation and OpenSpec baseline | Document, link and spec validation; implementation tasks remain open |
| M1 | Independent skills and runtime adapter | Clean installation, checksums and real MCP invocation |
| M2 | Complete domain workflow | Representative task, native reopen, decode and targeted revision |
| M3 | ArtCraft cross-plugin collaboration | Version propagation, selective invalidation and interruption recovery |
| M4 | Host and release acceptance | Actual host installation, platform evidence and synchronized catalogs |

Change OpenSpec before behavior; check a task only after actual acceptance. Maintain both document languages. See CONTRIBUTING.md and AGENTS.md.

## License and upstream

Original content uses [Apache-2.0](LICENSE). This is a third-party integration design, not upstream endorsement. Treat the four apps’ code licenses separately from ArtCraft/Services restrictions; do not copy restricted source or brand assets.

[Upstream EffectCraft](https://github.com/storytold/effectcraft) · [Issues](https://github.com/full-aigc-plugins/effectcraft-plugin/issues)
