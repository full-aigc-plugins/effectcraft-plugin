# EffectCraft Agent Plugin

Independent-skills-driven Layer compositing, motion graphics and shot effects.

[English](README.md) | [简体中文](README.zh-CN.md)

## Current release and reproducible host checks

Previously verified plugin/skill suite: `0.1.0-dev.3`. Codex 0.147.0 and 0.153.4 installed five fixed public releases and discovered all 58 enabled namespaced skills with zero loading errors and matching source digests. Five representative workflows passed through installed task-skill entrypoints on 0.147.0, including native projects, targeted revisions and the mixed ArtCraft online workflow. Model dispatch, desktop GUI, final creative review and full interchange fidelity remain unverified.

[Host verification design](docs/EffectCraft-Host-Verification-Architecture.md) · [Version-bound evidence](docs/evidence/codex-skill-suite.json). Historical milestones below retain their original scope; the current manifest and locks own version identity.

> Development release. Fixed-tag Codex installation, skill discovery and representative native workflows have passed; complete product and creative acceptance remain open.

Implementation has started in the independent skills package. The isolated first-use installer is tested on macOS arm64; complete creative workflows and plugin host acceptance remain pending. [Evidence](docs/evidence/bootstrap-tests.json)

## Positioning

Create a brand intro with a reopenable .ecproj and editor-ready render; edit its text while preserving other layers and animation curves.

For creators who need editable native projects, repeatable revisions and reliable automation.

## At a glance

```text
Intent + assets
  -> independent Skills (pinned development release)
  -> public skill workflow / ArtCraft adapter
  -> verified runtime / child adapter
  -> native project + preview + export + evidence
```
| Property | Value |
| :--- | :--- |
| Plugin ID | effectcraft |
| Metadata version | 0.1.0-dev.8 |
| Stage | implementation-in-progress |
| Skills source | effectcraft-skills / v0.1.0-dev.7 |
| Execution | Upstream CLI; ArtCraft uses child adapters |
| Host compatibility | Codex development install/discovery pass; GUI and other hosts pending |
| License | Apache-2.0 (original repository content) |


## Capabilities and boundaries

| Capability | Behavioral boundary | Status |
| :--- | :--- | :--- |
| Composition creation and range | Set composition dimensions, frame rate, duration and work area explicitly; convert time units according to the verified adapter contract. | Full scope pending |
| Layers and footage dependencies | Maintain layer IDs, order, parenting, footage references and visibility; detect parent cycles and missing footage. | Full scope pending |
| Text, graphics and keyframes | Record text, transforms and keyframes by property path with interpolation; text revision must not rebuild unrelated animation. | Full scope pending |
| Effect and mask capability matching | Discover commands and parameter schemas before applying effects or masks; reject unsupported parameters before execution rather than skipping them. | Full scope pending |
| Alpha and color handoff | Record color space, bit depth, alpha interpretation and codec during handoff; verify transparency from actual pixels or channel inspection. | Full scope pending |
| Project and render delivery | Reopen .ecproj to inspect layers and keyframes; bind renders to composition revisions and report editability lost by baking. | Full scope pending |

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

The recorded result is 0.2.0. The public independent-skill bootstrap and workflow are development entry points; plugin-host installation remains unverified.

## Configuration and runtime

Target configuration includes CLI paths, allowed read/write roots, execution mode, budget, timeout and output directory; the configuration schema is not implemented yet. The skills lock pins the published source commit and whole-skill digest. Runtime lock hashes identify real official artifacts and establish only the recorded platform’s smoke evidence.

## Reliability and security

Planned safeguards include project write locks, revision preconditions, persisted intent, idempotency keys, outcome reconciliation, native checkpoints, artifact hashes and bounded revisions. Secrets are host-managed references; asset metadata is never an execution instruction.

## Verification and maturity

[Sanitized CLI evidence](docs/evidence/runtime-baseline.json)

| Layer | Status |
| :--- | :--- |
| Upstream CLI and read-only MCP | Observed on macOS arm64 only |
| Independent skill and adapter | Technical workflow tested; full harness pending |
| Native project and creative acceptance | Native technical cases pass; creative acceptance pending |
| Target host installation | Codex controlled install/discovery pass; full host acceptance pending |


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

Independent skill workflow acceptance now covers native `.ecproj` round trips, transparent PNG previews, decoded H.264 export and text-only revisions preserving unrelated layers and keyframes (14 tests). See [test evidence](docs/evidence/effect-workflow-tests.json). Full Harness, external dependency packaging and plugin-host acceptance remain pending.

External PNG media collection and footprint-preserving replacement are now tested in the 15-test suite. See [current evidence](docs/evidence/effect-assets-tests.json). Full domain and host acceptance remain in progress.

Independent skills are pinned at the current published development tag `v0.1.0-dev.6`, including the exact source commit and whole-skill digest in `skills.lock.json`. Verify using `python3 scripts/vendor/skill_vendor.py check`. These source snapshots do not establish plugin-host acceptance or production readiness.

## Development skill installation and use

Install the independent skill: `npx skills add full-aigc-skills/effectcraft-skills --skill effectcraft-use`. Run the public entry from the actual installed directory; this plugin snapshot includes the same skill.

```bash
python3 -I -B skills/effectcraft-use/scripts/bootstrap.py
python3 -I -B skills/effectcraft-use/scripts/workflow.py --help
```

First use installs the pinned official CLI into user-level storage. Requires macOS arm64 and Python 3.11+. Use the bundled example with real input assets; consult SKILL.md for delivery and revision contracts. [Source verification](docs/evidence/skill-publication.json).

## Codex development host checks

All five plugins installed from public tags into an isolated Codex configuration. App-server discovered their namespaced skills without loading errors; the installed ArtCraft entry produced four native projects. [Host evidence](docs/evidence/codex-installation.json). These controlled development checks do not establish desktop GUI, other hosts, complete creative or production marketplace acceptance.

Development version `0.1.0-dev.1` pins the independent skill install-lock fix: bounded coordination for concurrent installation/reuse, with no native task replay.

The current development milestone adds hash-bound exchange loss reports to native deliveries. lost, observed and unknown are separate; derivatives never substitute for native projects. Font/effect/mask fidelity across editors remains unverified, so full exchange acceptance stays open.

## CLI and task skill suite

The source suite contains 13 independently installable skills with setup, public CLI operations and focused tasks. [Architecture and catalogue](docs/EffectCraft-Skill-Suite-Architecture.md). Runtime and plugin versions are separate; prior host evidence retains its original version scope.

Current plugin: `0.1.0-dev.7`; skill suite: `0.1.0-dev.6`. The corrected examples resolve scripts from the actual host-loaded `SKILL.md` directory. All skills passed isolated entry-point checks in user, project and plugin layouts with spaces. [Path evidence](docs/evidence/installed-skill-paths.json). Earlier host evidence above covers its recorded release; existing installations require an update.

Plugin `0.1.0-dev.6` corrects the whole-skill digests by fetching the immutable public source tag, without local Python caches. Plugin tag `v0.1.0-dev.5` is superseded and must not be installed because its source digests included ignored development caches.

Plugin dev.7 pins source dev.6 with a self-contained editable-mask example and vertex-edit/removal workflow. All 39 default-public-download native tests pass with zero skipped; isolated task skills install into empty runtimes, preserve old projects and non-target layers, and verify actual RGBA mask boundaries. [Evidence](docs/evidence/task-skill-first-use.json). Native runtime remains 0.2.0; model/GUI and full creative acceptance remain open.

Current fixed-release host verification (2026-10-06): Codex 0.153.4 installs all five current pinned plugins and discovers all 58 skills with exact content identity. This plugin’s representative native workflow runs from its actual installed skill path with a fresh native runtime; creation/reopening/revision checks pass. All 58 installed skill hashes remain unchanged after the five workflows. [Evidence](docs/evidence/codex-current-release-20261006.json). The shared explicit-tag generator belongs to ArtCraft. Model dispatch awaits authorization; GUI, creative and full host acceptance remain pending. This QA maintenance changes no published skill/runtime content or release tags.

The fixed FilmCraft dev.6 / EffectCraft dev.7 / PhotoCraft dev.6 / VectorCraft dev.6 / ArtCraft dev.17 matrix passed isolated Codex discovery of 58 skills and representative native workflows from installed skill content. All installed skill digests remained unchanged afterward. [Installed native evidence](docs/evidence/codex-release17-native-20261006.json). Model dispatch, GUI and complete creative acceptance remain unverified.

[Actual transparent handoff to FilmCraft](docs/EffectCraft-FilmCraft-Alpha-Handoff-Acceptance.md): one installed two-domain empty-runtime case passes, preserving title animation and compositing native RGBA PNG over a video background with verified foreground pixels. Complete color and animated-alpha-video acceptance remain open.

Installed animation skill temporal first use passed: four native RGBA samples and 12 decoded video frames verify the fade; text revision preserves badge/key properties and original deliveries. [Temporal acceptance](docs/EffectCraft-Temporal-Animation-Acceptance.md). Full creative/animated-alpha acceptance remains open.

Independent EffectCraft source dev.7 maps matching effect/mask plan parameter validation errors to unsupported_mapping; native CLI stays at 0.2.0. Actual rejected creation/revision preserves prior deliveries. All 44 native source tests pass with zero skips (182.083s); fixed new plugin installed acceptance is recorded below. [Architecture](docs/EffectCraft-Parameter-Errors-Architecture.md) · [Evidence](docs/evidence/parameter-mapping-repair-20261006.json).

Fixed plugin dev.8 / source dev.7 installed acceptance passes: five plugins, 58 skills, zero loading errors; independent effect/mask skills cold-verify valid creation and rejected new/revision parameters (1 test, 24.714s), all 13 skills pass separate public cold first use, and all host-installed hashes remain intact. [Release-bound evidence](docs/evidence/codex-effectcraft8-parameter-first-use-20261006.json). ArtCraft bundle/domain-code propagation and complete V1 gates remain open.

Dynamic RGBA PNG sequences now retain the editable ecproj and every frame digest, rate and duration in craft-image-sequence/v1. Source cold installation and actual Film handoff pass; fixed new-plugin and Art acceptance remain pending. [Architecture](docs/EffectCraft-Dynamic-Sequence-Architecture.md).
