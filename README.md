# xir-sax

[![Build](https://github.com/jurgenei/xir-sax/actions/workflows/ci.yml/badge.svg)](https://github.com/jurgenei/xir-sax/actions/workflows/ci.yml)
[![Release](https://github.com/jurgenei/xir-sax/actions/workflows/release.yml/badge.svg)](https://github.com/jurgenei/xir-sax/actions/workflows/release.yml)
[![Coverage CI](https://github.com/jurgenei/xir-sax/actions/workflows/coverage.yml/badge.svg)](https://github.com/jurgenei/xir-sax/actions/workflows/coverage.yml)
[![CodeQL](https://github.com/jurgenei/xir-sax/actions/workflows/codeql.yml/badge.svg)](https://github.com/jurgenei/xir-sax/actions/workflows/codeql.yml)
[![Dependency Check](https://github.com/jurgenei/xir-sax/actions/workflows/dependency-check.yml/badge.svg)](https://github.com/jurgenei/xir-sax/actions/workflows/dependency-check.yml)
[![SpotBugs Security](https://github.com/jurgenei/xir-sax/actions/workflows/spotbugs-security.yml/badge.svg)](https://github.com/jurgenei/xir-sax/actions/workflows/spotbugs-security.yml)
[![Dependabot](https://img.shields.io/badge/dependabot-enabled-025E8C?logo=dependabot)](https://github.com/jurgenei/xir-sax/security/dependabot)
[![Coverage](https://codecov.io/gh/jurgenei/xir-sax/graph/badge.svg?branch=main)](https://codecov.io/gh/jurgenei/xir-sax?branch=main)
[![Maven Central](https://img.shields.io/maven-central/v/name.jurgenei/xir-sax.svg)](https://search.maven.org/artifact/name.jurgenei/xir-sax)
[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Java](https://img.shields.io/badge/java-21+-green.svg)](https://www.oracle.com/java/)
[![Gradle](https://img.shields.io/badge/gradle-9.5+-blue.svg)](https://gradle.org/)

SAX parser, serializer, XMLReader for bracket-based XML/XDM S-expression format.

## Coordinates

```text
name.jurgenei:xir-sax:<version>
```

## Namespace

- Java package: `name.jurgenei.xir`
- Internal XDM bridge namespace URI: `urn:name.jurgenei.xml:xdm`
- Internal XDM bridge prefix: `xdm`

## Included APIs

- `XirParser` - parses S-expression input into SAX events
- `XirSerializer` - renders SAX events as canonical or legacy S-expression
- `XirReader` - `XMLReader` facade over parser

## Canonical syntax excerpt

- `()` nodes
- `{}` associative structures
- `[]` sequences
- `.` document head
- `?` processing instruction head
- `!` comment head

Examples:

- `(book (title "XML"))`
- `(book { id "b1" xmlns:m "urn:math" } (m:title "XML"))`
- `(. { version "1.0" encoding "UTF-8" } (book))`
- `{ name John age 42 }`
- `[ A B C ]`
- `(xs:boolean true)`

Notes:

- Bare `{...}` and `[...]` are canonical map/sequence values.
- Legacy `(xdm:map ...)` / `(xdm:array ...)` input is still accepted by parser for compatibility.
- To disambiguate element with first child map, use explicit empty attrs block: `(element {} { key value })`.
- XML in `http://www.w3.org/2005/xpath-functions` (`map`, `array`, `list`, `string`, `number`, `boolean`) is recognized in SAX serialization path.
- `XirSerializer` autotyping mode defaults to strict-string and supports optional promote mode.

## Run tests

```bash
./gradlew test
```

## Test Coverage

Generate coverage report and enforce current minimum line coverage baseline (>= 0%):

```bash
./gradlew coverage
```

Coverage report outputs:

- XML: `build/reports/jacoco/test/jacocoTestReport.xml`
- HTML: `build/reports/jacoco/test/html/index.html`

CI coverage workflow: `.github/workflows/coverage.yml`

Codecov upload uses GitHub OIDC in CI (`codecov/codecov-action`) and reads `build/reports/jacoco/test/jacocoTestReport.xml`.

## Building

```bash
./gradlew build
```

Required Java version: **21+**

## GitHub Actions

- `ci.yml` runs build and unit tests on push and pull request
- `coverage.yml` runs coverage task, uploads JaCoCo report artifact, uploads coverage to Codecov
- `codeql.yml` runs GitHub CodeQL static analysis
- `dependency-check.yml` runs OWASP Dependency-Check and uploads reports
- `spotbugs-security.yml` runs SpotBugs + FindSecBugs and uploads report
- `release.yml` publishes signed artifacts using Sonatype Central Publisher API
- `dependabot.yml` enables weekly updates for Gradle and GitHub Actions

## Maven Central publishing

### Local release prereqs

1. Maven Central credentials (`mavenCentralUsername`, `mavenCentralPassword`) in `~/.gradle/gradle.properties`
2. GPG key installed locally (`gpg --list-secret-keys` shows signing key)
3. Project version without `-SNAPSHOT`

Example `~/.gradle/gradle.properties`:

```properties
mavenCentralUsername=YOUR_MAVEN_CENTRAL_TOKEN_USERNAME
mavenCentralPassword=YOUR_MAVEN_CENTRAL_TOKEN_PASSWORD
signingKey=YOUR_ASCII_ARMORED_PRIVATE_KEY
signingPassword=YOUR_SIGNING_KEY_PASSPHRASE
signingKeyId=YOUR_GPG_KEY_ID
```

Publish:

```bash
./gradlew clean packageCentralBundle
```

### CI release secrets

Configure repository secrets for `release.yml`:

- `MAVEN_CENTRAL_USERNAME`
- `MAVEN_CENTRAL_PASSWORD`
- `SIGNING_KEY` (ASCII-armored private key)
- `SIGNING_PASSWORD`
- `SIGNING_KEY_ID` (optional)

## License

[MIT](LICENSE)
