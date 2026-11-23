# Spring Boot

Read when designing, writing, or changing API endpoints.

- **One application class** with `@SpringBootApplication`. Configuration lives in `application.properties`, with a profile file per environment and secrets from environment variables.
- **Thin controllers.** A `@RestController` under `/api/v1/<resource>` parses input, calls one service method, and returns the result. No business logic and no data access.
- **Records in and out.** Request bodies are records with Jakarta Validation annotations, validated with `@Valid`. Responses are records too. Never expose entities.
- **Declare the contract.** Return the record directly and set `@ResponseStatus(HttpStatus.CREATED)` for creation. Use `ResponseEntity` only when headers must be set.
- **Constructor injection** of services, which are `@Service` beans holding the rules. Repositories and clients are injected into services, never into controllers.
- **Typed settings.** When the app has configuration, bind it to a `@ConfigurationProperties` record and enable it with `@ConfigurationPropertiesScan`.
- **One error shape.** Services throw `AppException` subclasses. A `@RestControllerAdvice` that extends `ResponseEntityExceptionHandler` returns RFC 9457 `ProblemDetail` for them, keeps Spring's own 400, 404, and 405 problems, and maps anything else to a generic 500 that logs the stack trace.
- **Validation errors** come from the base handler as 400 problems. Do not re-implement them.
- **Health.** A demo exposes `GET /health` from a small controller. An application adds `spring-boot-starter-actuator` and uses `/actuator/health`.
- **Data access.** An application uses Spring Data with Flyway migrations and Testcontainers tests. A demo keeps an in-memory store behind a service, guarded with a lock around each check and the write that depends on it.
- **Concurrency.** Enable virtual threads with `spring.threads.virtual.enabled=true`. Never block on futures inside a request.
- **REST conventions.** Plural nouns (`/users/{userId}`) and the right status codes: 201 created, 204 deleted, 404 missing, 409 conflict, 400 invalid. An application pages every list endpoint with `page` and `size`.

## Pattern

```java
/** HTTP endpoints for users. Parses input, calls the service, returns the result. */
@RestController
@RequestMapping("/api/v1/users")
public class UserController {

    private final UserService userService;

    public UserController(UserService userService) {
        this.userService = userService;
    }

    /** Register a new user. */
    @PostMapping
    @ResponseStatus(HttpStatus.CREATED)
    UserResponse createUser(@Valid @RequestBody UserCreate newUser) {
        return userService.createUser(newUser);
    }

    /** Return one user by id. */
    @GetMapping("/{userId}")
    UserResponse readUser(@PathVariable long userId) {
        return userService.getUser(userId);
    }
}
```
