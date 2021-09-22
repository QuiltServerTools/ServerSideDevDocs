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
File configFile = new File("./config/my_mod_config.json");
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