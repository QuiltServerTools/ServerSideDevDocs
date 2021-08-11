# NBT

NBT is the way Minecraft stores information for multiple things: entity data, world storage, items and many more features are all powered by NBT.

## Introduction

Let's start by creating an empty NBT tag

```java
NbtCompound nbt = new NbtCompound();
```

Here we register a variable, `nbt`, and make it an empty NBT object.

Let's add some information:

```java
NbtCompound myInformation = new NbtCompound();

myInformation.putInt("age", 69);
myInformation.putString("name", "nbt tutorial");
```

We have now added an integer value, age, to our object. We also add a string value, called name.

But you may be wondering: isn't this just glorified JSON? Well in essence, yes. NBT has many similarities with JSON. If you know how to use JSON, NBT should be trivial to pick up. 

Like JSON, we can also add objects as the value:

```java
myInformation.putInt("profile", new NbtCompound());
```

This line would put an empty object as the profile value.

Below is a representation of what we have done, in JSON:

```json
{
  "age": 69,
  "name": "nbt tutorial",
  "profile": {
    
  }
}
```

---

You can also read from NBT objects:

```java
String name = myInformation.getString("name");
NbtCompound profile = myInformation.get("profile");
``` 

---

## Practical use

All this is great, but we actually need to use it. So, how?

This code sample will take the NBT from a hopper and will print it to the console:

```java
public class NbtTutorial {
    public static void printNbt(HopperBlockEntity hopper) {
        NbtCompound nbt = hopper.writeNbt(new NbtCompound());
        System.out.println(nbt.toString());
    }
}
```

`writeNbt(NbtCompound)` writes the entity data to the NBT tag provided in the argument

Not to be confused with

`readNbt(NbtCompound)`, which writes the data from the NBT tag to the entity