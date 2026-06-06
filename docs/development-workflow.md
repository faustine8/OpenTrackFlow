# Development Workflow

## Branch Strategy

OpenTrackFlow uses a lightweight branch workflow.

The `main` branch should always remain runnable and suitable for demonstration.

Non-trivial changes should be developed in short-lived branches and merged back into `main` after
completion.

## Branch Naming

Use the following branch prefixes:

* `feature/` for new features
* `fix/` for bug fixes
* `docs/` for documentation changes
* `chore/` for project setup, dependencies, and tooling
* `refactor/` for code restructuring without behavior changes
* `test/` for test-related changes

Examples:

* `feature/mvp-event-ingestion`
* `feature/container-timeline`
* `feature/data-quality-rules`
* `docs/architecture`
* `chore/docker-compose`
* `fix/timeline-sort`

## Commit Message Style

Use a lightweight Conventional Commits style:

* `feat:` for new features
* `fix:` for bug fixes
* `docs:` for documentation
* `chore:` for project setup or tooling
* `refactor:` for refactoring
* `test:` for tests

Examples:

* `docs: add architecture overview`
* `chore: initialize Spring Boot project`
* `feat: ingest raw tracking events`
* `feat: build container timeline snapshot`
* `fix: handle duplicate tracking events`

## Feature Development Flow

Create a feature branch from `main`:

```bash
git switch main
git pull
git switch -c feature/mvp-event-ingestion
```

Commit changes during development:

```bash
git add .
git commit -m "feat: ingest raw tracking events"
```

Merge back to `main` when the feature is complete:

```bash
git switch main
git merge --squash feature/mvp-event-ingestion
git commit -m "feat: add MVP event ingestion flow"
```

Delete the local feature branch after merge:

```bash
git branch -d feature/mvp-event-ingestion
```

If the branch was pushed to GitHub, delete the remote branch as well:

```bash
git push origin --delete feature/mvp-event-ingestion
```

## Practical Rules

* Keep feature branches short-lived.
* Do not keep unfinished work on `main`.
* Prefer one feature branch for one clear goal.
* Small documentation or typo fixes may be committed directly to `main`.
* Update README or docs when a user-visible feature is added.
* Keep public documentation free of private notes, interview strategy, company details, and
  confidential context.
