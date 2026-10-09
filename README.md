# apm: artifact updates get no credentials for private dependencies

## Current behavior

[`apm.yml`](apm.yml) pins the `hello` skill of MPV/renovate-apm-private-skill, a private repository, at `v1.0.0`. That repository also has a `v1.1.0` tag.

The Renovate app's own token can't read that repository's tags, so [`renovate.json`](renovate.json) gives the `github-tags` lookups a token that can, through a host rule and a Mend secret. Without it, the lookup fails with "Failed to look up github-tags package MPV/renovate-apm-private-skill: no-result".

With the host rule, Renovate finds the update. It then runs `apm install` to refresh [`apm.lock.yaml`](apm.lock.yaml), but passes `apm install` no credentials. APM can't fetch the private repository, so the PR gets an "Artifact update problem" comment. `apm.yml` moves to `v1.1.0` while `apm.lock.yaml` and `.claude/skills/hello/SKILL.md` stay at `v1.0.0`.

The skill's repository is private, so only Renovate's job log shows the failure. The relevant lines are in the discussion.

## Expected behavior

Renovate passes `apm install` the token its lookups use, so the PR moves `apm.yml`, `apm.lock.yaml` and `.claude/skills/hello/SKILL.md` to `v1.1.0` together.

## Link to the Renovate issue or Discussion

- Discussion: [renovatebot/renovate#46874](https://github.com/renovatebot/renovate/discussions/46874)
- Fix: [renovatebot/renovate#46876](https://github.com/renovatebot/renovate/pull/46876)
