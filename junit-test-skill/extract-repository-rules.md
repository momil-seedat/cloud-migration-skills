---
name: extract-java-test-rules
description: |
  Analyze a Java repository and extract its existing testing conventions into a module-level test-module.md file. Use when repository-specific Java/JUnit testing rules need to be documented for the java-unit-testing skill.
---
# Extract Java Test Repository Rules

Analyze the repository's existing Java tests and build configuration.

Create a `test-module.md` file containing **only repository-observed testing conventions**.

## Inspect

Inspect:

* `pom.xml`
* `build.gradle` / `build.gradle.kts`
* Java version
* JUnit dependencies
* Mockito dependencies
* Assertion libraries
* Test source directories
* Existing unit tests
* Test utilities
* Fixtures/builders
* Parameterized tests
* Spring tests
* Kafka tests
* Database tests
* Test naming
* Maven/Gradle test commands
* Coverage configuration

Inspect representative examples from each relevant module.

## Do Not Assume

Only document conventions supported by repository evidence.

If different modules use different conventions, document them separately.

Do not impose generic best practices.

Do not modify production code.

Do not modify existing tests.

Do not modify build configuration.

## Output

Create:

```text
<module>/test-module.md
```

Use this structure:

```markdown
# Test Module Rules

## Java
- Version:
- Relevant configuration:

## JUnit
- Version:
- Conventions:

## Mockito
- Version:
- Mocking conventions:

## Assertions
- Library:
- Conventions:

## Test Structure
- Naming:
- Setup:
- Fixtures:
- Parameterized tests:

## Framework-Specific Rules

### Spring

### Kafka

### Database

### HTTP/API

## Coverage
- Tool:
- Command:
- Configuration:

## Existing Utilities
- Utility:
- Location:
- When to reuse:

## Module-Specific Rules

## Evidence
- Representative test files:
- Relevant build configuration:
```

If a rule cannot be established from the repository, write:

`Not established in repository.`

Do not invent a rule.
