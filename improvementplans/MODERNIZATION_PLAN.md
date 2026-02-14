# INGenious Framework — Phased Modernization Plan

**Created:** February 13, 2026  
**Current Version:** 2.3.1  
**Current Java:** 11  
**Target Java:** 21 LTS  

---

## Phase 1 — Foundation & Safety Net (Week 1-2)

*Low effort, high impact. Stabilize the build before making changes.*

| # | Task | Effort | Files |
|---|---|---|---|
| 1.1 | **Pin all `LATEST` versions** to specific tested versions (Playwright, Appium, ExtentReports, JavaFaker, Kafka, Avro, IBM MQ, Oracle JDBC) | 1 day | Parent `pom.xml`, `Engine/pom.xml` |
| 1.2 | **Add OWASP dependency-check plugin** to parent POM | 0.5 day | `pom.xml` |
| 1.3 | **Add JaCoCo plugin** to parent POM for coverage visibility | 0.5 day | `pom.xml` |
| 1.4 | **Centralize plugin versions** — move `maven-compiler-plugin` to `<pluginManagement>` in parent; remove duplicates from child POMs | 1 day | All 6 module POMs |
| 1.5 | **Remove duplicate dependency** — `appium java-client` listed twice in Engine POM | 0.5 hour | `Engine/pom.xml` |
| 1.6 | **Enhance CI/CD pipeline** — add Maven dependency caching, test result publishing, coverage report upload | 1 day | `.github/workflows/maven.yml` |

**Exit Criteria:** Build is reproducible, security scan runs, coverage baseline established.

---

## Phase 2 — Java 21 LTS Upgrade (Week 2-3)

*Unlock modern language features and performance improvements.*

| # | Task | Effort | Files |
|---|---|---|---|
| 2.1 | **Update Java version** in parent POM: `<java.version>21</java.version>` | 0.5 hour | `pom.xml` |
| 2.2 | **Upgrade maven-compiler-plugin** from 3.8.1 → 3.12+ | 0.5 hour | `pom.xml` |
| 2.3 | **Upgrade JavaFX** from 17.0.2 → 21.x (align with Java 21) | 0.5 day | `pom.xml`, `IDE/pom.xml` |
| 2.4 | **Update CI pipeline** to use JDK 21 (Temurin); add matrix build for 11+21 during transition | 0.5 day | `.github/workflows/maven.yml` |
| 2.5 | **Fix compilation issues** — resolve any Java 21 incompatibilities (removed APIs, new warnings) | 1-2 days | Various source files |
| 2.6 | **Run full test suite** and fix regressions | 1 day | Test files |

**Exit Criteria:** Project builds and all tests pass on Java 21.

---

## Phase 3 — Critical Dependency Upgrades (Week 3-5)

*Address EOL and security-vulnerable dependencies.*

| # | Task | Effort | Files |
|---|---|---|---|
| 3.1 | **Upgrade Jetty** 9.4 → 12.x — update API calls in `DashBoardServer.java` and related websocket/servlet code | 2-3 days | `IDE/pom.xml`, ~3-5 IDE source files |
| 3.2 | **Replace `javax.mail:1.4.7`** → `jakarta.mail:2.x` — update import statements | 1 day | `Engine/pom.xml`, mail-related source files |
| 3.3 | **Remove `commons-lang:2.6`** — migrate any `org.apache.commons.lang.*` imports to `org.apache.commons.lang3.*` | 1 day | `pom.xml`, affected source files |
| 3.4 | **Upgrade Jackson** 2.15.2 → 2.17.x | 0.5 day | `pom.xml` |
| 3.5 | **Upgrade Log4j** 2.17.2 → 2.23.x | 0.5 day | `pom.xml` |
| 3.6 | **Upgrade TestNG** 7.7.1 → 7.10.x | 0.5 day | `pom.xml`, test files |
| 3.7 | **Upgrade Guava** 32.0.1 → 33.x | 0.5 day | `pom.xml` |
| 3.8 | **Upgrade Gherkin** 5.0.0 → 28.x — major version jump, expect API changes | 2 days | `IDE/pom.xml`, BDD source files |

**Exit Criteria:** Zero CVEs from OWASP scan, all dependencies on actively maintained versions.

---

## Phase 4 — Code Modernization (Week 5-8)

*Improve code quality using Java 21 features and modern patterns.*

| # | Task | Effort | Files |
|---|---|---|---|
| 4.1 | **Migrate `java.util.Date`/`SimpleDateFormat`** → `java.time` APIs | 3-4 days | ~38 files across all modules |
| 4.2 | **Fix raw types** — add diamond operator `<>` and proper generics | 1 day | ~16 files |
| 4.3 | **Replace `json-simple`** with Jackson (already present) | 2 days | Files using `org.json.simple` |
| 4.4 | **Replace JavaFaker** (`com.github.javafaker`, unmaintained) → `net.datafaker:datafaker` | 1 day | `Engine/pom.xml`, faker usage files |
| 4.5 | **Adopt Java 21 language features** where beneficial: text blocks, pattern matching, records, switch expressions | 2-3 days | Opportunistic across modules |
| 4.6 | **Replace legacy collections** — `Vector` → `ArrayList`, `StringBuffer` → `StringBuilder` where appropriate | 1 day | IDE module files |

**Exit Criteria:** No raw type warnings, no deprecated API usage, modern Java patterns adopted.

---

## Phase 5 — HTTP Client Modernization (Week 8-10)

*Consolidate HTTP stack on modern Java built-in client.*

| # | Task | Effort | Files |
|---|---|---|---|
| 5.1 | **Migrate Apache HttpClient 4.x** → `java.net.http.HttpClient` in reporting/sync layer | 3-4 days | ~24 files (BasicHttpClient, AbstractHttpClient, AzureHttpClient, ReportPortalClient, etc.) |
| 5.2 | **Migrate `HttpURLConnection`** → `java.net.http.HttpClient` in Slack integration | 0.5 day | `Slack.java` |
| 5.3 | **Remove Apache HC 4.x dependencies** (`httpclient`, `httpcore`) from POMs | 0.5 day | `Engine/pom.xml`, `IDE/pom.xml` |
| 5.4 | **Verify and test** all HTTP integrations (Azure, ReportPortal, Slack, Lambda Test) | 2 days | Integration tests |

**Exit Criteria:** Zero dependency on Apache HttpClient 4.x, all HTTP communication via `java.net.http`.

---

## Phase 6 — Code Quality Enforcement (Week 10-11)

*Prevent regressions with automated quality gates.*

| # | Task | Effort | Files |
|---|---|---|---|
| 6.1 | **Add SpotBugs plugin** with baseline exclusion file | 1 day | `pom.xml` |
| 6.2 | **Add Checkstyle plugin** with project-specific rules | 1 day | `pom.xml`, new `checkstyle.xml` |
| 6.3 | **Add Spotless plugin** for consistent code formatting | 0.5 day | `pom.xml` |
| 6.4 | **Set coverage thresholds** in JaCoCo (start at measured baseline, incrementally increase) | 0.5 day | `pom.xml` |
| 6.5 | **Update CI pipeline** with quality gates — fail build on violations | 0.5 day | `.github/workflows/maven.yml` |
| 6.6 | **Write additional unit tests** — prioritize Engine and Datalib modules | Ongoing | New test files |

**Exit Criteria:** Quality gates enforced in CI, coverage trending upward.

---

## Phase 7 — DevOps & Packaging (Week 11-13)

*Enable cloud-native execution.*

| # | Task | Effort | Files |
|---|---|---|---|
| 7.1 | **Add Dockerfile** for headless test execution | 1 day | New `Dockerfile` |
| 7.2 | **Add Docker Compose** for local development environment | 1 day | New `docker-compose.yml` |
| 7.3 | **Remove deprecated `maven-eclipse-plugin`** | 0.5 hour | `Engine/pom.xml` |
| 7.4 | **Upgrade exec-maven-plugin** 1.6.0 → 3.x | 0.5 day | `Engine/pom.xml`, `IDE/pom.xml` |
| 7.5 | **Rename `TestData - Csv` module directory** to `testdata-csv` for cross-platform compatibility | 0.5 day | Directory, all POMs referencing it |
| 7.6 | **Add multi-JDK CI matrix** (21 + future 25 LTS readiness) | 0.5 day | `.github/workflows/maven.yml` |

**Exit Criteria:** Containerized execution available, clean project structure.

---

## Timeline Summary

```
Week  1-2   ████ Phase 1: Foundation & Safety Net
Week  2-3   ████ Phase 2: Java 21 Upgrade
Week  3-5   ████████ Phase 3: Critical Dependencies
Week  5-8   ████████████ Phase 4: Code Modernization
Week  8-10  ████████ Phase 5: HTTP Client Migration
Week 10-11  ████ Phase 6: Quality Enforcement
Week 11-13  ████████ Phase 7: DevOps & Packaging
```

**Total estimated effort:** ~10-13 weeks with 1 developer, ~6-8 weeks with 2 developers working in parallel (Phases 4+5 can overlap, Phases 6+7 can overlap).

---

## Key Principles

- **Each phase is independently shippable** — you can stop after any phase and have a better codebase
- **Phase 1 is mandatory** before all others — stabilizes the build foundation
- **Phase 2 should follow Phase 1** — Java 21 unlocks benefits in later phases
- **Phases 4 & 5 can run in parallel** — they touch different files
- **Run full test suite after each phase** — catch regressions early
