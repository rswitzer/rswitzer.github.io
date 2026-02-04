---
layout: base
title: KCSA Cram Sheet
permalink: /kcsa-cram-sheet/
---
# KCSA Cram Sheet
Welcome to my KCSA Cram Sheet. I’m currently studying for the Kubernetes and Cloud Native Security Associate (KCSA) exam and decided to share the notes I’m creating along the way. This guide is intentionally focused on reinforcing the core concepts most relevant to the exam, giving me a concise reference I can revisit daily to keep key ideas fresh and top of mind. 

As of 2/1/2026, this is still a work in progress. I’ll continue expanding and refining it as I work through the remaining content and deepen my understanding.

## Overview of Cloud Native Security

> **Exam rule**: You are tested on *what problem a tool solves*, not how to configure it.


## Cloud & Infrastructure Security (Cloud layer of 4Cs)

**Concepts**

* Network isolation
* Perimeter protection
* Infrastructure visibility

**Supporting Technologies**

* **VPC / VNet** (AWS, Azure, GCP)
* **Security Groups / NSGs / Firewalls**
* **Private Endpoints / Private Clusters**
* **Cloud-native WAFs**
* **Cloud provider logging**

  * AWS CloudTrail
  * Azure Monitor
  * Google Cloud Logging

**Exam cues**

* These are **preventive + detective**
* Misconfiguration is still your responsibility

## Identity & Access Management (IAM + Cluster)

**Concepts**

* Least privilege
* Strong identity boundaries
* Human vs workload identity

**Supporting Technologies**

* **Cloud IAM** (AWS IAM, Azure AD, GCP IAM)
* **Kubernetes RBAC**
* **Service Accounts**
* **OIDC integration** (IAM ↔ Kubernetes)
* **MFA** (human access)

**Exam cues**

* IAM controls *who can access*
* RBAC controls *what they can do*
* Service accounts = workload identity


## Cluster Security & Admission Control

**Concepts**

* Prevent unsafe configurations
* Control access to the API server

**Supporting Technologies**

* **Kubernetes API Server**
* **Admission Controllers**

  * Pod Security Admission (PSA)
  * OPA Gatekeeper
  * Kyverno
* **Audit Logs**

**Exam cues**

* Admission happens **before pods run**
* Policies are **preventive**
* Audit logs are **detective**

## Container & Image Security (Supply Chain)

**Concepts**

* Trusted artifacts
* Vulnerability prevention before runtime

**Supporting Technologies**

* **Image Scanners**

  * Trivy
  * Grype
* **Private Registries**

  * ECR, ACR, GCR
  * Harbor, Artifactory
* **Image Signing**

  * Cosign
  * Sigstore

**Exam cues**

* Scanning belongs in CI/CD
* Registries are control points
* Signing prevents tampering


## Secure CI/CD Pipelines (Shift Left)

**Concepts**

* Early detection
* Artifact integrity
* Policy enforcement

**Supporting Technologies**

* **CI/CD Platforms**

  * GitHub Actions
  * GitLab CI
  * Azure DevOps
* **Policy-as-Code**

  * OPA
  * Kyverno
* **SAST / Dependency Scanning**

  * SonarQube
  * Snyk

**Exam cues**

* CI/CD is a **security boundary**
* Automation reduces human error
* Supply chain attacks start here


## Network Security & Zero Trust

**Concepts**

* Least privilege networking
* East-west traffic control
* No implicit trust

**Supporting Technologies**

* **Kubernetes Network Policies**

  * Calico
  * Cilium
* **Service Mesh**

  * Istio
  * Linkerd
* **mTLS**

**Exam cues**

* Network Policies control traffic paths
* Service mesh secures service-to-service traffic
* Zero Trust is a **model**, not a product

## Secrets & Data Protection

**Concepts**

* Confidentiality
* Secure secret injection
* Encryption

**Supporting Technologies**

* **Kubernetes Secrets**
* **External Secrets Operator**
* **HashiCorp Vault**
* **Cloud KMS**

  * AWS KMS
  * Azure Key Vault
  * GCP KMS

**Exam cues**

* Secrets should not be baked into images
* Encrypt at rest + in transit
* Kubernetes cannot protect leaked secrets


## Runtime Security & Threat Detection

**Concepts**

* Detect active exploitation
* Monitor abnormal behavior

**Supporting Technologies**

* **Runtime Detection**

  * Falco
  * Tetragon
  * Aqua Security
* **Kernel Controls**

  * seccomp
  * AppArmor
  * SELinux

**Exam cues**

* Runtime tools are **detective**
* They catch zero-days
* Containers share the host kernel

## Logging, Monitoring & SIEM

**Concepts**

* Visibility
* Incident detection
* Forensics

**Supporting Technologies**

* **Log Aggregation**

  * Fluentd
  * Loki
* **SIEM**

  * Splunk
  * Azure Sentinel
  * AWS GuardDuty

**Exam cues**

* Logs ≠ prevention
* SIEM correlates events
* Required for incident response

## Benchmarking, Compliance & Governance

**Concepts**

* Configuration hygiene
* Baseline security posture

**Supporting Technologies**

* **CIS Benchmarks**
* **kube-bench**
* **Compliance frameworks**

  * SOC 2
  * ISO 27001
  * PCI-DSS
  * HIPAA
  * NIST 800-53

**Exam cues**

* Benchmarks detect misconfiguration
* Compliance ≠ secure by default
* Governance reduces drift

## Ultra-High-Yield Exam Mapping (Memorize This)

| Question Mentions | Think               |
| ----------------- | ------------------- |
| Image scanning    | Supply chain        |
| Admission policy  | Preventive control  |
| Network Policy    | Traffic isolation   |
| Service mesh      | mTLS / east-west    |
| Runtime detection | Zero-day visibility |
| IAM vs RBAC       | Who vs what         |
| CIS benchmark     | Baseline hygiene    |

