# apm: artifact updates get no credentials for private dependencies

## Current behavior

[`apm.yml`](apm.yml) pins the `hello` skill of MPV/renovate-apm-private-skill, a private repository, at `v1.0.0`. That repository also has a `v1.1.0` tag.

The Renovate app's own token can't read that repository's tags, so [`renovate.json`](renovate.json) gives the `github-tags` lookups a token that can, through a host rule and a Mend secret. Without it, the lookup fails with "Failed to look up github-tags package MPV/renovate-apm-private-skill: no-result".

With the host rule, Renovate finds the update. It then runs `apm install` to refresh [`apm.lock.yaml`](apm.lock.yaml), but passes `apm install` no credentials. APM can't fetch the private repository, so the PR gets an "Artifact update problem" comment. `apm.yml` moves to `v1.1.0` while `apm.lock.yaml` and `.claude/skills/hello/SKILL.md` stay at `v1.0.0`.

Renovate's PR: [#1](https://github.com/MPV/renovate-apm-private-dependency/pull/1), which changes only `apm.yml`. Its artifact error quotes only APM's stderr, a policy warning. APM prints the authentication failure to stdout, so only Renovate's job log shows it; the relevant lines are in the discussion.

## Expected behavior

Renovate passes `apm install` the token its lookups use, so the PR moves `apm.yml`, `apm.lock.yaml` and `.claude/skills/hello/SKILL.md` to `v1.1.0` together.

## The private dependency

MPV/renovate-apm-private-skill has two tags:

| Tag | Commit |
|---|---|
| `v1.0.0` | `ef3619a2b58a34e0d25aa3b5c0a8722e64568dc1` |
| `v1.1.0` | `93dfc4e6db001fe886242d79a063497e1e60a162` |

The repository is private, so only accounts that can read it can check them:

```sh
git ls-remote --tags https://github.com/MPV/renovate-apm-private-skill
```

[`apm.lock.yaml`](apm.lock.yaml) records the same commit for `v1.0.0` (`resolved_ref: v1.0.0`, `resolved_commit: ef3619a…`), as `apm install` wrote it.

## Link to the Renovate issue or Discussion

- Discussion: [renovatebot/renovate#46874](https://github.com/renovatebot/renovate/discussions/46874)
- Fix: [renovatebot/renovate#46876](https://github.com/renovatebot/renovate/pull/46876)
