# Contributing to Alert Hub

How we work together: branches, commits, pull requests and tasks.

## Branches

`<type>/AH-<id>-<short-description>`, e.g. `feat/AH-12-loader-hourly-scan`. AH-id is the task in Notion.

- Always branch from the latest `main` (`git checkout main && git pull` first)
- One task = one branch = one pull request

## Daily workflow

**Starting a new task**

```bash
git checkout main
git pull
git checkout -b <type>/AH-<id>-<short-description>
```

**While working (every day or two)**: bring in the latest `main`, so conflicts stay small:

```bash
git add .
git commit -m "<type>(<service>): <message>"
git pull origin main
```

**Before pushing**

1. Commit your work
2. `git pull origin main` to bring in the latest `main`
3. Fix conflicts if any: IntelliJ → **Git → Resolve Conflicts…**, then `git add .` and `git commit`
4. Check it still works: `./mvnw verify` in your service, and start it once
5. `git push`

**Keep tasks small**: a task should be done in 1–3 days. If it takes longer, split it into smaller tasks in Notion.

## Commits

`<type>(<service>): <message>`, e.g. `feat(loader): map jira fields to schema`.
The `(service)` part is optional for repo-wide changes, e.g. `docs: update README`.

| Type | Use it for | Example commit |
|---|---|---|
| `feat` | New functionality | `feat(loader): read latest jira file` |
| `fix` | A bug fix | `fix(processor): treat null threshold as 0` |
| `docs` | Documentation only (README, comments) | `docs: add branch naming rules` |
| `test` | Adding or fixing tests | `test(metric): service layer tests` |
| `refactor` | Restructuring code without changing behaviour | `refactor(action): extract condition parser` |
| `style` | Formatting only (spaces, imports), no logic change | `style(user): reformat code` |
| `perf` | Faster or lighter code | `perf(evaluation): index developer_id` |
| `build` | Dependencies, `pom.xml`, Dockerfile | `build(loader): add springdoc 3.2.0` |
| `ci` | GitHub Actions workflows | `ci(security): add build workflow` |
| `chore` | Maintenance that fits nowhere else (scaffolding, configs) | `chore(sms-sender): scaffold service` |
| `revert` | Undoing an earlier commit | `revert: feat(loader): hourly scan` |

## Pull requests

- No direct pushes to `main`; every change goes through a pull request
- 1 approval required; only maintainers merge; **squash merge only**
- PR title = final commit message on `main`, e.g. `feat(loader): hourly scan (AH-12)`
- If `main` changed meanwhile: click **Update branch** on the PR, or run `git pull origin main`

## Tasks

Tracked in Notion (**Alert Hub — Team Workspace → Tasks**). Move your task to *In Progress* when you start, *In Review* when the PR is open, *Done* when it's merged, and paste the PR link into the task.

## Secrets

Never commit passwords or real personal data; use environment variables and fake seed data.

## Adding a new microservice

See [docs/new-service.md](docs/new-service.md).