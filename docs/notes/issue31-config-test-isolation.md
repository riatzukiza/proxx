# Configuration test environment isolation

[Issue #31](https://github.com/open-hax/proxx/issues/31) is the canonical repair
record. Its accepted scope includes every configuration test using `withEnv`,
not only the first test from the historical branch. The prior PR #336 and
its duplicate #385 were closed without merge; their one-line change is evidence
of incomplete coverage, not the new implementation baseline.

## Outcome and scope

Explicitly serialize the enclosing suite and all seven tests that mutate
`process.env`, retaining their
configuration assertions and their `finally` cleanup. Validate the suite under
its normal runner and a concurrent parent suite, including environment
restoration after the complete suite. No provider policy, runtime configuration,
shared service, package manifest, lockfile, or other test fixture changes.

## Acceptance and verification

- The enclosing configuration suite and every existing `withEnv` test are
  explicitly non-concurrent.
- Setting options on individual tests alone is insufficient under a concurrent
  parent: the parent controls scheduling of sibling tests.
- All seven original configuration assertions pass.
- A concurrent parent suite leaves the ambient environment unchanged after
  all children finish; no environment values are printed in diagnostics.
- Verification executes source through pinned tsx 4.20.6 using an isolated
  temporary npm cache:
  `npm exec --cache <isolated-cache> --yes --package=tsx@4.20.6 -- tsx --test scripts/config-env-isolation.test.mjs src/tests/config.test.ts`.
  It does not reuse another checkout's node_modules,
  write the shared npm cache, or start a server.
- The staging unit-test job runs the concurrent-parent probe before the
  existing complete unit suite, so future regression cannot skip this boundary.
- Diff hygiene passes and receipts preserve their existing history.

## Board and review boundary

Issue #31 retains its existing accepted GitHub projection. No repository-local
parser or shadow Rheos board implementation is added. No operational transition,
planning approval, native review verdict, or runtime/deployment result is inferred
from this plan or from local test success. PR qualification remains separate.

## Personal-fork development handoff, 2026-10-06

The user requires development PRs in personal forks and separately qualified final release/deploy PRs into origin. This branch carries only the six owned paths from origin PR453 (`d59b38681089d832dc2802d7f88f53077d5a1b7b`) on verified personal main `88471efd7f8cd27f64fc733a1c31369f00a6d432`. Personal staging `cf59b233a56d76c50b282e3e88863260a5741c17` has the same tree and is the development PR target under the existing redirect policy. No unrelated source-staging changes are imported.

The original planning/red/green/CI commits are ordinary cherry-picks. Receipt conflicts preserve the fork's existing history and carry only the original owned receipt/reflection additions; the original complete history remains inspectable at origin PR453. The fresh personal-base red run reproduces the after-hook failure for `PROXY_AUTH_TOKEN` with fourteen configuration assertions passing. Green executes fourteen tests in three suites with zero failures under pinned Node22.14.0/tsx4.20.6 and a new task-local npm cache. No environment values are printed. The no-new-TypeScript gate and whitespace pass.

Readiness is blocked by the inherited eager merge caller, tracked at https://github.com/open-hax/proxx/issues/454. The replacement stays draft with auto-merge off: the caller attempts SQUASH on a non-draft same-repository PR without canonical current-head qualification. Fork metadata currently reports `allow_auto_merge:false`; settings are not a replacement for reviewed enforcement. Manual CodeRabbit review can be requested on the draft; Codex quota and other pending reviewers retain their actual states. No workflow or repository settings are changed to bypass this blocker. Existing ensure-PR branch patterns exclude `codex/*`; no automated feature PR is requested.

A separately reviewed base-policy repair must precede marking this PR ready. No origin PR is closed or merged; no deployment, service, package/lock, or runtime policy change is made.
