

# Enforce the singleton property with a private constructor or an Enum type


#Singleton is a class that can be instantiated exactly once. It is a stateless object or a unique system component. Singleton can make it difficult to test its clients.

There are two ways to create a Singleton using class and one way using Enum. In both approaches, the class would have a private constructor and a public static member to access the Singleton instance.


1. Public member is a final field 


```
public class Elvis {
	public static final Elvis INSTANCE = new Elvis();
	private Elvis() { }

	private void leave() { }
}
```

The private constructor can be accessed reflectively using the `AccessibleObject.setAccessible` method. To defend against this, modify the constructor to throw an error on the second instance creation.

Pros:

- Simpler
- Exposes the class as a singleton


2. Public member is Static factory method
   
```
public class Elvis {
	private static final Elvis INSTANCE = new Elvis();
	private Elvis() { }

	public static Elvis getInstance() {
		return INSTANCE;
	}

	private void leave() { }
}
```


Pros:

- Flexibility, the returned instance can be modified based on the need (e.g., returning different objects for each thread)
- Can write a generic singleton factory
- The static factory method can be used as a supplier

If we want to make the Singleton class serializable and maintain the Singleton guarantee, declare all the instance fields as transient and provide a `readResolve()` method.

The `transient` field in Java is a field that is not included in the serialization process, allowing you to exclude specific data from being stored or transmitted when working with serialized objects. The `readResolve()` method is called when an object is deserialized. In the `readResolve()` method, the singleton instance can be returned. This will prevent the creation of multiple singleton instances.


```
private Object readResolve() { return INSTANCE; }
```


The third way to implement a Singleton is by declaring a single-element Enum:


```
public enum Elvis {
	INSTANCE;

	private void leave() { }
}
```



The Enum approach is more similar to the public final member approach.

Pros:

- Concise
- Out-of-the-box support for serialization
- Out-of-the-box guarantee against multiple instantiation.

**The single-element enum is the best way to implement a Singleton. However, there is one caveat: it can't be used if the Singleton needs to extend any superclass other than Enum.**

Enum can implement an interface.







