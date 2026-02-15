---
layout: base
title: KCSA Cram Sheet
permalink: /kcsa-cram-sheet/
---
# KCSA Cram Sheet
Welcome to my KCSA Cram Sheet. I’m currently studying for the Kubernetes and Cloud Native Security Associate (KCSA) exam and decided to share the notes I’m creating along the way. This guide is intentionally focused on reinforcing the core concepts most relevant to the exam, giving me a concise reference I can revisit daily to keep key ideas fresh and top of mind. 

As of 2/1/2026, this is still a work in progress. I’ll continue expanding and refining it as I work through the remaining content and deepen my understanding.

---

# Study Materials Used: 
## Courses 
- [KodeKloud — Kubernetes and Cloud Native Security Associate (KCSA)](https://learn.kodekloud.com/user/courses/kubernetes-and-cloud-native-security-associate-kcsa)
- [Udemy — Kubernetes & Cloud Security Associate (KCSA) with practice tests](https://www.udemy.com/course/kubernetes-cloud-security-associate-kcsa-with-practice-tests)


## Additional Reading 
- [NSA/CISA: Kubernetes Hardening Guidance](https://www.nsa.gov/Press-Room/News-Highlights/Article/Article/2716980/nsa-cisa-release-kubernetes-hardening-guidance/)
- [CloudSecDocs — Kubernetes Threat Model](https://cloudsecdocs.com/containers/theory/threats/k8s_threat_model/)
- [Kubernetes: Concepts — Security](https://kubernetes.io/docs/concepts/security/)

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

---

# Kubernetes Cluster Component Security


## API Server

### Security Responsibilities

The API Server is the **front door** to the cluster. If it is compromised, the cluster is compromised.

### Key Controls

**Authentication & Authorization**

* RBAC enforces least privilege
* OIDC or webhook auth integrates external IdPs
* Service Accounts scoped tightly to workloads
* Disable anonymous access (`--anonymous-auth=false`)

**Secure API Access**

* Restrict API exposure via firewalls / security groups
* Network Policies do *not* protect the API server directly (important exam distinction)
* Enable audit logging for all requests

**Encryption & Data Protection**

* TLS for all API communication
* Encrypt secrets at rest in etcd
* Rotate API server certificates regularly

**Abuse & DoS Protection**

* API rate limiting / request throttling
* Admission Controllers enforce policy before persistence

**Hardening**

* Disable unused flags and endpoints
* Limit `kubectl exec` / `kubectl proxy` via RBAC
* Patch control plane regularly

### Technologies

RBAC, OIDC, Admission Controllers, Audit Logs, TLS, etcd encryption

## Controller Manager

### Security Responsibilities

Maintains **desired state** (replicas, nodes, namespaces, service accounts). Over-permissioning = privilege escalation risk.

### Key Controls

**Access Control**

* Dedicated Service Account
* RBAC scoped to only required controllers
* Disable unused controllers (`--controllers=`)

**Isolation**

* Run on **dedicated control plane nodes**
* Protect configuration files
* Least privilege execution

**Secure Communication**

* TLS between Controller Manager, API Server, and etcd
* Certificate rotation

**Monitoring & Auditing**

* Enable audit logs
* Monitor `/metrics`
* Alert on abnormal reconciliation behavior

**Availability**

* Multiple replicas
* Leader election enabled

### Technologies

RBAC, TLS, Audit Logs, Prometheus, Leader Election


## Scheduler

### Security Responsibilities

Decides **where workloads run**. Manipulation can cause denial of service or workload exposure.

### Key Controls

**Access Control**

* RBAC limiting scheduling permissions
* Dedicated Service Account
* Disable anonymous auth

**Secure Communication**

* TLS everywhere
* Certificate rotation

**Hardening**

* Patch regularly
* Secure config files
* Disable unused flags

**Monitoring**

* Audit scheduling decisions
* Monitor scheduler metrics

**High Availability**

* Multiple schedulers
* Leader election

### Technologies

RBAC, TLS, Prometheus, Audit Logs, Leader Election


## Kubelet

### Security Responsibilities

Node-level authority. **High-value target** because it interacts with containers directly.

### Key Controls

**Authentication & Authorization**

* Disable anonymous access
* Webhook authorization (`--authorization-mode=Webhook`)
* Client certificate authentication
* TLS enforced

**Hardening**

* Restrict Kubelet API access
* Protect kernel defaults
* Disable legacy features

**Runtime Security**

* Disallow privileged containers
* Enforce seccomp, AppArmor, SELinux
* Use stronger isolation runtimes (gVisor, Kata)

**Monitoring**

* Audit logging
* `/metrics` endpoint monitoring
* Runtime threat detection

**Node Isolation**

* NodeRestriction Admission Controller
* Run Kubelet with least privilege
* Regular updates

### Technologies

seccomp, AppArmor, SELinux, Falco, gVisor, Kata Containers

## Container Runtime

### Security Responsibilities

Isolation boundary between workload and host.

### Key Controls

**Runtime Choice**

* Use containerd or CRI-O (Docker deprecated)
* Follow runtime-specific hardening guidance

**Patch Management**

* Regular updates to address CVEs
* Known risks: Dirty COW, runc breakout, container escapes

**Least Privilege**

* Run containers as non-root
* Read-only root filesystems
* Resource limits prevent DoS

**Mandatory Access Controls**

* SELinux
* AppArmor

**Monitoring**

* Centralized logging
* Runtime behavior monitoring
* Audit logs for forensics

### Technologies

containerd, CRI-O, SELinux, AppArmor, Prometheus, Fluentd


## KubeProxy

### Security Responsibilities

Manages **network rules**. Misconfiguration can expose internal services.

### Key Controls

**Config File Security**

* Secure kubeconfig permissions (≤644)
* Ownership `root:root`
* Protect service account tokens

**Secure Communication**

* TLS to API Server
* CA validation
* Service Account authentication

**Network Controls**

* iptables or eBPF mode
* Enforce Network Policies
* Monitor traffic flows

**Auditing & Updates**

* Enable audit logging
* Patch regularly

**Availability**

* Run as DaemonSet

### Technologies

iptables, eBPF, RBAC, TLS, Prometheus, Cilium


## Pod

### Security Responsibilities

Primary workload boundary.

### Key Controls

**Pod Security**

* Pod Security Standards (Baseline / Restricted / Privileged)
* Pod Security Admission
* Non-root execution
* Drop Linux capabilities
* Prevent privilege escalation

**Network Security**

* Network Policies
* Service Mesh mTLS
* Restrict egress

**Runtime Monitoring**

* seccomp / AppArmor / SELinux
* Falco, Tetragon
* Restrict `kubectl exec`

**Secrets & Storage**

* Secrets, not env vars
* Read-only mounts
* Encrypt secrets at rest

**Availability**

* Requests & limits
* Probes
* Pod Disruption Budgets

### Technologies

PSA, PSS, Network Policies, Istio, Falco


## Etcd

### Security Responsibilities

**Source of truth**. Full cluster compromise if exposed.

### Key Controls

**Encryption**

* TLS in transit
* Encryption at rest (AES-CBC / KMS providers)

**Access Control**

* Client cert authentication
* Network isolation
* No public exposure

**Backups**

* Regular snapshots
* Secure storage
* Restore testing

**Monitoring**

* Latency, disk I/O, quorum health
* Regular patching

### Technologies

TLS, AES-CBC, KMS, etcdctl


## Container Networking

### Security Responsibilities

Flat network = powerful but dangerous without controls.

### Key Controls

**Network Policies**

* Default deny
* Explicit allow rules
* Namespace isolation

**Traffic Encryption**

* mTLS via service mesh
* IPsec / WireGuard at CNI layer

**Workload Isolation**

* Namespaces
* Segmentation to reduce blast radius

**Ingress Protection**

* Secure ingress controllers
* TLS termination

### Technologies

Calico, Cilium, Istio, Linkerd, IPsec, WireGuard


## Client Security

### Security Responsibilities

Protects **humans and automation** accessing the cluster.

### Key Controls

**Authentication**

* OIDC with external IdPs
* Service Accounts for workloads

**Authorization**

* RBAC scoped by role
* No shared admin credentials

**Secure Communication**

* TLS and mTLS
* API auditing

**Credential Hygiene**

* Short-lived tokens
* Secrets, not config files
* Token rotation

### Technologies

RBAC, OIDC, TLS, Audit Logs


## Storage

### Security Responsibilities

Persistent data protection.

### Key Controls

**Access Control**

* RBAC for PVs/PVCs
* Namespace scoping
* Read-only mounts

**Encryption**

* At rest via StorageClasses
* In transit via TLS
* Cloud provider disk encryption

**Provisioning**

* Use PVCs (not direct PVs)
* Dynamic provisioning
* Standardized StorageClasses

**Backups & DR**

* Automated backups
* Tested restores
* Disaster recovery tooling

**Monitoring**

* Storage access auditing
* Usage anomaly detection

### Technologies

PVCs, StorageClasses, KMS, Velero, Prometheus

---

# Kubernetes Security Fundamentals 


## Pod Security Standards (PSS)

**Purpose:** Define allowed security posture of Pods (replaces legacy PSP model conceptually).

### Policy Levels

| Level          | Security Goal                        | Typical Use               |
| -------------- | ------------------------------------ | ------------------------- |
| **Privileged** | No restrictions                      | System components only    |
| **Baseline**   | Prevent obvious privilege escalation | Most workloads            |
| **Restricted** | Strict hardening                     | Production / multi-tenant |

### Key Controls Enforced

* Prevent privileged containers
* Require non-root user
* Restrict Linux capabilities
* Block host namespaces
* Restrict hostPath mounts
* Enforce seccomp / SELinux / AppArmor profiles

### Exam Notes

* Restricted = “secure by default”
* Baseline = “safe defaults”
* Privileged = essentially no security
* PSS is policy definition, **PSA is enforcement**


## Pod Security Admission (PSA)

**Built-in admission controller enforcing PSS at namespace level**

Replaces PodSecurityPolicy.

### Enforcement Modes

| Mode        | Behavior       |
| ----------- | -------------- |
| **Enforce** | Block pod      |
| **Audit**   | Log violation  |
| **Warn**    | Allow but warn |

### Namespace Labels

```
pod-security.kubernetes.io/enforce=restricted
pod-security.kubernetes.io/audit=baseline
pod-security.kubernetes.io/warn=baseline
```

### What It Evaluates

* securityContext
* capabilities
* host networking
* privileged flag
* root user

### Key Exam Points

* Namespace scoped
* Policy applied during admission
* Supports gradual rollout (audit → warn → enforce)

## Authentication

**Who are you?**

Kubernetes API & kubelet both authenticate requests.

### Kubelet Authentication (Important exam detail)

* Default: anonymous allowed ❌
* Disable: `anonymous-auth=false`
* Supported methods:

  * X509 client certificates
  * Bearer tokens

Kubelet must trust API server using CA bundle .

### Cluster Authentication Methods

| Method            | Use                    |
| ----------------- | ---------------------- |
| X509 Certificates | Most common            |
| Bearer Tokens     | Service accounts       |
| OIDC              | External IdP           |
| Webhook           | External auth provider |

### Kubeconfig

Contains:

* clusters
* users
* contexts

kubectl uses this for authentication .

## Authorization

**What are you allowed to do?**

### Mechanisms

| Type               | Description            |
| ------------------ | ---------------------- |
| RBAC               | Primary method         |
| ABAC               | Legacy                 |
| Webhook            | External policy        |
| Node Authorization | Kubelet limited access |

### RBAC Objects

| Object             | Scope               |
| ------------------ | ------------------- |
| Role               | Namespace           |
| ClusterRole        | Cluster             |
| RoleBinding        | Assign role         |
| ClusterRoleBinding | Assign cluster role |

### Kubelet Authorization

Default: **AlwaysAllow (insecure)**
Secure mode: `authorization-mode=Webhook` 

## Secrets

Store sensitive data securely.

### Types

| Type            | Purpose            |
| --------------- | ------------------ |
| Opaque          | Generic            |
| ServiceAccount  | Pod authentication |
| docker-registry | Pull images        |
| TLS             | Certificates       |

### Usage

* Environment variables
* Mounted volumes

### Security Facts

* Base64 encoded (NOT encrypted by default)
* Encrypt at rest recommended
* Protect via RBAC

### Best Practices

* External secret manager (Vault, cloud)
* Rotate regularly
* Avoid committing to Git

## Isolation and Segmentation

Reduce blast radius.

### Layers of Isolation

| Layer     | Mechanism                    |
| --------- | ---------------------------- |
| Workload  | securityContext              |
| Namespace | Logical boundary             |
| Node      | Taints / tolerations         |
| Network   | NetworkPolicy                |
| Kernel    | seccomp / SELinux / AppArmor |

### Kubernetes Mechanisms

* Namespaces separate resources
* Node pools isolate workloads
* Resource quotas prevent starvation
* Dedicated service accounts

## Audit Logging

Records **who did what and when**

### What It Tracks

* API calls
* User identity
* Source IP
* Resource accessed

### Storage Targets

* Files
* SIEM
* Elasticsearch / Loki

### Monitoring Tools

| Tool                  | Purpose                  |
| --------------------- | ------------------------ |
| Prometheus            | Metrics                  |
| Grafana               | Visualization            |
| Falco                 | Runtime threat detection |
| ELK                   | Logging                  |
| Cloud Security Center | Managed detection        |

### Best Practices

* Enable audit policy
* Immutable storage
* Alerting on anomalies

## Network Policy

Controls pod-to-pod communication.

Default: **allow all**

### Core Concepts

| Field       | Purpose          |
| ----------- | ---------------- |
| podSelector | Target pods      |
| policyTypes | Ingress / Egress |
| ingress     | Incoming rules   |
| egress      | Outgoing rules   |

### Example

Allow only frontend → backend

```yaml
kind: NetworkPolicy
spec:
  podSelector:
    matchLabels:
      app: backend
  ingress:
  - from:
    - podSelector:
        matchLabels:
          app: frontend
```

### Enforcement Requires CNI

Examples:

* Calico
* Cilium

# Rapid Exam Memory Map

**Identity**

* Authentication = who
* Authorization = permissions

**Pod Hardening**

* PSS defines
* PSA enforces

**Data Protection**

* Secrets
* Encryption at rest

**Blast Radius**

* Namespaces
* NetworkPolicy
* Kernel isolation

**Detection**

* Audit logs
* Falco


---

# Kubernetes Threat Model


## 1) Kubernetes Trust Boundaries and Data Flow

### Core Idea

A Kubernetes cluster is not one security zone.
It is a **layered set of trust boundaries**.
Security failures usually occur when attackers cross boundaries.


### Trust Boundaries (Isolation Layers)

| Boundary  | What it Separates      | Security Purpose                  |
| --------- | ---------------------- | --------------------------------- |
| Cluster   | Prod vs Dev vs Staging | Prevent environment contamination |
| Node      | Workloads across nodes | Stop lateral movement             |
| Namespace | Application tiers      | Authorization scope               |
| Pod       | App runtime            | Runtime isolation                 |
| Container | Process space          | Smallest blast radius             |

**Key exam concept:**
Every boundary crossed = privilege gain.


### Supporting Security Controls

* RBAC
* NetworkPolicies
* SecurityContext
* Pod Security Admission
* Separate clusters per environment

### Data Flow (Typical Path)

User → Ingress → Frontend Pod → Backend Pod → Database Pod

Security requirements at each hop:

| Hop                | Required Protection          |
| ------------------ | ---------------------------- |
| External → Ingress | TLS, Auth                    |
| Frontend → Backend | Service authentication       |
| Backend → DB       | Encryption + least privilege |
| Pod ↔ Pod          | NetworkPolicy restrictions   |

**Exam concept:**
Map communication paths to find attack opportunities.

### Threat Implications

* Lateral movement
* Unauthorized API calls
* Secret exposure
* Data exfiltration


## 2) Persistence

### Core Idea

Persistence = attacker survives restarts, reschedules, or rebuilds.


### Levels of Persistence

| Level                 | Survives                         |
| --------------------- | -------------------------------- |
| No resilience         | Container restart kills attacker |
| Container persistence | Restart survives                 |
| Pod persistence       | Pod recreate survives            |
| Node persistence      | Node reboot survives             |
| Cluster persistence   | etcd restore survives            |

### Common Techniques

| Technique               | What Attacker Does        |
| ----------------------- | ------------------------- |
| Modify config on volume | Survives restart          |
| Cron/init job           | Host execution            |
| Privileged container    | Host compromise           |
| API server access       | Permanent cluster control |
| etcd modification       | Total compromise          |


### Key Kubernetes Targets

* etcd
* API Server certificates
* Service Account tokens
* Host filesystem
* Mounted docker socket


### Mitigations

| Control                  | Purpose                  |
| ------------------------ | ------------------------ |
| Least-privilege RBAC     | Prevent secret access    |
| Read-only root FS        | Stop tampering           |
| No privileged containers | Block host access        |
| Secret isolation         | Prevent credential theft |
| Monitoring & auditing    | Detect footholds         |
| Patch images             | Remove exploit vectors   |


## 3) Denial of Service (DoS)

### Core Idea

Goal = make cluster unavailable.


### Major DoS Categories

| Category                 | Target                |
| ------------------------ | --------------------- |
| Resource exhaustion      | CPU/memory/storage    |
| Control plane disruption | API server, scheduler |
| Network disruption       | DNS, CNI              |
| Autoscaling abuse        | Infinite scaling      |


### Attack Techniques

| Attack           | Effect                         |
| ---------------- | ------------------------------ |
| Create many pods | Resource starvation            |
| Autoscale flood  | Financial + compute exhaustion |
| Flood API server | Cluster freeze                 |
| Overload etcd    | State failure                  |
| Kill kubelet     | Remove nodes                   |
| Break DNS        | Service outage                 |


### Mitigations

| Control                         | Why                     |
| ------------------------------- | ----------------------- |
| ResourceQuota                   | Contain blast radius    |
| LimitRange                      | Cap container usage     |
| NetworkPolicy                   | Block flooding          |
| Firewall                        | Protect control plane   |
| Secure service accounts         | Stop pod creation abuse |
| Monitoring (Prometheus/Grafana) | Detect spikes           |

## 4) Malicious Code Execution & Compromised Applications

### Core Idea

Attacker executes code inside container → expands control.


### Entry Points

| Method         | Example             |
| -------------- | ------------------- |
| Vulnerable app | RCE exploit         |
| Poisoned image | Supply chain attack |
| API exec       | kubectl exec abuse  |
| Host mount     | Escape container    |


### Techniques

| Technique                  | Result            |
| -------------------------- | ----------------- |
| Download tools (curl/wget) | Load malware      |
| Docker socket access       | Host control      |
| HostPID enabled            | Process hijack    |
| SYS_PTRACE                 | Inspect processes |
| Image repo poisoning       | Mass compromise   |

### Mitigations

| Control             | Purpose                  |
| ------------------- | ------------------------ |
| Image scanning      | Remove vulnerable images |
| Signed images       | Prevent tampering        |
| Restrict API server | Stop exec abuse          |
| Remove host mounts  | Prevent escape           |
| Monitoring          | Detect command execution |


## 5) Attacker on the Network

### Core Idea

Attacker has network access but not cluster credentials.

### Network Attacks

| Attack           | Description          |
| ---------------- | -------------------- |
| MITM             | Intercept traffic    |
| Packet sniffing  | Capture secrets      |
| DNS spoofing     | Redirect services    |
| ARP spoofing     | Traffic interception |
| API flooding     | Control plane DoS    |
| Lateral movement | Reach adjacent pods  |


### Kubernetes Weak Points

* Exposed API server
* Weak NetworkPolicies
* Unencrypted traffic
* Open node ports

### Mitigations

| Control             | Protection    |
| ------------------- | ------------- |
| TLS everywhere      | Stop sniffing |
| NetworkPolicies     | Contain pods  |
| Firewall rules      | Protect API   |
| mTLS / service mesh | Pod identity  |

## 6) Access to Sensitive Data

### Targets

| Location           | Example Data         |
| ------------------ | -------------------- |
| Secrets            | DB credentials       |
| Logs               | Tokens               |
| etcd               | Entire cluster state |
| Persistent Volumes | User data            |
| Network traffic    | Credentials          |


### Attack Methods

| Method           | Description         |
| ---------------- | ------------------- |
| RBAC misconfig   | Read secrets        |
| Log scraping     | Extract tokens      |
| Volume mounting  | Read DB files       |
| Network sniffing | Capture credentials |
| PKI compromise   | Decrypt cluster     |


### Mitigations

| Control           | Purpose          |
| ----------------- | ---------------- |
| Proper RBAC       | Limit read       |
| Encrypt etcd      | Protect state    |
| TLS communication | Protect transit  |
| Log sanitization  | Prevent leakage  |
| Secret isolation  | Prevent exposure |

## 7) Privilege Escalation

### Core Idea

Gain higher permissions than intended.

### Common Escalation Paths

| Method                 | Example              |
| ---------------------- | -------------------- |
| Over-permissive RBAC   | Admin access         |
| Privileged containers  | Host root            |
| Service account token  | API takeover         |
| Host filesystem mounts | Node control         |
| Master node workloads  | Control plane access |

### Preventive Controls

| Control                        | Why                   |
| ------------------------------ | --------------------- |
| allowPrivilegeEscalation=false | Block elevation       |
| Drop capabilities              | Reduce attack surface |
| Least privilege RBAC           | Limit damage          |
| Avoid privileged pods          | Protect host          |
| Audit RBAC                     | Detect risks          |


# Exam Memory Trick

Think in attacker progression:

**Network → Container → Pod → Node → Control Plane → Data**

Every KCSA question typically asks:

> “Which boundary failed?”

