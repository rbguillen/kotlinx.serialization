# JSON parser and syntax tree for Kotlin/Native

Since plugin API unavailable for K/N compiler yet,
kotlinx.serialization offers separate common+native artifacts for parsing JSON into tree-like structure
with minimalistic API:

```kotlin
import kotlinx.serialization.json.*

val input = """{"a": "foo", "b": 10, "c": true, "d": null}"""
val elem: JsonElement = JsonTreeParser(input).read() // or .readFully() to throw exception if input was not consumed fully

elem as JsonObject
elem.keys == setOf("a", "b", "c", "d") // true
assertEquals(JsonLiteral("foo"), elem["a"])
println(elem.getPrimitive("b").int) // 10
```

Whole JSON tree sources and API can be found [here](common/src/kotlinx/serialization/json/JsonElement.kt#L22).

## Installing

On JVM and JS, this module included into artifacts of kotlinx.serialization, so JSON tree available 'as is', including common code.
If you're using native, you can't use `kotlinx-serialization-runtime-common`, since it contains more features.
You need to declare dependency only on `kotlinx-serialization-runtime-jsonparser` in your common code, and then use
`org.jetbrains.kotlinx:jsonparser-native` dependency in Native. Example of native dependency can be found [here](../example-native) (CLI application)
or [here](../example-native) (iOS application alongside technology preview of HTTP client for native). Don't forget to `enableFeaturePreview('GRADLE_METADATA')`
in yours `settings.gradle`. You must have Gradle 4.7, because higher versions have unsupported format of metadata.
 
Note that by default, artifacts are published to bintray only for macOS, iOS x64 and iOS simulator.
If you want to use library on other targets, you'll need to build it by yourself with `./gradlew :jsonparser-native:build`.
By default, library is built for host machine. Then you can publish it to local maven repository as usual.
