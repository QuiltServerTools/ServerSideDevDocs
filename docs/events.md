# Events

Events provide a way of running code when a certain action happens

Fabric's API has the following groups of events:

- [Lifecycle events](https://github.com/FabricMC/fabric/tree/1.17/fabric-lifecycle-events-v1/src/main/java/net/fabricmc/fabric/api/event/lifecycle/v1)
- [Interaction events](https://github.com/FabricMC/fabric/tree/1.17/fabric-events-interaction-v0/src/main/java/net/fabricmc/fabric/api/event/player)
- [Networking events](https://github.com/FabricMC/fabric/blob/1.17/fabric-networking-api-v1/src/main/java/net/fabricmc/fabric/api/networking/v1/ServerPlayConnectionEvents.java)

For this example, we will use the lifecycle events module.

## Registering the callback

When you want to listen for an event, you register a bit of code that will run whenever the event is triggered.

Listening for the server starting event would be as follows:

```java
public class MyMod implements DedicatedServerModInitializer {
    @Override
    public void onInitializeServer() {
        ServerLifecycleEvents.SERVER_STARTING.register(server -> {
           System.out.println("SERVER STARTING"); 
        });
    }
}
```

The server starting event is defined within `ServerLifecycleEvents` in a field named `SERVER_STARTING`. We then register using the `SERVER_STARTING` event, which requires a lambda expression with a single `MinecraftServer` parameter.

### What is `MinecraftServer`?

If you have ever written a plugin for the Bukkit API, you will know that you interact with the API, not the game. This is not the case on Fabric. `MinecraftServer` is the name of the class that is, well, the Minecraft Server in use at the time. As you progress through this guide, we will show you many of the methods that this class contains. However, as the is so much in this class, we will not be able to cover all of its features - and this is a recurring theme. For this reason, if you are looking for something, IDE tab completion will be your friend. If you ever have issues, you can ask on the [Fabric Server-side Development Discord server](https://discord.gg/HU7JVxpVRS). The simplest way to put it is: you will know how to do more the more time you spend playing around.

### More uses of events

You can register events anywhere, and have whatever logic you want run when they are called. Let's look at a more complex example.

```java
public class MyMod implements DedicatedServerModInitializer {
    @Override
    public void onInitializeServer() {
        registerServerStarted();
        ServerLifecycleEvents.SERVER_STOPPING.register(server -> onServerStop(server));
    }
    
    public void registerServerStarted() {
        ServerLifecycleEvents.SERVER_STARTED.register(server -> {
            if (server.getPlayerManager().getPlayerList().isEmpty()) {
                System.out.println("SERVER STARTING");
            }
        });
    }
    
    private void onServerStop(MinecraftServer server) {
        System.out.println("SERVER STOPPING");
    }
}
```

## Fabric Wiki

This page also has an [alternative on the Fabric wiki](https://fabricmc.net/wiki/tutorial:callbacks), although it is very out of date.