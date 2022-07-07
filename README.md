# gradle-repo-order-causes-serial-downloads

A repro of an issue in Gradle 7.4.2 (and other versions) in which having a repo with `onlyForConfigurations` as the first repository can cause dependencies to get resolved in a single thread rather than in parallel.

## How to repro

Try running the following (adjusted for your OS if necessary):

```
# Clear Gradle's dependency cache so it needs to re-download
rm -rf ~/.gradle/caches/modules-2/
# Run a task that requires many dependencies
./gradlew resolveAsTaskInput
```

When the `onlyForConfigurations` repository is first, Gradle will show that all the dependency resolution is happening in a single worker. When the other (`mavenCentral`) repository is first, some of the dependency downloads happen in parallel across multiple workers.

In this repro, the timing difference is not quite large enough to be obvious, but in certain situations the single-threaded resolution can be over a minute slower.
