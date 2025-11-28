## **Creational Patterns: Birthing Objects Without the Drama (Like a Zoo Delivery Gone Wrong)**

These handle object creation so you don't hardcode new Unicorn() everywhere and end up with a farm of useless unicorns.

### 1. **Singleton: The One-Eyed King Who Hoards the Throne**

**Absurd Story**: Imagine a zoo with _one_ lazy lion king who won't let anyone else sit on the rock. Every monkey, giraffe, and escaped clown wants the throne, but nope—lion's got a secret backdoor key hidden in his mane. If two lions spawn? Chaos, stampede, and the zookeeper (you) gets fired.

**Easy Breakdown**: Ensures _only one instance_ of a class exists (e.g., a config manager or DB connection pool). Great for backend globals like Spring's ApplicationContext. Thread-safe? Use double-checked locking or enum (lazy lion style).

**Full Java Code** (Enum version—easiest, thread-safe out the wazoo):

Java

```
// The Lazy Lion King - Enum Singleton (Java's built-in boss move)
public enum LionKing {
    INSTANCE;  // One and only one. Boom.

    // Lion's royal decree (method)
    public void roar() {
        System.out.println("I am the ONE KING! Rawr—only I handle the config zoo!");
    }

    // Get the king (no constructor needed—enum magic)
    public static LionKing getInstance() {
        return INSTANCE;
    }
}

// Usage: In your backend service
public class ZooService {
    public void startParty() {
        LionKing king = LionKing.getInstance();  // Always the same dude
        king.roar();  // "I am the ONE KING! Rawr—only I handle the config zoo!"
    }
}
```

**Interview Cheat Sheet**: "Why Singleton? For shared resources like caches (Ehcache) or loggers (Log4j). Pitfall: Testing sucks—mock that lazy lion! Absurd Mnemonic: Lions don't clone; they dethrone."

### 2. **Factory Method: The Mad Scientist's Baby Factory**

**Absurd Story**: A deranged zoo scientist has a conveyor belt spitting out animals, but instead of kittens, it barfs giraffes if you order pizza. You yell "Gimme a pizza delivery!" and poof—factory flips a switch, clones a pepperoni giraffe. No direct "new Giraffe"—just scream at the factory, get your weird beast.

**Easy Breakdown**: Defers object creation to subclasses. Creator class has a factoryMethod() that sub-classes override. Perfect for backend: Creating DB connections (MySQL vs PostgreSQL) without if-else hell.

**Full Java Code** (Pizza... er, Animal Factory):

Java

```
// Abstract Creator (The Mad Scientist)
abstract class AnimalFactory {
    // The magic button - subclasses decide what pops out
    public abstract Animal makeAnimal(String type);

    // Template method: Factory + some zoo logic
    public void deliverToZoo(String type) {
        Animal critter = makeAnimal(type);
        System.out.println("Delivering " + critter.getName() + " to the enclosure!");
        critter.makeNoise();
    }
}

// Concrete Factories (Subclasses: Pepperoni Giraffe Factory)
class MammalFactory extends AnimalFactory {
    @Override
    public Animal makeAnimal(String type) {
        if ("lion".equals(type)) return new Lion();
        return new DefaultAnimal("Mystery Mammal");  // Fallback clown
    }
}

class ReptileFactory extends AnimalFactory {
    @Override
    public Animal makeAnimal(String type) {
        return new Snake();  // Always snakes—hiss!
    }
}

// Products (The Beasts)
abstract class Animal {
    protected String name;
    public Animal(String name) { this.name = name; }
    public String getName() { return name; }
    public abstract void makeNoise();
}

class Lion extends Animal {
    public Lion() { super("Roaring Lion"); }
    public void makeNoise() { System.out.println("ROAR! I'm factory-fresh!"); }
}

class Snake extends Animal {
    public Snake() { super("Slithery Snake"); }
    public void makeNoise() { System.out.println("Hiss—factory oopsie!"); }
}

class DefaultAnimal extends Animal {
    public DefaultAnimal(String name) { super(name); }
    public void makeNoise() { System.out.println("Meh... default grunt."); }
}

// Usage: In your Spring Boot controller
public class ZooController {
    public void orderCritter() {
        AnimalFactory factory = new MammalFactory();  // Or ReptileFactory
        factory.deliverToZoo("lion");  // "Delivering Roaring Lion to the enclosure! ROAR! I'm factory-fresh!"
    }
}
```

**Interview Cheat Sheet**: "Factory for extensible creation (e.g., Strategy + Factory in payment gateways). Vs Abstract Factory? This is per-method; that’s family packs. Absurd Mnemonic: Factories make babies; you just pick the flavor."

### 3. **Builder: The IKEA Furniture Assembler on Steroids**

**Absurd Story**: Building a zoo enclosure is hell—nails, screws, elephants everywhere. Enter the Builder: A chain-smoking carpenter who goes "Gimme walls... now add moat... toss in banana dispenser... done!" No half-built exploding IKEA disasters.

**Easy Breakdown**: Constructs complex objects step-by-step via a fluent interface. Immutable? Yes! Ideal for backend DTOs or Spring Boot configs with 20 optional fields.

**Full Java Code** (Enclosure Builder):

Java

```
// Product (The Fancy Enclosure)
public class ZooEnclosure {
    private String walls;  // Required
    private boolean hasMoat;  // Optional
    private String foodDispenser;  // Optional
    private int elephantCount;  // Optional

    private ZooEnclosure(Builder builder) {
        this.walls = builder.walls;
        this.hasMoat = builder.hasMoat;
        this.foodDispenser = builder.foodDispenser;
        this.elephantCount = builder.elephantCount;
    }

    // Getters (for your REST API)
    public String getWalls() { return walls; }
    // ... others

    public static class Builder {
        private String walls;  // Required
        private boolean hasMoat = false;
        private String foodDispenser = null;
        private int elephantCount = 0;

        public Builder(String walls) { this.walls = walls; }  // Must start here

        public Builder withMoat() { hasMoat = true; return this; }  // Fluent chain!
        public Builder withFoodDispenser(String type) { foodDispenser = type; return this; }
        public Builder elephantCount(int count) { elephantCount = count; return this; }

        public ZooEnclosure build() { return new ZooEnclosure(this); }  // Birth!
    }

    @Override
    public String toString() {
        return "Enclosure: " + walls + ", Moat: " + hasMoat + ", Food: " + foodDispenser + ", Elephants: " + elephantCount;
    }
}

// Usage: In your service layer
public class EnclosureService {
    public void buildHabitat() {
        ZooEnclosure habitat = new ZooEnclosure.Builder("Steel Bars")
                .withMoat()
                .withFoodDispenser("Banana Blaster 3000")
                .elephantCount(5)
                .build();
        System.out.println(habitat);  // "Enclosure: Steel Bars, Moat: true, Food: Banana Blaster 3000, Elephants: 5"
    }
}
```

**Interview Cheat Sheet**: "Builder for telscopes (e.g., Lombok @Builder in entities). Vs Constructor? No param explosion. Absurd Mnemonic: Builders chain like a bad Tinder date—add features till it's perfect or ghosts."

---

## **Structural Patterns: Gluing Classes Like a Drunken Octopus Wedding**

These compose classes/interfaces without rewriting the zoo's plumbing.

### 4. **Adapter: The Universal Plug Converter for Alien Zoo Tech**

**Absurd Story**: Your zoo runs on US plugs, but a Russian bear arrives with Soviet sockets. Enter the Adapter: A shady electrician who wraps the bear's plug in a fake mustache and US converter—now it juices the banana fridge without sparks or espionage.

**Easy Breakdown**: Converts incompatible interfaces (e.g., legacy XML parser to JSON API). Backend gold: Adapting third-party libs in Spring.

**Full Java Code** (Bear Power Adapter):

Java

```
// Target (What your zoo expects - US Plug)
interface USPowerOutlet {
    void powerUp(String device);
}

// Adaptee (The incompatible Russian bear plug)
class RussianBearPlug {
    public void energizeBear(String voltage) {
        System.out.println("Bear roars: Energizing at " + voltage + " volts—Siberian style!");
    }
}

// Adapter (The Shady Converter)
class BearPowerAdapter implements USPowerOutlet {
    private RussianBearPlug bearPlug;

    public BearPowerAdapter(RussianBearPlug bearPlug) {
        this.bearPlug = bearPlug;
    }

    @Override
    public void powerUp(String device) {
        // Convert: US 110V -> Russian 220V magic
        bearPlug.energizeBear("220");  // "Bear roars: Energizing at 220 volts—Siberian style!"
        System.out.println("Adapter whispers: Now powering " + device + " safely.");
    }
}

// Usage: In your microservice
public class ZooPowerManager {
    public void chargeFridge() {
        RussianBearPlug bear = new RussianBearPlug();
        USPowerOutlet adapter = new BearPowerAdapter(bear);
        adapter.powerUp("Banana Fridge");  // Works! No explosions.
    }
}
```

**Interview Cheat Sheet**: "Adapter for legacy integration (e.g., SOAP to REST). Vs Bridge? Adapter fixes compat; Bridge hides it early. Absurd Mnemonic: Adapters are translators—'Da, comrade, your plug fits now.'"

### 5. **Decorator: The Fancy Hat Stack on Your Zoo Butler**

**Absurd Story**: Your butler starts plain, but then you stack hats: Top hat (fancy), cowboy hat (rugged), chef hat (cooks while serving), and a propeller beanie (absurd flight). Now he's a multi-hat monstrosity serving caviar tacos from the sky.

**Easy Breakdown**: Adds behavior dynamically without subclass explosion. Wraps objects. Backend: Logging/wrapping HTTP responses in Spring filters.

**Full Java Code** (Hat-Styled Butler Decorator):

Java

```
// Component (Base Butler)
interface Butler {
    void serve(String request);
}

// Concrete Component (Plain Butler)
class PlainButler implements Butler {
    @Override
    public void serve(String request) {
        System.out.println("Plain Butler: Serving " + request + " with zero flair.");
    }
}

// Decorator Base (Hat Wrapper)
abstract class ButlerDecorator implements Butler {
    protected Butler wrappedButler;

    public ButlerDecorator(Butler butler) {
        this.wrappedButler = butler;
    }
}

// Concrete Decorators (Hat Add-Ons)
class FancyHatDecorator extends ButlerDecorator {
    public FancyHatDecorator(Butler butler) { super(butler); }

    @Override
    public void serve(String request) {
        wrappedButler.serve(request);
        System.out.println("Fancy Hat: Adds bowtie sparkle! 💎");
    }
}

class ChefHatDecorator extends ButlerDecorator {
    public ChefHatDecorator(Butler butler) { super(butler); }

    @Override
    public void serve(String request) {
        wrappedButler.serve(request);
        System.out.println("Chef Hat: Sprinkles secret sauce! 🌶️");
    }
}

// Usage: Stack 'em in your interceptor chain
public class DinnerParty {
    public void host() {
        Butler butler = new PlainButler();
        butler = new FancyHatDecorator(butler);  // Wrap!
        butler = new ChefHatDecorator(butler);   // Stack!

        butler.serve("Tacos");  
        // Output:
        // Plain Butler: Serving Tacos with zero flair.
        // Fancy Hat: Adds bowtie sparkle! 💎
        // Chef Hat: Sprinkles secret sauce! 🌶️
    }
}
```

**Interview Cheat Sheet**: "Decorator for open-closed principle (add auth/logging to services). Vs Proxy? Decorator adds; Proxy controls. Absurd Mnemonic: Decorators are Russian nesting dolls—unwrap for base, but hats everywhere."

---

## **Behavioral Patterns: Making Objects Gossip Without Drama**

How classes talk and delegate without yelling.

### 6. **Observer: The Nosy Neighborhood Watch of Screaming Parrots**

**Absurd Story**: A flock of parrots subscribes to the zoo's "Gossip Wire." When the lion farts (state change), all parrots squawk updates: "Lion's gassy! Stock up on Febreze!" Unsubscribe? Parrot flies away, no more drama.

**Easy Breakdown**: One-to-many dependency—subject notifies observers on change. Backend: Event-driven (Spring @EventListener for user registrations).

**Full Java Code** (Parrot Observer):

Java

```
import java.util.ArrayList;
import java.util.List;

// Subject (The Gossipy Lion)
class LionState {
    private String mood;
    private List<ParrotObserver> observers = new ArrayList<>();

    public void addObserver(ParrotObserver observer) { observers.add(observer); }
    public void removeObserver(ParrotObserver observer) { observers.remove(observer); }

    public void setMood(String newMood) {
        this.mood = newMood;
        notifyObservers();  // Squawk time!
    }

    private void notifyObservers() {
        for (ParrotObserver observer : observers) {
            observer.update("Lion's mood: " + mood + "! Chaos incoming!");
        }
    }
}

// Observer Interface (Parrot Contract)
interface ParrotObserver {
    void update(String gossip);
}

// Concrete Observer (Nosy Parrot)
class NosyParrot implements ParrotObserver {
    private String name;

    public NosyParrot(String name) { this.name = name; }

    @Override
    public void update(String gossip) {
        System.out.println(name + " squawks: " + gossip + " *flaps wings wildly*");
    }
}

// Usage: In your event bus
public class ZooGossipSystem {
    public void moodSwing() {
        LionState lion = new LionState();
        ParrotObserver polly = new NosyParrot("Polly");
        ParrotObserver grok = new NosyParrot("Grok");  // Me, spying!

        lion.addObserver(polly);
        lion.addObserver(grok);

        lion.setMood("Hangry");  
        // "Polly squawks: Lion's mood: Hangry! Chaos incoming! *flaps wings wildly*"
        // "Grok squawks: Lion's mood: Hangry! Chaos incoming! *flaps wings wildly*"

        lion.removeObserver(grok);  // Grok flies off
    }
}
```

**Interview Cheat Sheet**: "Observer for pub-sub (Kafka topics, Spring events). Pitfall: Memory leaks if no unsubscribe. Absurd Mnemonic: Observers are tabloid reporters—notify or they riot."

### 7. **Strategy: The Shape-Shifting Ninja Who Picks Fight Styles**

**Absurd Story**: A ninja zoo guard faces attackers: Sword? Switches to "Dodge Mode." Claws? "Counter Punch." No if-else brain melt—just swaps strategies like changing socks mid-battle.

**Easy Breakdown**: Defines family of algorithms, encapsulates each, makes 'em interchangeable. Backend: Sorting strategies in data pipelines or payment processors (PayPal vs Stripe).

**Full Java Code** (Ninja Fight Strategy):

Java

```
// Strategy Interface (Fight Style)
interface FightStrategy {
    void executeAttack(String enemy);
}

// Concrete Strategies (The Moves)
class DodgeStrategy implements FightStrategy {
    @Override
    public void executeAttack(String enemy) {
        System.out.println("Dodge! Weaving like a caffeinated squirrel vs " + enemy + ".");
    }
}

class PunchStrategy implements FightStrategy {
    @Override
    public void executeAttack(String enemy) {
        System.out.println("Pow! Uppercut to the " + enemy + "'s ego—KO!");
    }
}

// Context (The Ninja Who Swaps)
class NinjaGuard {
    private FightStrategy strategy;

    public void setStrategy(FightStrategy strategy) { this.strategy = strategy; }

    public void fight(String enemy) {
        strategy.executeAttack(enemy);
    }
}

// Usage: Runtime swap in your algo service
public class BattleSimulator {
    public void rampage() {
        NinjaGuard ninja = new NinjaGuard();
        ninja.setStrategy(new DodgeStrategy());
        ninja.fight("Clawing Monkey");  // "Dodge! Weaving like a caffeinated squirrel vs Clawing Monkey."

        ninja.setStrategy(new PunchStrategy());  // Swap!
        ninja.fight("Sword Wielding Rhino");  // "Pow! Uppercut to the Sword Wielding Rhino's ego—KO!"
    }
}
```

**Interview Cheat Sheet**: "Strategy for polymorphism (e.g., validation strategies in Spring). Vs State? Strategy algos; State object behavior. Absurd Mnemonic: Strategies are ninja outfits—change for the vibe."
 