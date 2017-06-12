# analysing-gradle-performance

This project shows a large variety of performance issues that a user could accidentally introduce into a build. The [`after`](https://github.com/oehme/analysing-gradle-performance/tree/after) branch shows how this project should look like to do as little work as possible and make the best use of the system's resources. It is orders of magnitudes faster than the version on the [`before`](https://github.com/oehme/analysing-gradle-performance/tree/before) branch.

You can use the [Gradle profiler](https://github.com/gradle/gradle-profiler) to run all the performance scenarios using a simple command:

`gradle-profiler --benchmark --gradle-user-home user-home --scenario-file performance.scenarios`

Here is a list of problems that this project showcases:

1. Not using the Gradle Daemon
2. Using an outdated Gradle version
3. The `uberJar` task does dependency resolution at configuration time
4. Cache timeouts are way too low
5. The `chuck-norris-fact` script does work on every build which really should be a task
6. The `projectStats` task runs at configuration time, doing lots of disk IO
7. The version calculation is done by calling git for every subproject
8. Most of the project's task are always out of date, even if no sources changed, because the version and JAR manifest change on each build.
9. The `my-app` tests depend on the `my-lib` tests, even though they only need a test fixture from `my-lib`. Any change to a test implementation in `my-lib` re-triggers all `my-app` tests.
10. The `my-lib` project leaks implementation details into my-app. Changes to the `commons` project trigger a recompile of `my-app` even though it doesn't use `commons`.
11. `my-lib` has undeclared annotation processors, which prevents compile avoidance
12. The project doesn't use incremental compilation
13. All tests are run serially
14. All tasks are run serially
15. Compilation is run in-process, greatly increasing the daemon's memory usage

Each commit on the `after` branch contains a description why it improves the project.
