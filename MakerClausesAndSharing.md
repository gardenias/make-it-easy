If you define the property of an object with a Maker expression, each instance of the object will get a new value made for its property.  Sometimes that is not what you want, so beware.

For example, the Maker below creates new orders from a customer named "Alice".

```
Maker<Order> anOrder = an(Order, with(customer, a(Customer, with(name, "Alice")))));
```

However, every order created by the `anOrder` maker will refer to a _different_ Customer instance that has the name "Alice", rather than all referring to the same Customer instance.

For example, order1 and order2 below are from two different customers that happen to both be called "Alice".

```
Order order1 = make(anOrder);
Order order2 = make(anOrder);
```

That's not what we wanted!  We wanted two orders both from the same customer.

To do that, we can pass a Customer instance, not a `Maker<Customer>`, as the second parameter of the `with(customer, `...`)` clause:

```
Person alice = make(a(Person, with(name, "Alice")));
Maker<Order> anOrder = an(Order, with(customer, alice));

Order order1 = make(anOrder);
Order order2 = make(anOrder);
```

Now order1 and order2 both refer to the same customer.

We can avoid the a variable, and define the Order maker in a single expression, with a "theSame" clause:

```
Maker<Order> anOrder = an(Order, with(customer, theSame(Customer, with(name, "Alice")))));

Order order1 = make(anOrder);
Order order2 = make(anOrder);
```

Again, order1 and order2 both refer to the same customer.