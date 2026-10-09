# apm: artifact updates get no credentials for private dependencies

## Current behavior

[`apm.yml`](apm.yml) pins the `hello` skill of MPV/renovate-apm-private-skill, a private repository, at `v1.0.0`. That repository also has a `v1.1.0` tag.

Renovate finds the update, because its `github-tags` lookup uses the app's token. It then runs `apm install` to refresh [`apm.lock.yaml`](apm.lock.yaml), but passes `apm install` no credentials. APM can't fetch the private repository, so the PR gets an "Artifact update problem" comment. `apm.yml` moves to `v1.1.0` while `apm.lock.yaml` and `.claude/skills/hello/SKILL.md` stay at `v1.0.0`.

The skill's repository is private, so only Renovate's job log shows the failure. The relevant lines are in the discussion.

## Expected behavior

Renovate passes `apm install` its github.com token, so the PR moves `apm.yml`, `apm.lock.yaml` and `.claude/skills/hello/SKILL.md` to `v1.1.0` together.

## Link to the Renovate issue or Discussion

Not posted yet. The draft is [MPV/renovate#23](https://github.com/MPV/renovate/issues/23).
