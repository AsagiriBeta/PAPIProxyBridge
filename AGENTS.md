# AGENTS.md

## Cursor Cloud specific instructions

### Project overview
PAPIProxyBridge is a Gradle multi-module Minecraft plugin/library that bridges PlaceholderAPI from backend servers to proxy servers. Modules: `common`, `proxy`, `bukkit`, `bungee`, `velocity`, `fabric` (with MC version subprojects).

### Java requirements
- **JDK 21** is the primary runtime (set in `gradle.properties`).
- **JDK 17** must also be installed — the `common` and `fabric:1.20.1` modules declare `java { toolchain { languageVersion = JavaLanguageVersion.of(17) } }`. Without JDK 17, the build fails with a toolchain resolution error.

### Build, test, and lint
- Build: `./gradlew clean build` (runs license checks and compiles all modules; outputs to `target/`).
- Test: `./gradlew test` (JUnit 5; there are currently no test sources, so all test tasks report `NO-SOURCE`).
- Lint / license headers: `./gradlew licenseFormat` (auto-fix) / `./gradlew checkLicenses` (verify).
- Default tasks are `licenseFormat` and `build`.

### E2E testing
Full end-to-end testing requires Minecraft server infrastructure (Paper/Spigot + PlaceholderAPI + BungeeCord/Velocity proxy), which cannot be automated in a standard CI/dev environment. Use `./gradlew :bukkit:runServer` to spin up a Paper 1.21.11 test server for the bukkit module.

### Gotchas
- The Fabric Loom plugin downloads and decompiles Minecraft jars on first build, which can take several minutes. Subsequent builds are cached.
- Kotlin module metadata warnings (`incompatible version of Kotlin`) during Fabric builds are benign and can be ignored.
- Deprecation warnings about Gradle 9.0 compatibility are from upstream plugins and are not actionable.
- The Velocity module compiles with `--release 21` (not 17) because `velocity-api:3.5.0-SNAPSHOT` requires JVM 21+.
- Fabric 1.21.11+ and 26.1.2+ subprojects cannot be added without upgrading the Essential Gradle Toolkit and Architectury Loom (the current loom 1.9 does not support 1.21.11 yarn's unpick format). Fabric 26.1.2 additionally requires Java 25, Loom 1.15, and Mojang mappings (no Yarn).
