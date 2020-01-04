<h4>Dependency Injection</h4>

Referring to another class by name is bad, an immediate consequence is that if the name of the class changes, the object depending on that class must also change.
A bigger problem is that it makes the object less flexible, only allowing the method to be used for instance of that specific class. 

For example below, the <em>`Gear`</em> class hardcodes a reference to <em>`Wheel`</em> in the <em>`gear_inches`</em> method, it's explicitly declaring that it's <em>only willing to calculate `gear_inches` for instances of `Wheel`</em> even if other objects (i.e. disks) has a diameter and uses gears - it's "stuck" to Wheels.

```
  class Gear
    attr_reader :chainring, :rim, :tire 
    def initialize(chainring, rim, tire)
      @chainring = chainring
      @rim = rim
      @tire = tire
    end
    def gear_inches
      ratio * Wheel.new(rim, tire).diameter
    end
# ...
end
```

It is not the class of the object that’s important, it’s the <em>message</em> you plan to send to it. The less a class knows about an object the better; it doesn’t need to know any other details, only that it can respond to a message (method call). 
Rather than instantiating a new object within a class method, we use a variable to hold and accessor method to access the object; this technique is called <bold>dependency injection</bold>.

For example below, the `Gear` class now uses the `@wheel` variable to hold the wheel method. It no longer cares whether or not `wheel` is an actual instance of wheel, it only knows that it responds to `diameter`. Moving the creation of the new Wheel instance outside of Gear decouples the two classes. `Gear` can now collaborate with any object that implements `diameter`.

```
class Gear
  attr_reader :chainring, :wheel 
  def initialize(chainring, wheel)
    @wheel = wheel  
    @chainring = chainring
   end
  def gear_inches
    ratio * wheel.diameter
  end
# ...
end
```

<h4>Isolate Dependencies</h4>
If dependencies cannot be removed,isolate them within your class (i.e. put them into their own method and reveal them). 
<em>Isolating instance creation</em>
If an instance of another class needs to be created within your class and you're too constrained to use dependency injection, create this instance in the initialize method or put it in it's own class (examples below) rather than in your class' main methods. This is still not great but at least it reduces the number of dependencies in your class methods and exposes your class dependencies, lowering barrier to reusing / refactoring code.

Example 1: create Wheel instance in initialize method instead of in gear_inches
```
class Gear
  attr_reader :chainring, :wheel 
  def initialize(chainring, rim, tire, wheel)
    @wheel = Wheel.new(rim,tire)  
    @chainring = chainring
   end
  def gear_inches
    ratio * wheel.diameter
  end
# ...
end
```
Example 2: create Wheel instance in it's own isolated method instead of in gear_inches

```
class Gear
  attr_reader :chainring, :wheel 
  def initialize(chainring, rim, tire, wheel)
    @rim = rim
    @tire = tire 
    @chainring = chainring
   end
  def gear_inches
    ratio * wheel.diameter
  end
  def wheel
    @wheel ||= Wheel.new(rim,tire)
  end
# ...
end
```
<em>Isolating vulnerable external messages</em>


<h4>Remove argument order dependencies</h4>
Use <em>keyword</em> arguments (arguments that end with `:` like, `arg1:` instead of `arg1`). Keyword arguments can be passed as a hash or explicit keyword syntax in any order, eliminating argument order dependencies. It's acceptable to have some classes take positional argument and others take keyword arguments depending on use case (ex. a point will always take x and y positional arguments, so no need to use keywords)

<h4>Isolate multi-parameter initialization</h4>
If an interface is not part of your application and has dependencies you can’t change, protect your code by creating a wrapper around the dependency. The wrapper is usually a module as opposed to a class, and is responsible for creating new instances of the external class - this conveys the idea that you don’t have instances of the wrapper but are also defining a separate object to send a message to. The wrapper is known as a factory - a object who’s purpose is to create other objects. If we wrap the external dependency in a method that is owned by your own application, this decouples the dependency from the rest of your code and we’ll only need to change the wrapper if external dependency changes.

When choosing a direction of dependency: A class should depend on things that changes less often than itself  
