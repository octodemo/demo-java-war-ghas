# Demo Java WAR — GHAS & Syft SBOM Demo

A minimal Maven WAR application with intentionally **outdated dependencies** for demonstrating:

- **Syft** SBOM generation from a WAR file
- **GitHub Advanced Security (GHAS)** — Dependabot alerts, CodeQL code scanning
- Dependency vulnerability discovery for enterprise Java apps

> ⚠️ **This repository contains known-vulnerable dependencies. Do not deploy to production.**

---

## Vulnerable Dependencies Included

| Library | Version | CVE | Severity | Common Name |
|---------|---------|-----|----------|-------------|
| `log4j-core` | 2.14.1 | CVE-2021-44228 | Critical | Log4Shell |
| `log4j-core` | 2.14.1 | CVE-2021-45046 | Critical | Log4Shell variant |
| `spring-webmvc` | 5.3.17 | CVE-2022-22965 | Critical | Spring4Shell |
| `commons-text` | 1.9 | CVE-2022-42889 | Critical | Text4Shell |
| `jackson-databind` | 2.13.0 | CVE-2022-42003 | High | — |
| `snakeyaml` | 1.29 | CVE-2022-25857 | High | — |

---

## Prerequisites

- Java 11+
- Maven 3.8+
- [Syft](https://github.com/anchore/syft) (`brew install syft` on macOS)

---

## Build

```bash
mvn package -DskipTests
# Output: target/demo-java-war.war
```

---

## Syft SBOM Generation

### Table view (quick scan)
```bash
syft scan target/demo-java-war.war -o table
```

### CycloneDX JSON
```bash
syft scan target/demo-java-war.war -o cyclonedx-json=sbom.cdx.json
```

### SPDX JSON
```bash
syft scan target/demo-java-war.war -o spdx-json=sbom.spdx.json
```

### Pipe into Grype for vulnerability matching
```bash
syft scan target/demo-java-war.war -o cyclonedx-json | grype
```

---

## GitHub Actions

On every push to `main`, the workflows:

| Workflow | What it does |
|----------|--------------|
| `build-and-sbom.yml` | Builds WAR, runs Syft, uploads CycloneDX + SPDX SBOMs as artifacts |
| `codeql.yml` | Runs CodeQL Java analysis, uploads results to GitHub Security tab |

SBOM artifacts are downloadable from the **Actions** tab → latest run → **Artifacts**.

---

## GHAS Features Demonstrated

### Dependabot Alerts
Navigate to **Security → Dependabot alerts** to see all CVEs detected in `pom.xml`.

### CodeQL Code Scanning
Navigate to **Security → Code scanning alerts** to see any findings from CodeQL's Java analysis.

### Secret Scanning
Enabled by default for public repositories — any accidentally committed credentials will appear under **Security → Secret scanning alerts**.
