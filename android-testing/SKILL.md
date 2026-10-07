---
name: android-testing
description: >
  Defines testing standards for Android projects, including JUnit 5, Turbine,
  Mockito or MockK, descriptive backtick test names, parameterized tests, and
  flow testing patterns. Use this skill when creating, reviewing, or refactoring
  unit tests for ViewModels, use cases, mappers, calculators, repositories, or
  other pure Kotlin Android components.
---

# Android Testing

Use this skill to create clear, behavior-focused tests for Android code.

## 1. Testing principles

Test behavior, not implementation.

Priorities:

- correctness
- readability
- explicit expectations
- stable test names
- complete assertions
- easy diagnosis when failures happen

Rules:

- Prefer unit tests for business logic, flow orchestration, mappers, and calculators.
- Keep tests deterministic.
- Avoid tests that depend on Android framework behavior unless they are true instrumentation tests.
- Verify the full expected result when the behavior has meaningful structure.
- Use the same naming style across the project.

## 2. Test naming

Use descriptive backtick names:

```kotlin
@Test
fun `When amount is valid then it returns parsed value`() { }
```

Rules:

- Describe the condition first.
- Describe the expected result second.
- Keep names readable without extra commentary.
- Use one test per scenario.
- Prefer names that read like a sentence.

## 3. System under test naming

Always name the subject being tested `sut`.

Example:

```kotlin
private lateinit var sut: ExchangeViewModel
```

Rules:

- Use `sut` consistently in every test file.
- Keep all other mocks and test fixtures named by their actual role.
- Avoid vague names for dependencies.

## 4. Test structure

Organize tests into four blocks:

1. create objects and mocks
2. stub functions
3. execute tested function
4. assert result

Example:

```kotlin
private val repository = mockk<ExampleRepository>()
private val sut = ExampleUseCase(repository)

@Test
fun `when repository returns data then ui state is loaded`() = runTest {
    // create objects and mocks
    val text = "mocked text"
    val textA = "A"
    val textB = "B"
    val param = mockk<Param>() {
        every { this.text } returns text
    }
    val expected = listOf(textA, textB)

    // stub functions
    every { repository.getData(param) } returns flowOf(Result.success(listOf(textA, textB)))

    // execute tested function
    val actual = sut(text)

    // assert result
    assertEquals(expected = expected, actual = actual.first().getOrThrow())
}
```

Rules:

- Keep arrange, act, and assert separated.
- Use `expected` name for the object expected to be the result.
- Use `actual` name for the resultof the tested function.
- Assert expected against actual.
- Keep mock setup close to the scenario it supports.
- Test one behavior per test unless a group of values is intentionally parameterized.

## 5. Assertion style

Prefer complete assertions over partial checks.

Good:

```kotlin
assertEquals(expected = expectedUiState, actual = actualUiState)
```

Better when collections matter:

```kotlin
assertEquals(expected = expectedItems, actual = actualItems)
```

Use partial assertions only when the test is explicitly about a small part of the output and the rest is irrelevant.

Rules:

- assert full object values when practical
- verify ordering when ordering matters
- verify contents when contents matter
- verify exact events when events matter
- compare expected and actual explicitly

## 6. Preferred tools

Use the following stack:

- JUnit 5
- Turbine for Flow testing
- MockK or Mockito-Kotlin for mocking
- `runTest` for coroutine tests

Rules:

- Use `@Test` from JUnit 5.
- Use `runTest` for suspending code and Flow collection.
- Use Turbine for emissions, completion, cancellation, and errors.
- Use MockK or Mockito-Kotlin consistently within the same project.

## 7. Flow testing with Turbine

Use Turbine whenever testing Flow or StateFlow behavior.

Common assertions:

- `awaitItem()`
- `awaitComplete()`
- `awaitError()`
- `expectNoEvents()`

Example:

```kotlin
@Test
fun `When flow emits values, then it receives them in order`() = runTest {
    val flow = flowOf(1, 2, 3)

    flow.test {
        assertEquals(expected = 1, actual = awaitItem())
        assertEquals(expected = 2, actual = awaitItem())
        assertEquals(expected = 3, actual = awaitItem())
        awaitComplete()
    }
}
```

Rules:

- Test emission order.
- Test failure behavior.
- Test cancellation when relevant.
- Test replay and state updates when using StateFlow or SharedFlow.
- Do not ignore intermediate emissions if they are part of the contract.

## 8. Parameterized and generated tests

Use parameterized approaches whenever multiple input and output combinations exist.

Prefer:

- `@ParameterizedTest`
- `@MethodSource`
- `@CsvSource`
- `@EnumSource`
- `@TestFactory` for dynamic tests

Use `@TestFactory` or dynamic tests when scenarios are naturally data driven and the body should stay compact.

Example:

```kotlin
@TestFactory
fun `When amounts are invalid, then parser returns zero`() = listOf(
    "a" to BigDecimal.ZERO,
    "" to BigDecimal.ZERO,
).map { (input, expected) ->
    dynamicTest("input = $input") {
        val actual = sut.parse(input)
        assertEquals(expected = expected, actual = actual)
    }
}
```

Rules:

- Use parameterized tests for repeated logic with different values.
- Use dynamic tests when the cases are easy to list as data.
- Keep scenario names meaningful.
- Prefer small, readable data sets.

## 9. Coroutine and Flow test patterns

Use `runTest` for all coroutine based tests.

Rules:

- avoid real delays when possible
- inject dispatchers or use test dispatchers
- advance virtual time when needed
- cancel collectors when a test scenario requires it
- keep test setup isolated from production threading behavior

Useful checks:

- state updates
- retry behavior
- cancellation behavior
- error recovery
- replay behavior
- ordering of emissions

## 10. ViewModel testing

Test ViewModels through their public API.

Prefer testing:

- `onAction`
- `uiState`
- `uiEvents`

Rules:

- do not assert private implementation details
- assert the emitted state sequence
- assert side effects as events
- use Turbine for state and event flows
- keep user scenarios readable through test names

## 11. Repository, use case, and mapper tests

### Use cases

Test the input to output contract.

### Mappers

Test exact mapped values.

### Repositories

Test interaction with dependencies and returned domain results.

Rules:

- mock external dependencies
- verify repository output shape
- test both success and failure paths
- test fallback behavior when relevant

## 12. Review checklist

Before merging a test, confirm:

- the test name explains the behavior
- `sut` is used consistently
- arrange, act, and assert are separated
- assertions compare expected and actual clearly
- data driven cases use parameterized or dynamic tests when appropriate
- Flow tests use Turbine
- coroutine tests use `runTest`
- the test verifies behavior instead of internal implementation

If a test is hard to read, hard to extend, or tied too closely to implementation details, rewrite it.