# Consider static factory Method instead of constructors

Instead of exposing a public constructor to obtain an instance of a class, consider providing a static method:

```
public static Boolean valueOf(boolean b){
	return b ? Boolean.TRUE : Boolean.FALSE;
}
```

This approach is not directly related to the factory method design pattern.

## Pros

1. Unlike constructors, static methods have names:

```
public static String valueOf(int i) {  
	return Integer.toString(i);  
}
```

2. With static methods, there is no need to create a new object each time the method is invoked. This can help reduce memory usage, as objects can be shared:

Flyweight pattern #flyweight: The flyweight pattern reduces memory usage by creating objects that share common data.

Instance Control: Static factory methods allow classes to maintain strict control over the instances that exist at any given time. This allows classes to enforce singleton behaviour or ensure that no two equal instances exist. For example:

```
a.equals(b) if and only if a == b
```

This guarantee is the basis of the flyweight pattern. Enums types provide this guarantee.

3. Unlike constructors, static factory methods can return objects of any subtype of their return type, enabling the creation of different child class instances.

Interface-based framework: Static factory methods for an interface named Type can be placed in a non-instantiable companion class.

4. Static factory methods can return different objects based on input parameters.

For example:

```
public static <E extends Enum<E>> EnumSet<E> noneOf(Class<E> elementType) {  
	Enum<?>[] universe = getUniverse(elementType);  
	if (universe == null)  
		throw new ClassCastException(elementType + " not an enum");  
	  
	if (universe.length <= 64)  
		return new RegularEnumSet<>(elementType, universe);  
	else  
		return new JumboEnumSet<>(elementType, universe);  
}
```

In this EnumSet code, a RegularEnumSet is returned if the provided enum has fewer than 64 elements; otherwise, a JumboEnumSet is returned. This implementation detail is hidden behind the factory method, relieving the client of worrying about it. Furthermore, if new subtypes need to be introduced or existing ones removed, it can be achieved without modifying the client-side code.

5. Static factory methods can be used to create objects of classes that are not known at compile time. This can be beneficial when creating objects of dynamically loaded classes or classes implemented in different modules.

Service Provider Framework is a design pattern that allows applications to dynamically discover and use services or implementations provided by external modules or libraries. It provides a flexible and extensible way to plug in different implementations of a service interface without modifying the core application code.

The service provider framework consists of three key components:

1. Service Interface: It defines the contract or API that the service providers must implement. It represents the functionality or behaviour that the application requires.
2. Provider registration API: used to register the implementations.
3. Service access API: Allow clients to specify the criteria for choosing an implementation. In the absence of criteria, it will return default implementation. 
4. Service provider interface: Describes a factory object that produces instances of the service interface.
   
In the case of the JDBC,

1. Service interface -> JDBC Connection
2. Provider registration API -> DriverManager.registerDriver
3. Service access API -> DriverManager.getConnection
4. Service provider interface -> Driver


## Cons

1. Classes without public or protected constructors cannot be subclassed. This encourages programmers to favor composition over inheritance.    
2. Constructors are typically easier to find compared to static methods, which may require some additional code reading.

### Some conventions

* *`from` - type-conversion method `Date d = Date.from(instant)`
* `of` - aggregation method `Set<Rank> faceCards = EnumSet.of(JACK, QUEEN, KING)`
* `valueOf`, more verbose alternative than `from` and `of`, `BigInteger prime = BigInteger.valueOf(Integer.MAX_VALUE)`
* `instance` or `getInstance`, returns an instance that is described by its parameters, `StackWalker luke = StackWalker.getInstance(options)`
* `create` or `newInstance` the method guarantees each call returns a new instance, `Object newArray = Array.newInstance(classObject, arrayLength)`
* `get`**Type**, like `getInstance` but when factory method is in a different class `FileStore fs = Files.getFileStore(path)`
* `new`**Type**, like `newInstance` but when factory method is in a different class `BufferedReader br = Files.newBufferedReader(path)`
* **type**, a concise alternative to `get`_Type_ and `new`_Type_, `List<Compliant> litany = Collections.list(legacyLitany)`

Consider using static factory methods instead of constructors.