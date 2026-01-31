---
layout: post
title:  "Back in Blog Mode (and Fresh off the GH-200)"
date:   2025-12-22
categories: github

---

I haven’t blogged in a bit, and my home lab has definitely taken a back seat lately. The reason? I’ve been heads-down on certifications.

Just a few weeks ago, I wrapped up the **KCNA**, and **today I officially passed the GitHub Actions (GH-200) exam**.

As an architect, I actually enjoy taking exams. Not because I love tests, but because they force accountability. When you work at the architecture level long enough, it’s easy to drift away from the implementation details. Certifications give me a concrete way to validate that I’m still absorbing the material and paying attention to the finer points. The stuff you don’t always hit when you’re doing POCs or designing high-level architectures.

Accountability looks different for everyone, but for me, certs are a reliable forcing function to stay sharp.

## Why I’m Sharing This (While It’s Still Fresh)

Since I’m only a few hours removed from hitting that **“pass”** screen, I figured now was the perfect time to share a few study tips and the cheat sheet I built while prepping.

This is very much a *“here’s what worked for me”* post.

## How I Studied

I like to use **multiple sources** when learning a new technology. I’ve found that reviewing the same material from two to three different angles really helps reinforce concepts.

My usual pattern looks like this:

* **1 primary reading resource**
* **1–2 video resources**

For this exam, I reviewed the following:

### Reading

* [Microsoft's Offical GH-200 Course Material](https://learn.microsoft.com/en-us/training/courses/gh-200t00)

### Video

* [Microsoft's Offical GH-200 Video Training Course](https://youtu.be/8m9JBtGFMp8?si=ocC69l7fL2AKorGK)
* [Andrew Brown's GH-200 Course](https://youtu.be/Tz7FsunBbfQ?si=m2qPuOQLWenOZFY_)


## Practice Exams

For practice exams, I stuck with:

* [Microsoft Learning Practice Questions](https://learn.microsoft.com/en-us/credentials/certifications/github-actions/practice/assessment?assessment-type=practice&assessmentId=1001&practice-assessment-type=certification) - I actually think these are too easy compare to the exam. This is a good starting place though.
* [Exam Pro](https://www.exampro.co/github-actions)

The ExamPro videos are free on YouTube, and if you want more structured practice, you can buy their flashcards and 3–4 full practice exams for around **$30**. For me, that was well worth it.

## What to Expect on the Exam

One thing you *really* need to know about this exam: **questions compound knowledge**.

You’ll often see an answer that looks like the obvious, quick win. But it’s not the right one.

Slow down. Read carefully. There’s usually an additional constraint, feature, or enterprise-level detail baked into the question that changes the answer.

I also heard from multiple coworkers that the **enterprise features** caught them off guard. Because I had that heads-up going in, that section didn’t trip me up. If you’re studying, don’t skip enterprise topics. They matter.

## Results & Timeline

* **Score:** 890 / 1000
* **Study time:** ~2–3 weeks

  * 1–2 hours after work on weekdays
  * A few hands-on labs on the weekends

I already had a background in **Azure DevOps**, which made the GitHub Actions content easier to digest, but this exam is still very doable if you’re consistent.


## The Cheat Sheet

Below is the cheat sheet I put together to review right before the exam.

## Domain 1: Author and Maintain Workflows (40%)

### Workflow Triggers (Events)

**Multiple Events**

```yaml
on: [push, pull_request]
```

**Scheduled (Cron)**

```yaml
on:
  schedule:
    - cron: "0 2 * * 1" # Mondays at 2am UTC
```

**Manual Trigger**

```yaml
on:
  workflow_dispatch:
    inputs:
      environment:
        required: true
```

**Webhook Events**

* `check_run`
* `check_suite`
* `deployment`
* `deployment_status`
* Used for CI/CD integrations and chained workflows


### Workflow Components

| Component | Description                           |
| --------- | ------------------------------------- |
| Workflow  | YAML file in `.github/workflows/`     |
| Job       | Group of steps, runs on one runner    |
| Step      | Action or shell command               |
| Action    | Reusable unit (JS, Docker, composite) |
| Run       | One execution of a workflow           |



### Workflow Syntax Basics

```yaml
jobs:
  build:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - run: npm install
```

**Key Rules**

* YAML indentation matters
* `steps` must be under `jobs.<job_id>`
* Each job runs on a single runner


### Conditional Logic

**Step Condition**

```yaml
if: github.ref == 'refs/heads/main'
```

**Job Condition**

```yaml
if: success()
```

### Dependent Jobs

```yaml
jobs:
  build:
    runs-on: ubuntu-latest

  deploy:
    needs: build
    runs-on: ubuntu-latest
```


### Runners

**GitHub-hosted**

* Short-lived
* No custom software
* Best for standard CI

**Self-hosted**

* Custom tools
* Network access
* Long-running jobs

**Runner Labels**

```yaml
runs-on: [self-hosted, linux, custom-software]
```


### Secrets & Environment Variables

**Encrypted Secrets**

```yaml
env:
  API_KEY: ${{ secrets.API_KEY }}
```

**Default Environment Variables**

* `GITHUB_REPOSITORY`
* `GITHUB_REF`
* `GITHUB_SHA`
* `GITHUB_RUN_ID`

**Custom Env Vars**

* Workflow-level: `env:`
* Job-level: `jobs.<job>.env`
* Step-level: `steps[].env`


### GITHUB_TOKEN

Use when:

* Creating issues
* Publishing packages
* Pushing to repos
* Authenticating to GitHub APIs

```yaml
env:
  GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
```


### Workflow Commands

**Set Environment Variable**

```bash
echo "MY_VAR=value" >> $GITHUB_ENV
```

**Set Output**

```bash
echo "result=success" >> $GITHUB_OUTPUT
```


### Publishing & Deployment

**GitHub Packages / GHCR**

* Authenticate
* Build artifact/image
* Push to registry

**Service Containers**

```yaml
services:
  postgres:
    image: postgres:15
```

**CodeQL**

```yaml
- uses: github/codeql-action/analyze@v3
```

**Releases**

* Create GitHub release
* Upload artifacts
* Deploy to cloud provider


## Domain 2: Consume Workflows (20%)

### Interpreting Workflow Effects

* Check commits, PRs, releases, issues
* Inspect triggered event via run metadata
* Read workflow YAML to infer behavior


### Diagnosing Failures

**UI**

* Actions tab → Workflow → Job → Step logs

**REST API**

* List workflow runs
* Download logs and artifacts


### Debugging

**Enable Step Debug**

```yaml
env:
  ACTIONS_STEP_DEBUG: true
```

### Data Between Jobs

**Artifacts**

```yaml
- uses: actions/upload-artifact@v4
```

**Outputs**

```yaml
outputs:
  version: ${{ steps.build.outputs.version }}
```

### Caching

{% raw %}
```yaml
- uses: actions/cache@v4
  with:
    path: ~/.npm
    key: npm-${{ hashFiles('package-lock.json') }}
```
{% endraw %}


### Workflow Management

* Add status badge from Actions UI
* Delete vs Disable:

  * **Disable:** keeps history
  * **Delete:** removes file + history
* Environment protections:

  * Required reviewers
  * Deployment gates
* Matrix builds:

```yaml
strategy:
  matrix:
    node: [18, 20]
```

## Domain 3: Author and Maintain Actions (25%)

### Action Types

| Type       | Use Case                   |
| ---------- | -------------------------- |
| JavaScript | Fast, reusable, Node-based |
| Docker     | Heavy dependencies         |
| Composite  | Glue steps together        |


### Action Structure

```text
action.yml
index.js
Dockerfile
```

**Metadata**

* `name`
* `inputs`
* `runs`
* `outputs`


### Troubleshooting Actions

**JavaScript**

* Check Node version
* Review logs
* Validate `action.yml`

**Docker**

* Dockerfile errors
* ENTRYPOINT issues
* Permissions


### Workflow Commands in Actions

* Exit codes control success/failure
* `core.setFailed()`
* `process.exit(1)`


## Domain 4: Manage GitHub Actions in the Enterprise (15%)

### Reuse & Distribution

* Reusable workflows (`workflow_call`)
* Centralized action repos
* Naming conventions
* Version pinning

### Access Control

* Organization policies
* Allow/deny action sources
* Control who can use which actions


### Runners at Scale

**GitHub-hosted**

* Subject to IP allow lists

**Self-hosted**

* Custom networking
* Proxies
* Labels
* Groups for access control


### Runner Management

* Group runners by purpose
* Move runners between groups
* Monitor `_diag` logs
* Update runner binaries regularly


### Secrets in the Enterprise

| Scope        | Use                   |
| ------------ | --------------------- |
| Organization | Shared across repos   |
| Repository   | Repo-specific         |
| Environment  | Protected deployments |

Access via:

```yaml
{% raw %}
${{ secrets.MY_SECRET }}
```
{% endraw %}
