# Shaper

Kotlin library and command line tool for generation of directory with files from template (on
Handlebars) and configuration.

# Usage

## Core

Core is kotlin library, for integration in cli-app, idea plugin, gradle plugin, etc.

Using is simple:

```kotlin
import dev.icerock.tools.shaper.core.Config
import dev.icerock.tools.shaper.core.Shaper

// describe files configuration for generator
val buildGradleFile = Config.FileConfig(
    // path of output file
    pathTemplate = "build.gradle.kts",
    // name of template file used for content of file
    contentTemplateName = "build.gradle.kts",
    // params that will be passed into template
    templateParams = mapOf("dependencies" to listOf("dep1", "dep2"))
)
val sourceCodeFile = Config.FileConfig(
    // path also support templates and got all params
    pathTemplate = "src/main/kotlin/{{packagePath packageName}}/Test.kt",
    contentTemplateName = "Test.kt",
    templateParams = mapOf("packageName" to "dev.icerock.test")
)

// describe generator config
val config = Config(
    // params that will be passed into template of each files. 
    // can be overriden by params of file
    globalParams = mapOf("packageName" to "dev.icerock"),
    // list of files that should be generated
    files = listOf(buildGradleFile, sourceCodeFile)
)

// create generator with configuration
val shaper = Shaper(config)
// execute generation into build/test directory
shaper.execute(output = "build/test")
```

Templates will be loaded by priority:

1. search at working dir by name
2. search at resources of ClassLoader by name

Here sample templates:
`build.gradle.kts.hbs`:

```handlebars
plugins {
id("org.jetbrains.kotlin.jvm") version "1.4.30"
}

dependencies {
{{#each dependencies}}    implementation("{{this}}")
{{/each}}
}
```

`Test.kt.hbs`:

```handlebars
package {{packageName}}

class Test {
}
```

## CLI

You can use generator by CLI app. Configuration stored in yaml file in this case:

```yaml
globalParams:
  packageName: dev.icerock

files:
  - pathTemplate: 'build.gradle.kts'
    contentTemplateName: build.gradle.kts
    templateParams:
      dependencies:
        - dep1
        - dep2
  - pathTemplate: 'src/main/kotlin/{{packagePath packageName}}/Test.kt'
    contentTemplateName: Test.kt
    templateParams:
      packageName: dev.icerock.test
```

To run CLI:

```shell
java -jar shaper-cli-0.1.0-SNAPSHOT.jar -i <path-to-yaml> -o <output-directory>
```

## License

    Copyright 2021 IceRock MAG Inc
    
    Licensed under the Apache License, Version 2.0 (the "License");
    you may not use this file except in compliance with the License.
    You may obtain a copy of the License at
    
       http://www.apache.org/licenses/LICENSE-2.0
    
    Unless required by applicable law or agreed to in writing, software
    distributed under the License is distributed on an "AS IS" BASIS,
    WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
    See the License for the specific language governing permissions and
    limitations under the License.
