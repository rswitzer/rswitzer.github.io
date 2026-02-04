---
layout: base
title: KCSA Cram Sheet
permalink: /kcsa-cram-sheet/
---
# KCSA Cram Sheet
Welcome to my KCSA Cram Sheet. I’m currently studying for the Kubernetes and Cloud Native Security Associate (KCSA) exam and decided to share the notes I’m creating along the way. This guide is intentionally focused on reinforcing the core concepts most relevant to the exam, giving me a concise reference I can revisit daily to keep key ideas fresh and top of mind. 

As of 2/1/2026, this is still a work in progress. I’ll continue expanding and refining it as I work through the remaining content and deepen my understanding.

---

# KCNA Study Guide – Cloud Native Security

> **Core mental model**
> **Security failures cascade downward. Controls should stop attackers as early as possible.**
> Kubernetes amplifies both good *and* bad security decisions.
---

## 1. The 4Cs of Cloud Native Security

The **4Cs** describe where security controls can be applied in a cloud-native system, from the broadest surface area to the most specific.

| Layer     | Focus                    | Key Idea                           |
| --------- | ------------------------ | ---------------------------------- |
| Cloud     | Provider infrastructure  | Stop attackers at the perimeter    |
| Cluster   | Kubernetes control plane | Control access and configuration   |
| Container | Images and runtime       | Reduce blast radius                |
| Code      | Application logic        | Kubernetes can’t fix insecure code |


### Cloud

**Focus**: Where the cluster lives

**Threats**

* Public IP exposure
* Open management ports (e.g., Docker 2375)
* Flat or unsegmented networks

**Controls**

* Firewalls / Security Groups / NSGs
* VPC / VNet isolation
* Private endpoints and private clusters
* Restricted ingress

**Exam cues**

* Port scanning = cloud-layer failure
* Firewalls are **preventive**
* If attackers can scan it, it’s already misconfigured

### Cluster

**Focus**: Kubernetes control plane and access

**Threats**

* Public API server or dashboard
* No authentication or RBAC
* Weak admission controls

**Controls**

* Kubernetes RBAC
* Admission controllers
* Audit logging
* API server access restrictions

**Exam cues**

* Dashboard exposure = cluster issue
* RBAC controls API actions, not network traffic
* Admission happens **before pods run**

### Container

**Focus**: Images and runtime execution

**Threats**

* Untrusted images
* `latest` tags
* Privileged containers
* Excess Linux capabilities

**Controls**

* Image scanning and signing
* Trusted registries
* seccomp, AppArmor, SELinux
* Least privilege

**Exam cues**

* Privileged containers enable escape
* Container security includes **supply chain + runtime**
* Smaller images = smaller attack surface

### Code

**Focus**: Application logic and dependencies

**Threats**

* Hardcoded secrets
* SQL injection
* Vulnerable libraries

**Controls**

* Secure coding practices
* SAST and dependency scanning
* Secrets management
* mTLS for service communication

**Exam cues**

* Kubernetes cannot fix insecure code
* Secrets ≠ environment variables
* Code is the final blast radius

## 2. Cloud Provider and Infrastructure Security

### Shared Responsibility Model (Highly Tested)

**Cloud provider secures**

* Physical data centers
* Hardware
* Underlying infrastructure

**You secure**

* Network configuration
* IAM
* Kubernetes clusters
* Workloads, data, secrets

> **Exam rule**
> If you can configure it, you are responsible for securing it.


### Network & Perimeter Security

**Concepts**

* Network isolation
* Perimeter protection
* Infrastructure visibility

**Technologies**

* VPC / VNet
* Firewalls, Security Groups, NSGs
* Cloud-native WAFs
* Private endpoints and private clusters

**Exam cues**

* These controls are **preventive**
* Misconfiguration is still your fault


### Logging & Visibility

**Purpose**

* Detect misuse
* Support forensics
* Enable incident response

**Technologies**

* AWS CloudTrail
* Azure Monitor
* Google Cloud Logging

**Exam cues**

* Logging is **detective**
* Visibility does not prevent attacks

## 3. Controls and Frameworks

### Control Types

| Type       | Purpose         | Examples              |
| ---------- | --------------- | --------------------- |
| Preventive | Stop bad things | Firewalls, RBAC, PSA  |
| Detective  | Find bad things | Logs, monitoring      |
| Corrective | Respond         | Automated remediation |

**Exam cue**

* Know **why** a control exists, not how to configure it

### Policy & Governance

**Purpose**

* Prevent unsafe configurations
* Reduce human error
* Enforce standards consistently

**Technologies**

* Pod Security Admission (PSA)
* OPA Gatekeeper
* Kyverno

**Exam cues**

* Policies act **before deployment**
* Governance reduces drift
* Automation > manual review

### Benchmarks & Compliance

**Concepts**

* Configuration hygiene
* Baseline security posture

**Technologies**

* CIS Kubernetes Benchmark
* kube-bench
* SOC 2, ISO 27001, PCI-DSS, HIPAA, NIST 800-53

**Exam cues**

* Compliance ≠ secure
* Benchmarks detect misconfiguration

## 4. Isolation Techniques

> **Isolation is layered. No single mechanism is sufficient.**

### Isolation Layers

| Layer              | Purpose                |
| ------------------ | ---------------------- |
| Namespaces         | Logical separation     |
| RBAC               | API access control     |
| Network Policies   | Traffic isolation      |
| Admission Policies | Prevent unsafe configs |
| Node Isolation     | Scheduling control     |
| Kernel Controls    | Reduce blast radius    |

**Exam cues**

* Namespaces alone ≠ security boundary
* Multi-tenancy requires multiple layers

### Network Isolation

**Controls**

* Kubernetes Network Policies
* Default-deny traffic models

**Technologies**

* Calico
* Cilium

**Exam cues**

* Network Policies control **traffic paths**
* They do not control identity

### Service-to-Service Isolation

**Purpose**

* Secure east-west traffic
* Prevent lateral movement

**Technologies**

* Service Mesh (Istio, Linkerd)
* mTLS

**Exam cues**

* Service mesh secures service communication
* Zero Trust is a **model**, not a product

## 5. Artifact Repository and Image Security

### Supply Chain Security

**Common Failures**

* Public images
* `latest` tags
* No scanning or signing

**Concepts**

* Images are build artifacts
* Supply chain security starts early

### Build-Time Controls (Shift Left)

**Technologies**

* Image scanners (Trivy, Grype)
* Dependency scanning
* Artifact signing (Cosign, Sigstore)

**Exam cues**

* Scanning belongs in CI/CD
* Signing prevents tampering

### Registries as Control Points

**Technologies**

* Private registries (ECR, ACR, GCR)
* Harbor, Artifactory

**Exam cues**

* Registries are more than storage
* Trust boundaries exist at pull time

## 6. Workload and Application Code Security

### Secure Workload Configuration

**Best Practices**

* Run as non-root
* Drop unnecessary capabilities
* Disallow privileged containers
* Set CPU and memory limits

**Exam cues**

* Privileged containers = escape risk
* Resource limits prevent DoS


### Application Code Security

**Static Security**

* SAST
* Dependency scanning

**Runtime Security**

* Behavior-based detection
* Detect unknown exploits

**Technologies**

* SonarQube, Snyk
* Falco, Tetragon, Aqua Security

**Exam cues**

* Static tools miss zero-days
* Runtime security is **detective**

### Secrets & Data Protection

**Principles**

* Inject secrets at runtime
* Encrypt at rest and in transit
* Never hardcode secrets

**Technologies**

* Kubernetes Secrets
* External Secrets Operator
* HashiCorp Vault
* Cloud KMS (AWS KMS, Azure Key Vault, GCP KMS)

**Exam cues**

* Secrets ≠ environment variables
* Kubernetes cannot protect leaked secrets


## Final High-Yield Exam Mapping

| Question Mentions | Think               |
| ----------------- | ------------------- |
| Image scanning    | Supply chain        |
| Admission policy  | Preventive control  |
| Network Policy    | Traffic isolation   |
| Service mesh      | East-west mTLS      |
| Runtime detection | Zero-day visibility |
| IAM vs RBAC       | Who vs what         |
| CIS benchmark     | Baseline hygiene    |

