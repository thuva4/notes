# Enforce non-instantiability with a private constructor

When developing utility classes that consist solely of static methods and static fields, their purpose is to group related functionalities and data. These classes are not intended to be instantiated. By declaring the class as **Abstract**, we prevent direct instantiation. However, it is still possible for someone to extend the class and create an instance of the subclass. To prevent such scenarios, we can include a private constructor.


```
// Noninstantiable utility class**
public class UtilityClass {
    **// Suppress default constructor for noninstantiability**
    private UtilityClass() {
        throw new AssertionError();
    }
    ...  // Remainder omitted
}
```


The private constructor ensures that it cannot be accessed from outside the class. Although not strictly necessary, adding an `AssertionError` within the constructor acts as a safeguard in case the constructor is accidentally invoked from within the class. This ensures that the class remains uninstantiated in any circumstances.

Furthermore, this approach also prevents the class from being subclassed. Since all constructors must invoke a superclass constructor either explicitly or implicitly, a subclass would have no accessible superclass constructor to invoke in this case.