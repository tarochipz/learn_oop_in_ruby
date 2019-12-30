<h1>Dependency Injection</h1>

Referring to another class by name is bad, an immediate consequence is that if the name of the class changes, the object depending on that class must also change.
A bigger problem is that it makes the object less flexible, only allowing the method to be used for instance of that specific class. 

For example below, the <em>`Gear`</em> class hardcodes a reference to <em>`Wheel`</em> in the <em>`gear_inches`</em> method, it's explicitly declaring that it's <em>only willing to calculate `gear_inches` for instances of `Wheel`</em>.

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

 It is not the class of the object that’s important, it’s the message you plan to send to it. The less a class knows about an object the better; it doesn’t need to know any other details, only that it can respond to a message (method call). 
 Rather than instantiating a new object within a class method, we use a variable to hold and accessor method to access the object - this technique is called dependency injection.
