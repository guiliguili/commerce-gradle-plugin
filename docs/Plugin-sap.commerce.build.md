# Plugin `sap.commerce.build`

The goal for this plugin is to automate the most common steps when setting up a SAP Commerce development environment and allows you to call the platform ant build as a gradle task (in your script, or from the command line).

It also allows you to automate the download of the distribution zip files from the SAP Support Portal.

## Configuration 
 
The following example shows the full DSL (Domain Specific Language) with all default options and the dependencies the 
plugin pre-configures.

```groovy
hybris {
    //hybris version to use
    version = "6.6.0.0"
    
    //what files should be deleted when cleaning up the platform?
    cleanGlob = "glob:**hybris/bin/{ext-**,platform**}"
    
    //what should be unpacked from the platform zip files?
    bootstrapInclude = ["hybris/**"]
    
    //what should excluded when unpacking?
    //the default value is a npm package folder that includes UTF-8 filenames, which lead to problems on linux
    bootstrapExclude = ["hybris/bin/ext-content/npmancillary/resources/npm/node_modules/http-server/node_modules/ecstatic/test/**"]
}

dependencies {
    //Hybris Platform Dependencies. Expects zip files that are unpacked into the project root folder
    //you can define more than one zip file to unpack
    //bootstrapInclude and bootstrapExclude define what is unpacked
    hybrisPlatform "de.hybris.platform:hybris-commerce-suite:${hybris.version.get()}@zip"
    
    //JDBC Drivers. Automatically downloaded and configured during bootstrap, if a dependency is configured
    //the plugin does not provide defaults!
    dbDriver <NO DEFAULT>
}
```

## Tasks

The plugin defines the following tasks

### `bootstrapPlatform`

Bootstraps the configured platform version with the configured DB drivers.

This means the following:

1. **if there is a version mismatch** between the version defined
   in the `build.gradle` and the version present in `hybris/bin/platform/build.number`:
   clean the hybris artifacts from the project directory using `hybris.cleanGlob`
1. Unpack all zip files defined for the `hybrisPlatform` dependency using `hybris.bootstrapInclude` and
   `hybris.bootstrapExclude` to limit the files.
1. Download all `dbDriver` dependencies into `hybris/bin/platform/lib/dbdriver`
1. touch `hybris/bin/platform/lib/dbdriver/.lastupdate`
   (this stops the platform build from running maven to fetch drivers)

### `cleanPlatform`

Deletes the hybris platform folders (all files/folders matching `hybris.cleanGlob`)

[What is a Glob?](https://docs.oracle.com/javase/tutorial/essential/io/fileOps.html#glob)

### `createDefaultConfig`

Launches hybris build to create the standard developer config folder, if no config folder is present

## Task Types

The plugin defines a custom task type you can use in your build script to interact with the normal hybris build

### `mpern.sap.commerce.build.tasks.HybrisAntTask`

This task type allows you to define a hybris ant target as gradle task.

Under the hood it's a pre-configured `JavaExec` task

#### Example:

```groovy
task unitTests(type: mpern.sap.commerce.build.tasks.HybrisAntTask) {
    //provide ant target name as argument
    args("unittests")
    
    //everything you define with -D... when calling ant you can define via antProperty
    antProperty("testclasses.extensions", "training")
    antProperty("testclasses.suppress.junit.tenant", "true")
    
    //we can also call other ant targets by prefexing the target name with `y`
    //because the plugin defines a task rule (see below)
    dependsOn tasks.getByPath("ybuild")
}
```

### `mpern.sap.commerce.build.tasks.SupportPortalDownload`

**Deprecated with v2.1.1; Removed with v3.0.0**

Downloads hybris commerce files from the SAP Support Portal.

| Parameter     | Description |
| ------------- | ------------- |
| `supportPortalUrl` | URL of the file in the Support Portal (The same URL that opens when you click on the link on the Hybris Commerce Release Page) |
| `username`         | S/I/D-User |
| `password`         | Password  |
| `targetFile`       | Where the file is downloaded to |
| `md5Hash`          | Expected MD5-hash of the file (used to perform up-to-date checks). You can find the hash in the Support Portal via Related Info -> Content Info |
| `sha256Sum`        | Expected SHA-256 hash of the file. You need to specify either the `md5Hash` or the `sha256Sum` |

The plugin also makes sure that all `SupportPortalDownload` tasks are executed before `bootstrapPlatform`, to avoid
dependency resolution issues when bootstrapping the platform after a fresh clone.

## Task Rules

For convenience, the plugin also defines task rule that allows you to call any ant target directly from the gradle
command line or from other tasks


### com.hybris.yps.build.rules.HybrisAntRule

Pattern: `y<target>`: Run hybris `ant <target>`

The rule maps the called task name to the ant target and preconfigures a `HybrisAntTask` to execute

**Warning** Gradle does not know the proper order for hybris ant targets! The plugin configures sane defaults for
`clean`, `build`, `all` and `production`, but for everything else you have to configure the proper order
yourself!

For example in`build.gradle`:
````
tasks.getByPath("ycustomtarget").mustRunAfter("yall")
````
*While it is possible to import an ant build into gradle to solve the issue above, I decided against it as the hybris build is very complex and slows down gradle too much, IMHO*
