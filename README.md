# Getting Started with Gradle

This is a short tutorial to use Gradle with Java in the real world so that beginners won't be scared away by Java's baffling tooling, specially when they are sitting behind a forbidden firewall. 

Read these docs in order specified:

1. https://docs.gradle.org/current/samples/sample_building_java_libraries.htm
2. https://docs.gradle.org/current/samples/sample_building_java_applications.html
3. https://docs.gradle.org/current/userguide/building_java_projects.html

## Create a project folder

```
mdkir demo
cd demo
```

## Initialize

Gradle comes with a built-in task `init` to initialize a new Gradle project in an empty folder.

From inside the new project directory, run `gradle init`.

```
Select type of project to generate:
  1: basic
  2: application
  3: library
  4: Gradle plugin
Enter selection (default: basic) [1..4]
```

Enter 2.

```
Select implementation language:
  1: C++
  2: Groovy
  3: Java
  4: Kotlin
  5: Scala
  6: Swift
Enter selection (default: Java) [1..6]
```

Enter 3.

```
Select build script DSL:
  1: Groovy
  2: Kotlin
Enter selection (default: Groovy) [1..2]
```

Enter 2.

## Change Repositories

Gradle retrieves dependencies from maven by default. To customize the repository server, open the `app/build.gradel.kts` file. Find `repositories` section. Change it like this:

```
repositories {
    maven {
        url = uri("https://maven.aliyun.com/repository/central")
    }
    mavenCentral()
}
```

Now Gradle will try to use Aliyun maven repository first.

See https://docs.gradle.org/current/userguide/declaring_repositories.html for more details.

## Bundle Project

The `application` plugin also bundles the application, with all its dependencies.

```
gradle build
```

The archive is located in `app/build/distributions/app.tar`

NOTE this is not a fat jar. It only zips all jar files under a single directory.

The files bundled this way can be executed directly with the `java -jar` command since you the main class name is missing. You can either provide it in the command arguments, or set it in manifest file.

## Customize the JAR

### Manifest

Configure the `jar` task:

```
tasks.jar {
    manifest {
        attributes(mapOf(
            "Implementation-Version" to project.version
            "Main-Class" to project.application.mainClass
        ))
    }
}
```

### Version

To have version included in the JAR file name, set a top-level `version` property in the build script:

```
version = "0.1.0"
```

## Add Dependency

## Uber JAR

Use [Shadow](https://github.com/johnrengelman/shadow) to package a fat JAR.

Add it in the plugins section:

```
plugins {
    id("com.github.johnrengelman.shadow") version "7.1.2"
    application
}
```

The plugin will be retrieved from Gradle plugin portal if not found in your local cache.

Run `gradle shadowJar` will generate a JAR containing all dependencies of your project. 