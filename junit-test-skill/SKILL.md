---
name: junit5-testing-java
description: Generate meaningful, deterministic JUnit 5 unit tests for Java production code changed in the current Git diff. Use when adding tests, improving test coverage, or validating Java code changes. Focus only on changed or directly affected production code, target 90 percent or higher line coverage where practical, and run a test-fix-test loop until relevant tests pass.
---

# Java JUnit 5 Unit Testing

Generate **meaningful, deterministic, maintainable JUnit 5 unit tests** for Java code.

## Core Rule

Work on the **current Git diff**, not the entire repository.

The default scope is:

> Changed production code and tests directly affected by those changes.

Do not expand the scope to unrelated production classes or repository-wide coverage unless explicitly requested.

---

## 1. Inspect Before Writing

First inspect the Git changes:

```bash
git status --short
git diff --name-only
git diff --stat
git diff
```

Identify:

- Added production classes
- Modified production classes
- Modified methods
- Added branches/conditions
- Changed exception handling
- Changed dependencies/interactions
- Existing tests affected by the changes

Then inspect existing tests for the affected classes/modules.

Look for:

- JUnit version
- Mockito usage
- Assertion library
- Test naming
- Test fixtures/builders
- Test utilities
- `@Nested`
- `@ParameterizedTest`
- Existing setup patterns
- Module-specific conventions

If `test-module.md` exists in the relevant module, read it and follow its repository-specific conventions.

Do not invent repository conventions.

---

## 2. Determine Test Strategy

For every changed class/method, determine:

1. What behavior changed?
2. What scenarios can exercise that behavior?
3. Which dependencies should be mocked?
4. Which dependencies should remain real?
5. Which edge/error cases are meaningful?
6. What existing test utilities should be reused?

Prefer the **smallest appropriate test strategy**.

Use unit tests by default.

Do not introduce Spring context, databases, Kafka brokers, containers, HTTP servers, or other integration infrastructure merely to increase coverage.

---

## 3. JUnit 5 Conventions

Use JUnit 5 conventions when compatible with the module.

Prefer:

```java
@Test
@BeforeEach
@AfterEach
@Nested
@ParameterizedTest
@ValueSource
@CsvSource
@MethodSource
@NullSource
@EmptySource
@NullAndEmptySource
```

Prefer descriptive behavior-oriented test names:

```text
shouldReturnUserWhenUserExists
shouldRejectTransferWhenBalanceIsInsufficient
shouldThrowExceptionWhenAccountDoesNotExist
```

Structure tests clearly:

```text
Arrange
Act
Assert
```

- Use `assertAll` when multiple assertions belong to the same behavior.
- Use `assertThrows` for expected exceptions.
- Prefer precise assertions over vague assertions.

---

## 4. Meaningful Test Requirement

Every test must validate meaningful behavior.

Before adding a test, ask:

> What production behavior does this test protect?

Avoid tests that only execute lines without validating behavior.

Do not add tests solely because a coverage report identifies an uncovered line.

Prioritize:

- Happy path
- Important branches
- Boundary conditions
- Invalid input
- Error handling
- Exception behavior
- Empty/null behavior when supported
- External dependency failures
- Business rules

Do not test implementation details that are irrelevant to observable behavior.

Avoid excessive interaction verification.

---

## 5. Mocking Rules

Mock dependencies, not the class under test.

Prefer existing dependency-injection patterns.

Do not mock:

- Simple value objects
- Collections
- Deterministic pure functions
- Simple DTOs
- The class under test

Avoid verifying every method call.

Verify interactions when the interaction itself represents meaningful behavior.

Reuse existing mocks, fixtures, builders, and test utilities where available.

---

## 6. Static Classes

### Deterministic Static Utility

For deterministic static utility methods, prefer testing the real implementation directly.

Example categories:

```text
normal input
boundary input
empty input
invalid input
null input, when supported
```

Do not mock deterministic static methods merely to make tests easier.

### Static Dependencies

If production code calls a static dependency:

1. Prefer the real implementation when deterministic and inexpensive.
2. Avoid static mocking when unnecessary.
3. If static mocking is required, use the project's existing Mockito/JUnit 5 conventions.
4. Keep static mock scope limited to the test.
5. Ensure the static mock is always closed.

Never introduce static mocking solely for coverage.

---

## 7. Determinism and Flakiness

Tests must be deterministic.

Do not introduce dependencies on:

- Current system time
- Randomness
- Thread scheduling
- Network availability
- External services
- Real databases
- Real Kafka brokers
- Execution order
- Shared mutable state
- Environment-specific configuration

Avoid:

```java
Thread.sleep(...)
```

Do not use timing-based assertions to synchronize asynchronous code.

If time is relevant, control/inject the clock when the existing design permits it.

If randomness is relevant, make it deterministic.

Tests must be independently executable.

---

## 8. Coverage Target

Target:

> **90%+ line coverage for changed production code**

Do not target 90% coverage across the entire repository unless explicitly requested.

After tests pass:

1. Run the repository's existing coverage command.
2. Inspect coverage for changed production files.
3. Identify meaningful uncovered code.
4. Add tests for meaningful uncovered behavior.
5. Run tests again.
6. Re-check coverage.

Do not create meaningless tests just to reach 90%.

If 90% is impractical, report the reason. Examples:

- Generated code
- Defensive code that cannot realistically be reached
- Framework-managed code
- Environment-specific behavior
- Production design preventing isolated testing

---

## 9. Test → Run → Fix Loop

Never finish immediately after generating test code.

After creating or modifying tests:

### Step 1 — Run

Run the smallest relevant test command.

For Maven, prefer the module/test scope used by the repository.

Example:

```bash
mvn -pl <module> test
```

### Step 2 — Diagnose

If compilation or tests fail, determine whether the problem is:

- Incorrect test setup
- Incorrect mock configuration
- Incorrect assertion
- Incorrect expected behavior
- Production regression
- Repository configuration issue

### Step 3 — Fix

Fix the appropriate problem.

- Do not weaken assertions simply to make tests pass.
- Do not modify production code unless the user explicitly asks for production changes or the test reveals a genuine defect requiring correction.

### Step 4 — Run Again

Repeat:

```text
write → run → inspect → fix → run
```

until the relevant tests pass.

### Step 5 — Coverage

Once tests pass, run coverage and add meaningful missing cases where necessary. Then run the tests again.

---

## 10. Git Diff Boundary

Use Git to determine scope.

Prefer:

```bash
git diff
git diff --name-only
git diff --unified=80
```

Do not:

- Rewrite unrelated tests
- Add tests for untouched classes
- Refactor unrelated production code
- Change unrelated configuration
- Add repository-wide tests
- Increase legacy coverage unrelated to the diff

Existing tests may be modified if they directly correspond to changed production behavior.

---

## 11. Repository Rules

Repository-specific rules are optional.

If the relevant module contains:

```text
test-module.md
```

read it before generating tests.

Repository rules can override generic conventions where appropriate. Examples:

```text
JUnit version
Mockito version
Maven commands
Assertion library
Test naming
Fixture conventions
Spring testing rules
Kafka testing rules
Database testing rules
Coverage command
Module-specific patterns
Existing test utilities
```

Do not assume a repository convention that is not documented or observable.

---

## 12. Final Verification

Before finishing, verify:

- Changed production code was identified from Git diff.
- Tests target changed/relevant behavior.
- Existing test conventions were inspected.
- JUnit 5 conventions are followed.
- Tests are meaningful.
- Important branches are covered.
- Important error paths are covered.
- Static deterministic code is tested directly where appropriate.
- Static mocking is avoided unless justified.
- No flaky synchronization was introduced.
- No unnecessary integration infrastructure was introduced.
- No unnecessary mocks were introduced.
- Tests are independent and deterministic.
- Relevant tests were executed.
- Failures were fixed and tests rerun.
- Coverage was checked.
- Changed production code reaches approximately 90%+ line coverage where practical.
- No unrelated files were modified.

Final response should summarize:

```text
Tests added/modified:
<list>

Coverage:
<coverage for changed code>

Validation:
<test command>
<result>

Remaining gaps:
<if any>
```
