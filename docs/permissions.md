# Permissions

This tutorial shows how to handle and check permissions.

## Setup

You will need to have set up the Luckperms Permissions API in your environment. See the [libraries page](libaries.md)

---

## Command Permissions

Here is our command without a permission check:

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

This allows any player on the server to use `/mymodcommand`. But in many cases, we want to restrict who can run the command

---

## Permission Levels

Every player has a permission level. Here is a non-extensive list of standard values:

- Standard player: 0
- Command blocks: 2
- Operator: Specified in `server.properties`, default 4

#### What do the levels allow?

- 0: Normal non-operator commands
- 1: Bypass spawn protection
- 2: Operator commands without moderation perms
- 3: Moderation commands
- 4: /stop command

---

## Set a Command's Permission Level

```java
public class MyCommandsMod implements DedicatedServerModInitializer {
    @Override
    public void onInitializeServer() {
        CommandRegistrationCallback.EVENT.register(dispatcher, dedicated -> {
            dispatcher.register(
                    literal("mymodcommand")
                            .then(literal("subcommand")
                                    .requires(scs -> scs.hasPermissionLevel(3))
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
`.requires(scs -> scs.hasPermissionLevel(3))`

This line means that only players with permission level 3 or greater can run the following parts of the tree

---

## Permission Nodes

While the permission level system provides security, it does not provide flexibility for servers. For this reason, we use the Luckperms API, which allows permissions mods 

Instead of `.requires(scs -> scs.hasPermissionLevel(3))`, we can do `.requires(Permissions.require("mymod.command.mymodcommand.subcommand", 3))`

This line requires that the player have permission level 3 or the permission node of the name `mymod.command.mymodcommand.subcommand`

### Checking permission nodes outside of commands

```java
public class PermissionUtils {
    public boolean hasPermissionOrNode(ServerPlayerEntity player, int i, String node) {
        return Permissions.check(player, node, i);
    }
}
```

This code sample will return whether the player has a permission node or has permission level `i`

--- 

You have now added permissions checks to your code!