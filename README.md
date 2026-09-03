A Zero-Trust Marketplace for Cross-Organization Compute Using Confidential Enclaves
# Zero-Trust Confidential Compute Marketplace

A marketplace where organizations can lease idle computing capacity to other organizations — even competitors — while confidential-computing enclaves, remote attestation, and cryptographic key release protect workload confidentiality and integrity from the infrastructure provider itself.

> **Research question:** Can compute capacity become a trust-minimized commodity when workload confidentiality and execution integrity are verified cryptographically rather than assumed institutionally?

## Why

Traditional compute leasing requires significant institutional trust — in the infrastructure provider, its administrators, the OS, the hypervisor, and the physical host. That's especially uncomfortable when competitors share infrastructure. This project uses **cryptographic evidence, not institutional trust**, to let a provider run a buyer's sensitive workload without ever seeing the plaintext data.

## How it works

```
Buyer → Marketplace → Provider → Confidential Enclave → Attestation
     → Buyer Verification → Key Release → Workload Execution → Encrypted Output
```

1. Buyer encrypts sensitive workload data and submits it with resource requirements.
2. Marketplace matches the workload to a provider with available capacity.
3. Provider starts the workload inside a confidential enclave.
4. The enclave produces attestation evidence (platform identity, enclave identity, workload measurement, signature).
5. The evidence is verified against an approved policy.
6. Only on successful verification is the decryption secret released to the enclave.
7. The workload executes and returns encrypted output — the provider never sees plaintext input.

Full architecture and diagrams: see the [wiki](../../wiki).

## Core components

| Component | Responsibility |
|---|---|
| Buyer | Registration, workload submission, encrypted input, attestation verification |
| Provider | Resource advertisement, job acceptance, enclave execution, attestation generation |
| Marketplace | Provider/job registry, matching, lease management, pricing, settlement |
| Confidential Enclave | Protected execution of workload code and data |
| Attestation Service | Verifies cryptographic evidence about the execution environment |
| Key-Release Service | Releases workload secrets only when verification succeeds |

## Threat model (summary)

The provider, provider OS, provider administrator, marketplace, and network are all treated as **untrusted** for confidentiality. The trusted computing base is limited to the confidential-computing mechanism, the attestation process, and cryptographic primitives. See the [Security and Threat Model wiki page](../../wiki/Security-and-Threat-Model) for the full breakdown.

## Tech stack

| Area | Technology |
|---|---|
| Backend | Java + Spring Boot |
| Database | PostgreSQL |
| API | REST or gRPC |
| Containers | Docker |
| Frontend | React |
| Confidential computing | Intel SGX or AMD SEV-SNP (mock/simulated TEE if no hardware access) |
| Cryptography | Established, audited libraries only |

## Project status

This project follows a phased build:

1. **Research** — zero trust, TEE options, remote attestation, threat modelling
2. **Marketplace prototype** — buyer/provider/marketplace registration, matching, leasing
3. **Secure execution** — containerized, isolated workload execution with encrypted I/O
4. **Attestation** — enclave evidence, verification policy, conditional key release
5. **Marketplace economics** — pricing, usage tracking, internal credits
6. **Evaluation** — security tests, performance measurements, final demo

See [Development Phases](../../wiki/Development-Phases) and the open [issues](../../issues) for current progress.

## MVP

The MVP does **not** aim to be a full commercial cloud marketplace. It proves one thing: a provider can execute a buyer's workload inside a confidential enclave, verified end-to-end via remote attestation, **without ever receiving the buyer's plaintext sensitive data.**

## Getting started

```bash
# clone the repo
git clone <repo-url>
cd <repo-name>

# start local infra (Postgres, etc.)
docker compose up -d

# run the backend
./mvnw spring-boot:run

# run the frontend
cd frontend && npm install && npm start
```

> Update this section once the actual project scaffolding (build tool, module layout, env vars) is in place.

## Testing

```
Unit → Component → Integration → Client/Server → System → Demonstration/Acceptance
```

Security tests specifically attempt to: access protected input directly, tamper with workload measurements, submit invalid attestation, and obtain secrets without successful verification.

## Success criteria

- **Security:** a malicious provider cannot obtain plaintext workload input
- **Integrity:** invalid enclave measurements are rejected
- **Availability:** jobs can be matched to providers
- **Performance:** confidential execution overhead and attestation latency are measured
- **Marketplace:** leases, usage, and settlement recorded end-to-end

## Documentation

- Full architecture, data model, and phase-by-phase detail: [project wiki](../../wiki)
- Tracked work: [issues](../../issues)