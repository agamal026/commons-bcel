# BCEL-238: BootstrapMethod argument ownership proof

This packet accompanies source commit [`4ec24e4`](https://github.com/agamal026/commons-bcel/commit/4ec24e4626250c4411388323a05c95d50673e218), tree `f2d597d840a5b09af42518f21ceeaad8ebdfbf98`, based on upstream `0d7c17fb365e4be8e3b66018548e7ebbda0e9507`. It addresses BootstrapMethod array ownership within [BCEL-238](https://issues.apache.org/jira/browse/BCEL-238).

## Executed results — 22 October 2026

| Run | UTC start–finish | Reported | Passed | Failures | Errors | Skipped |
| --- | --- | ---: | ---: | ---: | ---: | ---: |
| Unchanged production plus regression tests | 11:21:36–11:21:56 | 9 | 5 | 4 | 0 | 0 |
| Fixed focused tests | 11:22:29–11:22:42 | 9 | 9 | 0 | 0 | 0 |
| Full default build, final environment | 11:54:22–11:56:51 | 3,090 | 3,075 | 0 | 0 | 15 |

The same two test files ran before and after the fix. Four baseline assertions demonstrate constructor-input, setter-input, getter-output, and serialized-output aliasing. All pass on the fixed source. The full run contains 159 suites and includes all nine focused cases.

The required default sequence completed: `clean verify apache-rat:check japicmp:cmp checkstyle:check pmd:check spotbugs:check javadoc:javadoc`. JaCoCo analyzed 411 classes and met the configured thresholds; RAT, compatibility, style, PMD, SpotBugs and Javadoc gates completed. [validation.json](validation.json) records actual counts, named skips, run/source identities, gate evidence, commands and checksums. [The final full-build log](logs/05-default-jdk-tools.log) retains its non-fatal CycloneDX, cached-SPDX-license and PMD warnings.

## Reproduction

With a complete JDK and Maven, on the fixed source:

```sh
mvn '-Dtest=BootstrapMethod*' clean test
mvn
```

For the failing control, use a fresh checkout of upstream `0d7c17fb365e4be8e3b66018548e7ebbda0e9507`, apply [regression-tests.patch](regression-tests.patch), then run the focused command. That patch changes tests only. Expect four assertion failures and five passes.

Execution used Maven 3.9.11 and OpenJDK 17.0.20 on Linux amd64, compiling with release 8. Machine-local Maven settings/cache options are recorded with path placeholders. Native JDK launchers were absent in this environment; the retained [javap](tools/javap), [javac](tools/javac), and [Javadoc](tools/javadoc) launchers invoke the installed JDK modules. The final run prepended their directory to PATH and supplied `-Dcommons.compiler.javadoc=<TOOLS>/javadoc`. No source, dependency, test, or gate was changed to work around the environment.

## Earlier attempts and limits

[The initial dependency download attempt](logs/00-baseline.log) stopped before tests. [The baseline](logs/01-baseline-network.log) and [fixed focused run](logs/02-focused-fixed.log) followed. Two initial full builds stopped at missing [javap](logs/03-default.log) and [javac](logs/04-default-javap.log) launchers; each reported 3,063 passes, 12 errors and 15 skips. They are retained as failed attempts, separately from the successful final run.

Public logs replace private local paths with labeled placeholders. Original-log and sanitized-log hashes are distinct; private settings contents and XML system properties are omitted. These results establish local Linux/JDK 17 validation; Java 8 is a compilation target, not a tested runtime. Hosted CI, other platforms, upstream acceptance, and independent human review are not claimed.

ChatGPT generated this patch, tests, documentation and evidence packet. Two independent AI specialists reviewed the exact source and compatibility; a third audited the actual logs/XML/source identities. Both production copy implementations remain unchanged. The revised copy tests verify real independent updates, without claiming to inspect hidden array identity.
