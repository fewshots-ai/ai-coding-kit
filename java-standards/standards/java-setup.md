# Java, Maven, and Spring Boot setup

Read when creating a project, or changing dependencies or tooling.

## Rules

- **Latest LTS Java.** Set `java.version` to the current LTS release (25 at the time of writing). Run Maven on that same LTS JDK: the formatter and Error Prone use compiler internals and break on a newer non-LTS JDK. If `java -version` shows the wrong JDK, ask the user to point `JAVA_HOME` at the LTS one.
- **Maven through the wrapper.** Commit `mvnw`, `mvnw.cmd`, and `.mvn/`. Every command is `./mvnw ...`, so everyone builds with the same Maven.
- **Spring Boot from Spring Initializr** with the `web` and `validation` dependencies. Let the Boot parent manage every Spring and test library version; pin only plugins the parent does not know.
- **`pom.xml` is the single source** for dependencies, plugins, and compiler flags. Keep it readable: properties for versions, one plugin per concern.
- **Spotless with palantir-java-format** formats. **Error Prone** with `-Xlint:all -Werror` lints at compile time. Do not add Checkstyle or PMD unless the project already uses them.
- **JUnit, AssertJ, and Mockito** come with the Boot test starter. Testcontainers for an application that talks to a real database.
- **Never commit** `target/`, IDE files, or `.env` files. Secrets come from environment variables.

## Command map

| Task | Command |
|---|---|
| Create a project | `curl` the Spring Initializr archive, as below |
| Format, then build with lint and tests | `./mvnw -q spotless:apply`, then `./mvnw -q verify` |
| Check formatting without changing files | `./mvnw -q spotless:check` |
| Run only the tests | `./mvnw -q test` |
| Run the app | `./mvnw -q spring-boot:run` |
| Build the runnable jar | `./mvnw -q package`, then `java -jar target/<name>.jar` |
| Add or remove a dependency | Edit `pom.xml`, then `./mvnw -q verify` |
| See available upgrades | `./mvnw -q org.codehaus.mojo:versions-maven-plugin:display-dependency-updates` |
| Inspect the dependency tree | `./mvnw -q dependency:tree` |

## New project

Run this in the project root, the folder that holds `AGENTS.md`. Replace the names in the query string.

```bash
curl -sS "https://start.spring.io/starter.tgz?type=maven-project&language=java&javaVersion=25&packaging=jar&groupId=com.example&artifactId=my-app&name=my-app&packageName=com.example.myapp&dependencies=web,validation" -o starter.tgz
tar -xzf starter.tgz && rm starter.tgz HELP.md
```

Then add the build settings below to `pom.xml`, create `.mvn/jvm.config`, and run the Definition of done once before writing code.

## Build settings

Add to `<properties>`:

```xml
<error-prone.version>2.51.0</error-prone.version>
<spotless.version>3.10.4</spotless.version>
<palantir-java-format.version>2.102.0</palantir-java-format.version>
```

Add to `<build><plugins>`, next to `spring-boot-maven-plugin`:

```xml
<plugin>
    <groupId>org.apache.maven.plugins</groupId>
    <artifactId>maven-compiler-plugin</artifactId>
    <configuration>
        <showWarnings>true</showWarnings>
        <compilerArgs>
            <arg>-Xlint:all,-processing,-serial</arg>
            <arg>-Werror</arg>
            <arg>-XDcompilePolicy=simple</arg>
            <arg>--should-stop=ifError=FLOW</arg>
            <arg>-Xplugin:ErrorProne</arg>
        </compilerArgs>
        <annotationProcessorPaths>
            <path>
                <groupId>com.google.errorprone</groupId>
                <artifactId>error_prone_core</artifactId>
                <version>${error-prone.version}</version>
            </path>
        </annotationProcessorPaths>
    </configuration>
</plugin>
<plugin>
    <groupId>com.diffplug.spotless</groupId>
    <artifactId>spotless-maven-plugin</artifactId>
    <version>${spotless.version}</version>
    <configuration>
        <java>
            <palantirJavaFormat>
                <version>${palantir-java-format.version}</version>
            </palantirJavaFormat>
            <removeUnusedImports/>
        </java>
    </configuration>
    <executions>
        <execution>
            <goals>
                <goal>check</goal>
            </goals>
            <phase>validate</phase>
        </execution>
    </executions>
</plugin>
```

`.mvn/jvm.config`, which Error Prone needs on Java 16 and later:

```text
--add-exports jdk.compiler/com.sun.tools.javac.api=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.file=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.main=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.model=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.parser=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.processing=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.tree=ALL-UNNAMED
--add-exports jdk.compiler/com.sun.tools.javac.util=ALL-UNNAMED
--add-opens jdk.compiler/com.sun.tools.javac.code=ALL-UNNAMED
--add-opens jdk.compiler/com.sun.tools.javac.comp=ALL-UNNAMED
```
