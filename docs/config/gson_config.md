# Config files

Sooner or later you'll get to the point where you'd like to make certain things toggleable.
For such things, we can utilize included `Gson` library to read and write json configs.

## Why gson?

As mentioned above, `Gson` is already available in vanilla minecraft, meaning that you won't need
any extra dependencies. If you don't need anything special and you're fine with `json` format, `Gson` will
probably cover your needs. Not to mention that it will auto add the missing fields when updating
the config without need to overwrite old values.

## Config structure

We'll use a custom config class that has fields that user / admin will be able to change.
See below example.

```java
public class MyModConfig {

    public float activationRange = 8.0F;

    public boolean show = true;

    public String message = "This is a config guide.";
    
}
```
We can access our config values like this:
```java
MyModConfig config = new MyModConfig();

if(config.show)
    System.out.println(config.message);

config.show = false;

// Do other stuff with values ...
```

We can present the config object in the following json structure.

```json
{
    "activationRange": 8.0,
    "show": true,
    "message": "This is a config guide."
}
```

## Saving, reading and parsing

If `config` only exists in our memory, it's not that usefull, as changes will be reset
after server restart. If we want to make them permanent, we should write the config to a file.

```java
public class MyModConfig {
    private static final Gson gson = new GsonBuilder()
            .setPrettyPrinting() // Makes the json use new lines instead of being a "one-liner"
            .serializeNulls() // Makes fields with `null` value to be written as well.
            .disableHtmlEscaping() // We'll be able to use custom chars without them being saved differently
            .create();

    // Config values
    public float activationRange = 8.0F;

    public boolean show = true;

    public String message = "This is a config guide.";
    
    
    // Reading and saving

    /**
     * Loads config file.
     *
     * @param file file to load the config file from.
     * @return MyModConfig object
     */
    public static MyModConfig loadConfigFile(File file) {
        MyModConfig config = null;
        
        if (file.exists()) {
            // An existing config is present, we should use its values
            try (BufferedReader fileReader = new BufferedReader(
                    new InputStreamReader(new FileInputStream(file), StandardCharsets.UTF_8)
            )) {
                // Parses the config file and puts the values into config object
                config = gson.fromJson(fileReader, MyModConfig.class);
            } catch (IOException e) {
                throw new RuntimeException("[MyMod] Problem occurred when trying to load config: ", e);
            }
        }
        // gson.fromJson() can return null if file is empty
        if (config == null) {
            config = new MyModConfig();
        }
        
        // Saves the file in order to write new fields if they were added
        config.saveConfigFile(file);
        return config;
    }

    /**
     * Saves the config to the given file.
     *
     * @param file file to save config to
     */
    public void saveConfigFile(File file) {
        try (Writer writer = new OutputStreamWriter(new FileOutputStream(file), StandardCharsets.UTF_8)) {
            gson.toJson(this, writer);
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

Now that we have those new methods, we should use the following code to load the config.
```java
File configFile = FabricLoader.getInstance().getConfigDir().resolve("my_mod_config.json");
MyModConfig config = MyModConfig.loadConfigFile(configFile);

if(config.show)
    System.out.println(config.message);

config.show = false;

// We can save it now
// If you'd like, you can also save the file parameter when loading
config.save(configFile);

// Do other stuff with values ...
```

This will automatically create new config file or read the existing one.


## Nested classes

Creating nested structures is really simple. All you need to have is another
static inner class and a new object for it. See the example below.

```java
public class MyModConfig {

    public float activationRange = 8.0F;

    public boolean show = true;

    public String message = "This is a config guide.";
    
    public NestedValues nested = new NestedValues();
    
    public static class NestedValues {
        public String message = "This is a another message.";
    }
    
}
```

And its json counterpart:

```json
{
    "activationRange": 8.0,
    "show": true,
    "message": "This is a config guide.",
    "nested": {
        "message": "This is a another message."
    }
}
```


## Arrays

To create a json array, you can use `ArrayList<>` class from java.

```java
public class MyModConfig {

    public float activationRange = 8.0F;

    public boolean show = true;

    public String message = "This is a config guide.";
    
    public NestedValues nested = new NestedValues();
    
    public static class NestedValues {
        public String message = "This is a another message.";
    }
    
    public List<String> randomQuestions = new ArrayList<>(Arrays.asList(
        "Why no forge port?",
        "When quilt?",
        "Tiny potato or tiny pumpkin?",
        "What is minecraft?" // How dare you
    ));
}
```

And its json counterpart:

```json
{
    "activationRange": 8.0,
    "show": true,
    "message": "This is a config guide.",
    "nested": {
        "message": "This is a another message."
    },
    "randomQuestions": [
        "Why no forge port?",
        "When quilt?",
        "Tiny potato or tiny pumpkin?",
        "What is minecraft?"
    ]
}
```

## Naming the fields differently

Json structure doesn't limit us as much as java when it comes to naming fields.
Luckily, we can overcome this problem by using `@SerializedName` annotation on fields
to change how they're written in the file.

```java
public class MyModConfig {

    @SerializedName("activation range")
    public float activationRange = 8.0F;

    @SerializedName("Show config message.")
    public boolean show = true;

    @SerializedName("config_message")
    public String message = "This is a config guide.";
    
}
```
The above code will result in the following json:

```json
{
    "activation range": 8.0,
    "Show config message.": true,
    "config_message": "This is a config guide."
}
```

## Poor-man's comments

With the above technique we can even add "comments" to our config.
Sure, they won't be "perfect json5" comments, but ... they'll still
be useful for the end user. Comments must be unique, as they
act like json fields.

```java
public class MyModConfig {

    // Naming comments in style `_comment_` + `field name`
    @SerializedName("// When to activate feature xyz.")
    public final String _comment_activationRange = "(default: 8.0)";
    @SerializedName("activation_range")
    public float activationRange = 8.0F;

    
    @SerializedName("// Whether to show config message.")
    public final String _comment_show = "(default: true)";
    @SerializedName("show_config_message.")
    public boolean show = true;

    @SerializedName("// Which message to print out")
    public final String _comment_message0 = "";
    @SerializedName("// if above option is enabled")
    public final String _comment_message1 = "(default: 'This is a config guide.')";
    @SerializedName("config_message")
    public String message = "This is a config guide.";
    
}
```

This will be saved as the following json:

```json
{
    "// When to activate feature xyz.": "(default: 8.0)",
    "activation_range": 8.0,
    "// Whether to show config message.": "(default: true)",
    "show_config_message.": true,
    "// Which message to print out": "",
    "// if above option is enabled": "(default: 'This is a config guide.')",
    "config_message": "This is a config guide."
}
```
