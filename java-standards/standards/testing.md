# Testing

Read when writing tests or judging whether work is done.

- **JUnit, AssertJ, and MockMvc** from the Boot test starter. Tests live in `src/test/java`, mirroring the main packages. Run them with `./mvnw -q test`.
- **Test behavior, not implementation.** One behavior per test, named for it: `createUserRejectsDuplicateEmail`. No assertions on private state.
- **Arrange, act, assert**, separated by blank lines. No loops or conditionals in a test. Use `@ParameterizedTest` for variations.
- **Test the API through MockMvc** with `@SpringBootTest` and `@AutoConfigureMockMvc`. Assert the status and the JSON body, including the problem detail shape.
- **Test services directly** as plain objects with fakes passed through the constructor. Use Mockito only for collaborators that are expensive to fake.
- **Fresh state per test.** Reset in-memory stores in `@BeforeEach`, or mark the test class `@DirtiesContext` when the Spring context itself holds state.
- **Isolate.** No real network or services. An application uses Testcontainers for the database and WireMock or a fake for external APIs.
- **AssertJ assertions**, `assertThat(user.email()).isEqualTo("ada@example.com")`, read as sentences. No bare `assertTrue` on a comparison.
- **Fast and deterministic.** No `Thread.sleep`, no order dependence, no clock or random values without injection.
- **Trace to requirements.** Every acceptance criterion has a test. Every bug fix starts with a failing test that reproduces the bug.
- **Coverage is a signal.** An application adds JaCoCo and watches the report. Cover services fully before chasing a number.
- **Warnings are work.** Fix deprecation warnings instead of silencing them.
- **Never weaken a test to get green.** Fix the code, or explain why the test was wrong.
