# AdvancementAPI

AdvancementAPI is library made to easily create Advancement for your server using built in Bukkit/Spigot API features.

Original repository created by [Roxeez](https://github.com/Roxeez/AdvancementAPI).

Transited to Maven from Gradle.

## How To Use

**Add Jitpack repository to pom.xml:**
```xml
<repositories>
    <repository>
        <id>jitpack.io</id>
        <url>https://jitpack.io</url>
    </repository>
</repositories>
``` 

**Then add this dependency to pom.xml:**
```xml
<dependencies>
    <dependency>
        <groupId>com.github.bottleofench</groupId>
        <artifactId>AdvancementAPI</artifactId>
        <version>59258e349c</version>
    </dependency>
</dependencies>
``` 

## Example
**Creating root advancement unlocked by collecting 3 cobblestone**
```java
public class CobblestoneAdvancement implements AdvancementCreator {

    public static final String ID = "some_cobblestone";
    
    @Override
    public @NotNull Advancement create(@NotNull Context context) {
        
        Advancement advancement = new Advancement(context.getPlugin(), ID);

        advancement.setDisplay(x -> {
            x.setTitle("Some Cobblestone");
            x.setDescription("Get 3 cobblestone");
            x.setBackground(BackgroundType.STONE);
            x.setIcon(Material.COBBLESTONE);
        });

        advancement.addCriteria("cobblestone", TriggerType.INVENTORY_CHANGED, trigger -> {
            trigger.hasItemMatching(item -> {
                item.setType(Material.COBBLESTONE);
                item.setCount(3);
            });
        });
        
        return advancement;
    }

}
```  

**Creating child advancement of previous one unlocked by crafting a stone pickaxe**
```java
public class StonePickaxeAdvancement implements AdvancementCreator {

    public static final String ID = "stone_pickaxe";

    @Override
    public @NotNull Advancement create(@NotNull Context context) {

        Advancement advancement = new Advancement(context.getPlugin(), ID);

        advancement.setParent(new NamespacedKey(context.getPlugin(), CobblestoneAdvancement.ID));

        advancement.setDisplay(x -> {
            x.setTitle("Stone Pickaxe");
            x.setDescription("Craft a stone pickaxe with your 3 cobblestone");
            x.setIcon(Material.STONE_PICKAXE);
        });

        advancement.addCriteria("pickaxe", TriggerType.INVENTORY_CHANGED, trigger -> {
            trigger.hasItem(Material.STONE_PICKAXE);
        });

        return advancement;
    }

}
```  

**Register previously created advancements**
```java
public class MyPlugin extends JavaPlugin {

    private final AdvancementManager manager = new AdvancementManager(this);

    @Override
    public void onEnable() {
        manager.register(new CobblestoneAdvancement());
        manager.register(new StonePickaxeAdvancement());
        
        manager.createAll();
    }

}
```
