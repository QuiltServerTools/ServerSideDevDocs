# Commands

Commands are Minecraft's way of handling text-based user input outside of standard gameplay

---

## Registering a command

In order to register commands, we first need to listen to Fabric API's command registration event.

```java
public class MyCommandsMod implements DedicatedServerModInitializer {
    @Override
    public void onInitializeServer() {
        CommandRegistrationCallback.EVENT.register(dispatcher, dedicated -> {
            // Add command registration code here
        });
    }
}
```
---

## Creating our command

Minecraft commands use Mojang's brigadier library.

Registering a basic command is as follows:

```java
public class MyCommandsMod implements DedicatedServerModInitializer {
    @Override
    public void onInitializeServer() {
        CommandRegistrationCallback.EVENT.register(dispatcher, dedicated -> {
            dispatcher.register(
                    literal("mymodcommand")
                            .executes(ctx -> {
                                ctx.getSource().getPlayer().sendMessage(new LiteralText("IT WORKS"), false);
                                return 1;
                            })
            );
        });
    }
}
```

If you now run your server and type `/mymodcommand` you will see a message appear in game chat!

---

### Arguments

Above, you were introduced to basic registration of commands. We will now make our command more advanced: using arguments.

```java
public class MyCommandsMod implements DedicatedServerModInitializer {
    @Override
    public void onInitializeServer() {
        CommandRegistrationCallback.EVENT.register(dispatcher, dedicated -> {
            dispatcher.register(
                    literal("mymodcommand")
                            .then(literal("subcommand")
                                    .executes(ctx -> {
                                        ctx.getSource().getPlayer().sendMessage(new LiteralText("It works"), false);
                                        return 1;
                                    })
                            )
            );
        });
    }
}
```

`/mymodcommand subcommand` will now run the same as `/mymodcommand` had in the previous example

#### But what does the code do?

Let's take a look line-by-line

`dispatcher.register(`

This line tells Minecraft to register the command supplied in the argument of the `register` method.


`literal("mymodcommand")`

Here we call `CommandManager#literal` with the argument `"mymodcommand"`. Literal means that the first part of the command is the string provided.


`.then(literal("subcommand")`

We add the next part of the command, a string literal with the value `subcommand`.


```
.executes(ctx -> {
    ctx.getSource().getPlayer().sendMessage(new LiteralText("It works!"), false);
    return 1;
})
```

`.executes` tells Minecraft to run the lambda block provided, taking one lambda parameter: an object of type `CommandContext<ServerCommandSource>`. It requires an integer to be returned.


### Formatting of commands

It's suggested to indent your commands in a way that the various commands and arguments are clearly visible at a glance. IntelliJ autoformatting (CTRL+ALT+L) will help with this.

---

## Non-String-Literal arguments

String literals provide an excellent way to tell the game which command you are running, as you wouldn't type `/minecraft:overworld`. We do however want users to be able to enter their own information. Let's take a look at Mojang provided argument types.

```java
public class MyCommandsMod implements DedicatedServerModInitializer {
    @Override
    public void onInitializeServer() {
        CommandRegistrationCallback.EVENT.register(dispatcher, dedicated -> {
            dispatcher.register(
                    literal("count")
                            .then(argument("number", IntegerArgumentType.integer())
                                    .executes(ctx -> {
                                        ctx.getSource().getPlayer().sendMessage(new LiteralText("You entered: " + IntegerArgumentType.getInteger(ctx, "count")), false);
                                        return 1;
                                    })
                            )
                            .then(literal("subcommand")
                                    .executes(ctx -> {
                                        ctx.getSource().getPlayer().sendMessage(new LiteralText("It works"), false);
                                        return 1;
                                    })
                            )
            );
        });
    }
}
```

As you can see, we duplicated our code and replaced the string literal with a call to `CommandManager#argument`, providing the name of the argument and the type of the argument. We then obtain that argument using the `CommandContext` provided in our lambda.

This would leave you with the option of the two commands below:

`/count subcommand` -> "It works"

`/count <count>` -> "You entered: <count>"

---

Congratulations! You learnt how to create a command in your mod. You can now learn to add permission checks to your commands on the [Permissions page](permissions.md)