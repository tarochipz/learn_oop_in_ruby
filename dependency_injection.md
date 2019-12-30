<h1>Dependency Injection</h1>

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
