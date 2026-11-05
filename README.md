# block-review-rules

Code review rules for Platforma block repos. One file, `.reviewrules/config.json`, serves two reviewers:

- **reviewrules** reads it as a [rule set](https://github.com/popoffvg/reviewrules/blob/main/docs/rule-sets.md).
- **Greptile** reads the same format from `.greptile/config.json` in a block repo.

The rules come from the `block-dev-v2` skill of the mictx pack. Each rule cites the reference line it comes from in the [table below](#rules).

## Use with reviewrules

Add two files to the block repo.

`.reviewrules/settings.toml`:

```toml
[[rule_sets]]
name = "block"
url = "https://github.com/vgpopov/block-review-rules.git"
ref = "v0.1.0"
```

`.reviewrules/config.json`: reviewrules needs a root rule folder in the reviewed repo, and only the repo decides which files the diff ignores. Use it for the ignore patterns of a block and any rules of that one block:

```json
{
  "ignorePatterns": "**/node_modules/**\n**/dist/**\n**/block-pack/**\n**/.turbo/**\n**/*.tgz\n**/CHANGELOG.md\nsoftware/**/uv.lock\nsoftware/**/src/requirements.txt\nsoftware/**/tests/data/corpus/**\ntest/fixtures/data/**\n**/__pycache__/**",
  "rules": []
}
```

Rule ids get the prefix `block/`, for example `block/producer-identity-in-domain`.

## Use with Greptile

Greptile reads rules only from a `.greptile/` folder in the reviewed repo. Copy the file:

```bash
mkdir -p .greptile && cp <path-to>/block-review-rules/.reviewrules/config.json .greptile/config.json
```

The file already holds `strictness`, `commentTypes`, `instructions`, and `ignorePatterns` for a block repo. Copy it again when the rules change; Greptile has no link to this repo.

## Change a rule

1. Edit `.reviewrules/config.json`. Keep `id`, `rule`, `scope`, and `severity`; scope globs are paths of the block repo.
2. Write a rule that a reviewer can decide from the diff with a yes or a no.
3. Tag a release (`v0.2.0`) and move `ref` in the block repos when they are ready.

## Conflicts in the source references

The rules follow one side of each conflict below. Fix the reference, then the rule.

| Topic | One side | Other side | Rule follows |
|---|---|---|---|
| Producer identity in `domain` | `p-columns.md:144` suggests `{"pl7.app/block": wf.blockId()}` as an axis domain | `p-columns.md:63-65` puts producer identity in `contextDomain` | `p-columns.md:63-65` |
| Version bumps | `build-and-release.md:190` says bump the software version by hand | `build-and-release.md:82` forbids a manual bump | `build-and-release.md:82` |
| package.json scripts | `build-and-release.md:82` allows edits to scripts | `structure.md` says the structurer manages build and test scripts | `structure.md` |

## Rules

74 rules: 40 high, 28 medium, 6 low. The source is a file in `block-dev-v2/references/` (or the skill's `SKILL.md`) with its line.

| Id | Severity | Scope | Source |
|---|---|---|---|
| `map-iteration-unsorted-in-template` | high | `workflow/src/**/*.tengo`, `software/**/*.tengo` | `workflow.md:32` |
| `array-loop-with-blank-index` | low | `workflow/src/**/*.tengo`, `software/**/*.tengo` | `workflow.md:41` |
| `hash-override-on-semantic-change` | high | `workflow/src/**/*.tpl.tengo`, `software/**/*.tpl.tengo` | `workflow.md:53` |
| `side-effect-in-pure-template` | high | `workflow/src/**/*.tpl.tengo`, `software/**/*.tpl.tengo` | `workflow.md:188` |
| `ephemeral-template-without-reason` | medium | `workflow/src/**/*.tengo`, `software/**/*.tengo` | `workflow.md:26` |
| `inline-branch-on-unrun-output` | high | `workflow/src/**/*.tengo`, `software/**/*.tengo` | `workflow.md:69` |
| `prepare-awaits-unneeded-data` | medium | `workflow/src/**/*.tengo` | `workflow.md:95` |
| `savefile-getfilecontent-mismatch` | high | `workflow/src/**/*.tengo`, `software/**/*.tengo`, `model/src/**/*.ts` | `workflow.md:162` |
| `nested-json-encode-without-string-cast` | high | `workflow/src/**/*.tengo`, `software/**/*.tengo` | `workflow.md:166` |
| `producer-identity-in-domain` | high | `workflow/src/**/*.tengo`, `model/src/**/*.ts` | `p-columns.md:63` |
| `presentation-hint-in-domain` | medium | `workflow/src/**/*.tengo`, `model/src/**/*.ts` | `p-columns.md:146` |
| `new-axis-duplicates-existing` | high | `workflow/src/**/*.tengo` | `p-columns.md:149` |
| `inputs-by-spec-not-block-id` | high | `model/src/**/*.ts`, `workflow/src/**/*.tengo` | `p-columns.md:164; model.md:298; block-anatomy.md:63` |
| `trace-built-by-hand` | medium | `workflow/src/**/*.tengo` | `trace-and-provenance.md:15` |
| `derived-column-without-trace` | medium | `workflow/src/**/*.tengo` | `trace-and-provenance.md:33` |
| `annotation-value-invalid` | low | `workflow/src/**/*.tengo`, `model/src/**/*.ts` | `p-columns.md:99` |
| `anchored-id-stored-without-plref` | high | `model/src/**/*.ts`, `ui/src/**/*.{ts,vue}` | `anchors-and-discovery.md:86` |
| `frame-local-id-persisted-or-recast` | medium | `model/src/**/*.ts`, `ui/src/**/*.{ts,vue}` | `anchors-and-discovery.md:73` |
| `selector-partial-axes-implicit` | medium | `model/src/**/*.ts`, `ui/src/**/*.{ts,vue}` | `anchors-and-discovery.md:125` |
| `manual-loop-instead-of-process-column` | high | `workflow/src/**/*.tengo` | `process-column.md:20` |
| `exec-formula-without-fallback-or-bounds` | high | `workflow/src/**/*.tengo`, `software/**/*.tengo` | `exec-and-resources.md:111` |
| `gpu-request-shape` | medium | `workflow/src/**/*.tengo`, `software/**/*.tengo` | `exec-and-resources.md:124` |
| `software-ref-not-literal-import` | medium | `workflow/src/**/*.tengo`, `software/**/*.tengo` | `software-and-envs.md:104` |
| `cli-args-and-parser-drift` | high | `workflow/src/**/*.tengo`, `software/**/*.tengo`, `software/**/*.py`, `software/**/*.R` | `software-and-envs.md:115; failure-triage.md:84` |
| `hand-written-python-dockerfile` | medium | `software/**/package.json`, `software/**/Dockerfile` | `software-and-envs.md:88` |
| `ui-no-output-to-data-watcher` | high | `ui/src/**/*.vue`, `ui/src/**/*.ts` | `hairpin.md:69` |
| `args-lambda-reads-only-data` | high | `model/src/**/*.ts` | `model.md:45` |
| `blockdata-new-field-needs-migrate-step` | high | `model/src/**/*.ts` | `model.md:130` |
| `migration-version-keys-unique` | high | `model/src/**/*.ts` | `model.md:139` |
| `shipped-migrations-unchanged` | high | `model/src/**/*.ts` | `model-v1-to-v3-migration.md:154` |
| `v1-to-v3-needs-upgrade-legacy` | high | `model/src/**/*.ts` | `model-v1-to-v3-migration.md:79` |
| `no-mixed-v1-v3-model-api` | high | `model/src/**/*.ts`, `ui/src/**/*.ts`, `ui/src/**/*.vue` | `model-v1-to-v3-migration.md:125` |
| `model-derivations-pure` | high | `model/src/**/*.ts` | `model.md:297` |
| `output-key-matches-workflow` | high | `model/src/**/*.ts`, `workflow/src/**/*.tengo` | `model.md:289` |
| `plain-objects-in-block-data` | high | `model/src/**/*.ts`, `ui/src/**/*.vue`, `ui/src/**/*.ts` | `model.md:303` |
| `args-projects-not-identity` | medium | `model/src/**/*.ts` | `model.md:302` |
| `plref-key-canonicalized` | medium | `model/src/**/*.ts`, `ui/src/**/*.vue`, `ui/src/**/*.ts` | `model.md:83` |
| `upload-output-is-active` | high | `model/src/**/*.ts` | `upload-flow.md:101` |
| `is-active-only-for-side-effects` | medium | `model/src/**/*.ts` | `lifecycle-and-flags.md:184` |
| `import-progress-in-dedicated-output` | low | `model/src/**/*.ts` | `upload-flow.md:102` |
| `uploads-through-desktop-flow` | medium | `ui/src/**/*.vue`, `ui/src/**/*.ts`, `model/src/**/*.ts` | `upload-flow.md:105` |
| `with-status-only-when-consumed` | low | `model/src/**/*.ts` | `lifecycle-and-flags.md:182` |
| `pl-ag-data-table-no-v-if` | medium | `ui/src/**/*.vue` | `ui.md:157` |
| `guard-undefined-outputs` | medium | `ui/src/**/*.vue`, `ui/src/**/*.ts`, `model/src/**/*.ts` | `ui.md:151` |
| `no-v-model-on-outputs` | medium | `ui/src/**/*.vue`, `ui/src/**/*.ts` | `ui.md:167` |
| `no-derived-state-in-data` | medium | `ui/src/**/*.vue`, `ui/src/**/*.ts`, `model/src/**/*.ts` | `ui.md:168` |
| `sdk-components-not-custom` | medium | `ui/src/**/*.vue` | `ui.md:13` |
| `required-fields-outside-accordion` | medium | `ui/src/**/*.vue` | `ui.md:110` |
| `collapsed-field-has-default` | high | `ui/src/**/*.vue`, `model/src/**/*.ts` | `ui.md:123` |
| `import-wasm-literal-arg` | high | `workflow/src/**/*.tengo` | `wasm.md:68` |
| `v3-migration-changeset-minor` | low | `.changeset/*.md` | `model-v1-to-v3-migration.md:135` |
| `no-hand-edit-generated-config` | high | `**/tsconfig*.json`, `**/.oxlintrc.json`, `**/.oxfmtrc.json`, `.vscode/settings.json`, `turbo.json`, `pnpm-workspace.yaml`, `ui/index.html`, `block/src/AGENTS.ts`, `.github/workflows/**` | `SKILL.md:126` |
| `no-hand-edit-structurer-package-json-fields` | high | `**/package.json` | `structure.md:33` |
| `no-manual-version-bump` | medium | `**/package.json` | `build-and-release.md:82` |
| `changeset-required` | high | `workflow/**`, `model/**`, `ui/**`, `software/**`, `block/**`, `.changeset/**` | `build-and-release.md:70` |
| `changeset-lists-every-touched-package` | high | `.changeset/**`, `workflow/**`, `model/**`, `ui/**`, `software/**`, `block/**` | `build-and-release.md:44` |
| `changeset-minor-major-lists-root-block` | medium | `.changeset/**` | `build-and-release.md:84` |
| `lockfile-with-workspace-change` | high | `pnpm-workspace.yaml`, `**/package.json`, `pnpm-lock.yaml` | `build-and-release.md:43` |
| `software-build-uses-block-tools` | high | `software/**/package.json` | `structure.md:70` |
| `software-package-not-private` | high | `software/**/package.json` | `structure.md:57` |
| `block-facade-not-private` | high | `block/package.json` | `build-and-release.md:131` |
| `block-meta-filled` | medium | `block/package.json` | `build-and-release.md:132` |
| `cross-block-dep-uses-facade` | medium | `test/**`, `**/package.json`, `pnpm-workspace.yaml` | `build-and-release.md:136` |
| `v3-block-test-uses-mutate-block-storage` | medium | `test/src/**` | `testing-block.md:80` |
| `tengo-test-lib-only-in-test-files` | high | `workflow/src/**/*.tengo` | `testing-workflow.md:32` |
| `tengo-tests-keep-a-runner` | high | `workflow/src/**`, `.github/workflows/**` | `testing-workflow.md:117` |
| `model-ui-test-wiring-from-refresh` | medium | `model/**`, `ui/**` | `testing-model-ui.md:46` |
| `turbo-test-passes-backend-env` | high | `turbo.json` | `testing.md:33` |
| `python-tests-workflow-for-software` | high | `software/**`, `.github/workflows/**` | `testing-software.md:84` |
| `pytest-pythonpath-config` | medium | `software/**/pyproject.toml` | `testing-software.md:36` |
| `no-skeleton-tests` | medium | `test/src/**`, `model/src/**/*.test.ts`, `ui/src/**/*.test.ts`, `workflow/src/**/*.test.ts` | `testing-block.md:142` |
| `block-test-asserts-ranges` | low | `test/src/**` | `testing-block.md:112` |
| `wf-body-returns-exports` | high | `workflow/src/**/*.tpl.tengo` | `failure-triage.md:128; workflow.md:151` |
| `exec-step-states-resources` | high | `workflow/src/**/*.tengo` | `block-anatomy.md:66` |
