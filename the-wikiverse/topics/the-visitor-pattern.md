# The Visitor Pattern

Overall the `Visitor Pattern` exists as a way of separating the concerns of business and object logic under the umbrella of a single class. Often classes which implement this pattern will interact with a series of more generic 'sub-type' classes that have been defined elsewhere in the library. The nature of the interaction between the `Visitor` and the underlying generic can result in re-defining a behavior for each generic individually, creating repetitive code which is prone to inconsistent handling of what should be otherwise consistent behavior. In order to address this a `Visitor` allows new functionality to be written on top of the underlying object code to separate out business logic into its own sub-type specific method. 

In the case of the `Wikidata Toolkit` there are two classes implementing this pattern: the `SnakVisitor` and `ValueVisitor` classes ([see docs](https://wikidata-toolkit.github.io/Wikidata-Toolkit/index.html)). Each of these two classes consist of 3-7 sub-types of `Snak`s or `Value`'s, and require the implementing class to create a `visit()` function to funnel execution of logic to the appropriately typed method.  

## Value Visitor: 

The `Value Visitor` is the most straightforward to understand of the two without having to understand the overall shape of the Wikidata data model. In short a `Value` could be any of 1 of 7 sub-types that exist in the data set: `EntityIdValue`, `GlobeCoordinatesValue`, `MonolingualTextvalue`, `Quantity Value`, `String Value`, `Time Value`, or an `Unsupported Value`. In order to provide consistent handling of these values each `.visit()` method is passed an appropriately typed copy of the original value instance, providing you with a closure to modify, read, or operate on the value as needed. This prevents the underlying data from being unintentionally modified, while also narrowing the instance type to provide methods and access to class members specific to the type itself. 

In a world where this pattern is not implemented you would first have something like this: 

```java
public void whatKindaResultsAreThese(List<Values> results){
  // etc... with handling for each of the 7 result types
  for (Value value : results) { 
    if (value instanceof TimeValue) {
      handleTimeValue((TimeValue) value);
    } else if (value instanceof StringValue) {
      handleStrigValue((StringValue) value)
    } else {
      // etc... with handling for each of the 7 result types
    }
  }
}
```

This sort of type-narrowing if nested monstrosity is an accident waiting to happen. It provides the opportunity for a consumer to accidentally modify `Value`, and unintentionally pass this modified value backwards into the library accidentally overwriting the underlying Wikidata source. Not only that, but it's a nasty chunk of code to have stack if statements like Oreo's and makes for clunky verbose code you're likely to have to fix later. 

Instead, the `Visitor` pattern abstracts the instancing, and narrowing to the underlying interface/object providing convenient scoped methods that allow a consumer to transform the data however they need for each sub-type individually and cleanly:

```java
// ValueData implements the ValueVisitor and is required to implement a .visit() method for each type...
public class ValueData implements ValueVisitor<ValueData> {
  public String value;
  public ValueType type;

  public enum ValueType {
    EntityId, GlobalCoordinate, MonolingualText, Quantity, String, Time, Unsupported
  }

  @Override
  // .visit() method 'subscribes' when a value is passed, and the underlying object calls
  // the correct overloaded method allowing the class to handle isolate business handling logic...
  public ValueData visit(EntityIdValue value) {
    if (value != null) {
      this.value = value.getId();
      this.type = ValueType.EntityId;
    }
    return this;
  }

  @Override
  public ValueData visit(TimeValue value) {
    if (value != null) {
      this.value = convertToWikidataSearchableDate(value);
      this.type = ValueType.DateTime;
    }
    return this;
  }

  // additional .visit()'s omitted for brevity...
}
```
After creating a class which implements the `Visitor` interface and passes it the implementing class for the type, then you use the `.accpet()` method and pass it a new instance of the visitor to execute the underlying `.visit()` implementations. 

```java
/* 
* where data.getValue() returns an unknown instance of the Value class which is funneled 
* to the underlying ValueData class and the correctly instanced .visit() method defined above...
*/
ValueData copy = data.getValue().accept(new ValueData());

```

For me getting to this Syntax took a lot of learning, and it was all just to be able to read something like an id value from some data... 

## References: 
- [FreeCodeCamp: OO Design Patterns with Java](https://www.freecodecamp.org/news/object-oriented-design-patterns-with-java/)
- [Baeldung: Java Visitor Pattern](https://www.baeldung.com/java-visitor-pattern)
- [Gang of Four Design Patterns](https://springframework.guru/gang-of-four-design-patterns/)
- [Intro Gang of GOF Design Patterns (Geeks for Geeks)](https://www.geeksforgeeks.org/introduction-to-gang-of-fourgof-design-patterns/)
