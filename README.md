# EffectCraft Agent Plugin

Independent-skills-driven Layer compositing, motion graphics and shot effects.

[English](README.md) | [简体中文](README.zh-CN.md)

> Implementation is in progress. This is not yet a validated, installable plugin release; independent skills and runtime integration are being developed.

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
| Metadata version | 0.1.0-dev.0 |
| Stage | implementation-in-progress |
| Skills source | effectcraft-skills / v0.1.0-dev.0 |
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

Independent skills are now pinned at the published development tag `v0.1.0-dev.0`, including the exact source commit and whole-skill digest in `skills.lock.json`. Verify using `python3 scripts/vendor/skill_vendor.py check`. These source snapshots do not establish plugin-host acceptance or production readiness.

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
