# Libraries and APIs

If you have a program you want to write which shares common code with other programs, what would you do? Luckily, this has already been decided. You put your common code in a library for other people to use.

## Adding a Library or API

For this tutorial, we will be adding the Luckperms Permissions API, which is the most common permissions API in use with Fabric server-side mods.

### Locate Artifact Location

You will need to find the maven repository that your library is on. In the case of the luckperms API, the location is `https://oss.sonatype.org/content/repositories/snapshots`

### Add Repository

Locate the `repositories` block in your `build.gradle`:

```groovy
repositories {
    
}
```

You now add a maven repository with the URL you found to it:

```groovy
repositories {
    maven {
        url = "https://oss.sonatype.org/content/repositories/snapshots"
    }
}
```

### Add to `build.gradle`

#### Mod Libraries or APIs

Find the `dependencies` block in your `build.gradle`:

```groovy
dependencies {
    // To change the versions see the gradle.properties file
    minecraft "com.mojang:minecraft:${project.minecraft_version}"
    mappings "net.fabricmc:yarn:${project.yarn_mappings}:v2"
    modImplementation "net.fabricmc:fabric-loader:${project.loader_version}"

    // Fabric API. This is technically optional, but you probably want it anyway.
    modImplementation "net.fabricmc.fabric-api:fabric-api:${project.fabric_version}"
}
```

Add the following line in the block:

`modImplementation(include("library.package:library-name:library-version"))`

This will tell Gradle to add our library and when you export your mod, it will add the library inside the produced jar file. If your library is a mod which is intended to be downloaded on its own, such as fabric-language-kotlin, you should exclude the `include()` block.

In the case of Luckperms, we need to add `modImplementation(include('me.lucko:fabric-permissions-api:0.1-SNAPSHOT'))`

#### Non-Mod Libraries or APIs

Find the `dependencies` block in your `build.gradle`:

```groovy
dependencies {
    // To change the versions see the gradle.properties file
    minecraft "com.mojang:minecraft:${project.minecraft_version}"
    mappings "net.fabricmc:yarn:${project.yarn_mappings}:v2"
    modImplementation "net.fabricmc:fabric-loader:${project.loader_version}"

    // Fabric API. This is technically optional, but you probably want it anyway.
    modImplementation "net.fabricmc.fabric-api:fabric-api:${project.fabric_version}"
}
```

Add the following line in the block:

`implementation(include("library.package:library-name:library-version"))`

This will tell Gradle to add our library and include the library when the mod is exported

---

Once you have added your library, you will see a button to refresh Gradle. Press this, and your library is now added!
