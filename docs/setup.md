# Setup

### Prerequisites

You will need:

- Java Development Kit Version 16 or newer
- IntelliJ IDEA
- Intermediate Java knowledge
- Git and basic git experience
- The Minecraft Development plugin for IntelliJ
- Basic JSON knowledge

---

## Download

Create a new repository, either locally or on GitHub/GitLab. If you did not create it locally, go to `file -> new from version control` and clone the repo

Download the code from the [Fabric example mod repo](https://github.com/fabricmc/fabric-example-mod). You will then need to copy the contents into your repository folder.

### Note about the Minecraft Development Plugin

While you can handle the initial setup with the plugin, it has major bugs when it comes to 1.17 mod setup. You will also gain experience with Gradle and Loom this way

---

### Initialise your mod

First, open up the `gradle.properties` file within IntelliJ. It will look something like this:

```properties
# Done to increase the memory available to gradle.
org.gradle.jvmargs=-Xmx1G

# Fabric Properties
	# check these on https://fabricmc.net/versions.html
	minecraft_version=1.17.1
	yarn_mappings=1.17.1+build.14
	loader_version=0.11.6

# Mod Properties
	mod_version = 1.0.0
	maven_group = com.example
	archives_base_name = fabric-example-mod

# Dependencies
	fabric_version=0.37.0+1.17
```

First, you want to delete the dependencies section of your `gradle.properties` file.

Open up the [fabric versions page](https://fabricmc.net/versions.html) in your browser.

Replace your `# Fabric Properties` section with the `gradle.properties` values found on the versions page.

Next, you need to set your mod information. 

Set `maven_group` to `io.github.yourusername` and set `archives_base_name` to the name of your mod, in lower-case letters and without spaces

---

### Fabric setup

Now we have our Gradle set up, you will need to tell fabric about your mod. Navigate to your `fabric.mod.json` file found under `src/main/resources`

It will look something like this:

```json
{
  "schemaVersion": 1,
  "id": "modid",
  "version": "${version}",

  "name": "Example Mod",
  "description": "This is an example description! Tell everyone what your mod is about!",
  "authors": [
    "Me!"
  ],
  "contact": {
    "homepage": "https://fabricmc.net/",
    "sources": "https://github.com/FabricMC/fabric-example-mod"
  },

  "license": "CC0-1.0",
  "icon": "assets/modid/icon.png",

  "environment": "*",
  "entrypoints": {
    "main": [
      "net.fabricmc.example.ExampleMod"
    ]
  },
  "mixins": [
    "modid.mixins.json"
  ],

  "depends": {
    "fabricloader": ">=0.11.3",
    "fabric": "*",
    "minecraft": "1.17.x",
    "java": ">=16"
  },
  "suggests": {
    "another-mod": "*"
  }
}
```

This files holds all the metadata about our mod, which Fabric uses to load the mod. Here is a list of fields and what you should change them to:

`id`: The same value as you put for `archives_base_name` in `gradle.properties`.

`name`: The formatted name of your mod. This can include spaces and capital letters.

`description`: A short, formatted description of your mod

`authors`: `[ "your_username" ]`

Fill out the contact section with your relevant links, but you can remove it if you would like.

`license`: The name and potentially version of the license of your choice. Although we strongly discourage keeping mods closed source, you may indicate this by omitting the field.

`environment`: Leave at `*` if you want your mod to work on singleplayer (might cause issues), otherwise change it to `server`

`suggests`: Feel free to remove this section

`depends`: Will be covered in a tutorial showing how to use mod libraries. You can leave it as the default for now

#### Entrypoints

Entrypoints are the classes where Fabric starts your mod - a piece of code that runs on game launch. More information can be found on the [fabric wiki page](https://fabricmc.net/wiki/documentation:entrypoint).

##### Dedicated only

Change the `main` entry in your `entrypoints` section to `server`

---

You must now determine your package. This will most likely be the `maven_group` value from your `gradle.properties`, but with the name of your main class appended. An example would be: `io.github.username.modname.ModName`.

After specifying this value, we now need to actually create the entrypoint. Delete the `net` folder found under `src/main/java`.

Create a folder structure with your package, within the `src/main/java` directory. `io.github.username.modname` would become `io/github/username/modname`.

Once you have created this folder, right click it in IntelliJ and press "Create Java class". The class name should be the value after your package specified in the entrypoints section. In the example case, it would be `ModName`.

Just one more step now - we need to get Fabric to recognise our main class. Make the class you just created implement `DedicatedServerModInitializer` if you are dedicated server only, or `ModInitializer` if you support singleplayer. Add `System.out.println("MOD LOADED")` to the overridden method IntelliJ should prompt you to make. 

#### Run configurations

Go to the top right-hand corner of IntelliJ, press "Add Configuration", then under "application" you will find `Minecraft Server`. Select that and press debug. You will need to agree to the EULA, but if all goes well your mod has been loaded!
