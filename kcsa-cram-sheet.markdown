---
layout: base
title: KCSA Cram Sheet
permalink: /kcsa-cram-sheet/
---
# KCSA Cram Sheet
Welcome to my KCSA Cram Sheet. I’m currently studying for the Kubernetes and Cloud Native Security Associate (KCSA) exam and decided to share the notes I’m creating along the way. This guide is intentionally focused on reinforcing the core concepts most relevant to the exam, giving me a concise reference I can revisit daily to keep key ideas fresh and top of mind. 

As of 2/1/2026, this is still a work in progress. I’ll continue expanding and refining it as I work through the remaining content and deepen my understanding.

## Overview of Cloud Native Security

The Kubernetes and Cloud Native Security Associate (KCSA) exam is an entry-level, vendor-neutral certification that validates foundational knowledge of cloud-native and Kubernetes security concepts.

The exam focuses on understanding security principles and reasoning, not hands-on configuration. Candidates are tested on how security applies across the cloud, cluster, container, and code layers, including topics like shared responsibility, isolation techniques, supply chain security, workload protection, and common attack patterns.

Rather than asking you to configure tools, the KCSA evaluates your ability to identify risks, choose appropriate controls, and reason about where security responsibilities live in a cloud-native environment.

In short: the KCSA measures whether you can think like a cloud-native security practitioner, even if you are not yet operating Kubernetes security tooling day to day.

## Overview of Cloud Native Security

---

## The 4Cs of Cloud Native Security (MEMORIZE THIS)

> **Security failures cascade downward. Controls should stop attackers early.**

### 1. **Cloud (Infrastructure / Provider)**

**Focus**: Where the cluster lives

**Threats**

* Public IP exposure
* Open ports (e.g., Docker 2375)
* No network segmentation

**Controls**

* Firewalls / Security Groups
* Network segmentation
* Private endpoints
* Restricted ingress

**Exam cues**

* Port scanning = Cloud failure
* Firewalls are **preventive**
* If attackers can scan it, it’s already misconfigured

### 2. **Cluster**

**Focus**: Kubernetes control plane & access

**Threats**

* Public Kubernetes API / Dashboard
* No authentication or RBAC
* Weak admission controls

**Controls**

* RBAC (who can do what)
* API server access restrictions
* Admission controllers
* Audit logging

**Exam cues**

* Dashboard exposure = cluster security issue
* RBAC ≠ network security
* Admission controls block bad configs *before* runtime

### 3. **Container**

**Focus**: Images + runtime behavior

**Threats**

* Untrusted images
* `latest` tags
* Privileged containers
* Excess Linux capabilities

**Controls**

* Trusted registries only
* Image scanning
* Disallow privileged containers
* Sandboxing (seccomp, AppArmor)
* Least privilege

**Exam cues**

* Privileged containers enable escape
* Container security = supply chain + runtime
* “Runs anywhere” ≠ safe everywhere

### 4. **Code**

**Focus**: Application logic & secrets

**Threats**

* Hardcoded secrets
* Plaintext env vars
* SQL injection
* Vulnerable dependencies

**Controls**

* Kubernetes Secrets (encrypted at rest)
* Secure coding practices
* Dependency scanning
* mTLS for service communication

**Exam cues**

* Kubernetes cannot fix insecure code
* Secrets ≠ environment variables
* Code is the final blast radius

## Cloud Provider & Infrastructure Security

### Shared Responsibility Model (VERY TESTED)

**Provider handles**

* Physical data centers
* Hardware
* Base infrastructure

**You handle**

* Network configs
* Firewalls
* IAM
* Cluster configuration
* Data & workloads

> **Rule of thumb**:
> If you can configure it, you are responsible for securing it.

### Common Provider Capabilities

(Not tool-memorization heavy, concept-focused)

* Threat detection (SIEM/SOAR)
* Web Application Firewalls (WAF)
* Managed Kubernetes security features

**Exam cues**

* These support security but don’t replace it
* Misconfiguration is still your fault

## Controls & Security Frameworks

### Control Types

* **Preventive**: firewalls, RBAC, PSA
* **Detective**: logging, scanning, monitoring
* **Corrective**: automated remediation, response

**Exam cues**

* Know *why* controls exist
* Map controls to attack stages

## Isolation Techniques (HIGH-VALUE SECTION)

> Isolation is layered. No single feature is enough.

### 1. Namespace Isolation

* Logical separation of workloads
* NOT a security boundary by itself

### 2. RBAC

* Controls API access
* Who can do what, where

### 3. Network Policies

* Control pod-to-pod traffic
* Default Kubernetes networking is open

### 4. Policy Enforcement

* Enforce rules at deployment time
* Example: Pod Security Admission (PSA)

**Exam cues**

* Namespaces alone ≠ isolation
* PSA prevents risky pod configs
* Multi-tenancy requires all four

## Artifact Repository & Image Security (Supply Chain)

### Common Failure Pattern

* Pulling public images
* Using `latest`
* No vulnerability scanning

### Key Concepts

* Images are **build artifacts**
* Supply chain security starts at build time
* Artifact repos are control points

### Controls

* Trusted base images
* Vulnerability scanning (build + storage)
* Private registries
* Image signing

**Exam cues**

* `latest` ≠ secure
* Scanning belongs in CI/CD
* Repositories are more than storage

## Workload & Application Code Security

### Static Security

* SAST (e.g., SQL injection detection)
* Dependency scanning (CVEs)

### Runtime Security

* ASM detects unknown exploits
* Runtime monitoring (syscalls, resource usage)

### Key Insight

* Static tools miss zero-days
* Runtime visibility catches exploitation

**Exam cues**

* Static + dynamic security are complementary
* Runtime security matters in production

## Common Exam Traps

* ❌ Namespace = security boundary
* ❌ Kubernetes fixes insecure code
* ❌ Cloud provider secures everything
* ❌ `latest` means newest & safest
* ❌ RBAC controls network traffic

## Final Mental Model (Repeat This)

* **4Cs = attack stopping points**
* **Security shifts left** (build → deploy → run)
* **Isolation is layered**
* **Misconfiguration causes most breaches**
* **Kubernetes amplifies both good and bad security**
