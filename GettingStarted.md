Assuming you've downloaded Make It Easy and added the JAR to your classpath, here's how you use the library to write a Test Data Builder for the following class.

```
public class Person {
    private String name;
    private int age;

    public Person(String name, int age) {
        this.name = name;
        this.age = age;
    }

    // and some methods that do things with that state

}
```

## Writing the Person Maker ##

You need to define the properties and instantiator of Person objects as public, static fields of some class.  Let's call that class PersonMaker:

```
public class PersonMaker {
    // we will define the properties and instantiator here
}
```

Now we need to define the properties of a Person object that are used to initialise its state.  In this case, a Person is initialised with a name, of type String, and an age, of type int.  We define these using the convenience method Property.newProperty():

```
public static final Property<Person,String> name = newProperty();
public static final Property<Person,Integer> age = newProperty();
```

Finally we need to define now a Person is instantiated using these properties and the default values used when a test does not define values for these properties.  This is done by implementing the Instantiator interface.  By convention, the instantiator is a constant that has the same name as the type it instantiates.

```
public static final Instantiator<Person> Person = new Instantiator<Person>() {
    public Person instantiate(PropertyLookup<Person> lookup) {
        return new Person(lookup.valueOf(name, "Default Name"),
                          lookup.valueOf(age, 99));
    }
};
```

Putting all that together:

```
public class PersonMaker {
    public static final Property<Person,String> name = newProperty();
    public static final Property<Person,Integer> age = newProperty();

    public static final Instantiator<Person> Person = new Instantiator<Person>() {
        public Person instantiate(PropertyLookup<Person> lookup) {
            return new Person(lookup.valueOf(name, "Bob"), // default name is "Bob"
                              lookup.valueOf(age, 99));    // default age is 99
        }
    };
}
```

## Using the Person Maker ##

To make Person objects we import the syntactic sugar from the MakeItEasy and PersonMaker classes:

```
import static com.natpryce.MakeItEasy.*;
import static example.PersonMaker.*;
```

We then create new Person objects with default properties like this:

```
Person somebody = make(a(Person));
```

We can specify properties like this:

```
Person nat = make(a(Person, with(name, "Nat"), with(age, 21)));
```

We can define a person maker to be used multiple times, as follows.  Note that we've not defined all the properties, so any Person made by the oldPerson maker has the default name defined by the Instantiator we wrote above.

```
Maker<Person> anOldPerson = a(Person, with(age, 80));

doSomethingWith(make(anOldPerson));
```

We can use that to define other person makers:

```
Maker<Person> anOldWoman = anOldPerson.but(with(name, "Alice"));
Maker<Person> anOldMan = anOldPerson.but(with(name, "Bill"));
```

## Where Next? ##

  * MakerClausesAndSharing