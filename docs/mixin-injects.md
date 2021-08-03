# Mixin Injects

Mixin is a powerful tool used for bytecode transformation - specifically modifying the Minecraft source code in a way that prevents as many conflicts as possible between mods. Let's take a look at an example.

## Mixin triggered whenever a block is broken

```java
@Mixin(Block.class)
public class MixinBlock {
    @Inject(at = @At("HEAD"), method = "onBreak")
    public void onBreak(World world, BlockPos pos, BlockState state, PlayerEntity player, CallbackInfo info) {
        player.sendMessage(new LiteralText("You broke " + Registry.BLOCK.getId(state.getBlock())), false);
    }
}
```

This is considerably more complex than anything shown so far. But what does it do?

`@Mixin(Block.class)`

This tells the compiler that this is a mixin class, targeting the class `net.minecraft.block.Block`.

`@Inject(at = @At("HEAD"), method = "onBreak")`

`@Inject` is a mixin annotation. It tells the mixin compiler to "inject" the logic in our method to the part of the method `onBreak` specified by the `at` argument. `@At("HEAD")` refers to the top of the method target; the first bit of code to be run when that method is called.

We then have a method with the same arguments as our target method, plus a `CallbackInfo` object.

#### Registering Mixins

When you add a mixin, you must put the mixin class in `your_package.mixin`. Then, you should add an entry to the `server` array in your `<your_modid>.mixins.json` file with the name of your mixin class.

### Fabric Wiki

Confused? The fabric wiki has a [very extensive guide to mixins](https://fabricmc.net/wiki/tutorial:mixin_introduction)

### Mixin cheatsheet

The [Mixin cheatsheet](https://github.com/2xsaiko/mixin-cheatsheet) has guides and examples for every type of mixin. If you want to learn how to do more than injecting at the top of a method, I would advise checking it out.

### Game code used in the example

`LiteralText`: `new LiteralText(String text)` - this creates a `Text` object with the value of the string passed. 

`Text`: a class representing a message that can be sent to a player through chat. Contains colour information, the string literal, or potentially a translation key.

`PlayerEntity#sendMessage(Text text, boolean actionBar)`: Sends the player a message with the text component provided in the arguments. `actionBar` should nearly always be false
