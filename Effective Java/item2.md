
# Consider a builder when faced with many constructor parameters

Static factories and constructors don't scale well with a large number of optional parameters. If a class has a large number of optional parameters, the *telescoping constructor* pattern can be used to provide constructors with varying numbers of optional parameters. This involves creating a constructor with all the required parameters, another with one optional parameter, another with two optional parameters, and so on. Here is an example:

**DO NOT SCALE**
```
public class NutritionFacts {
    private final int servingSize;  // (mL)            required
    private final int servings;     // (per container) required
    private final int calories;     // (per serving)   optional
    private final int fat;          // (g/serving)     optional
    private final int sodium;       // (mg/serving)    optional
    private final int carbohydrate; // (g/serving)     optional

    public NutritionFacts(int servingSize, int servings) {
        this(servingSize, servings, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories) {
        this(servingSize, servings, calories, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat) {
        this(servingSize, servings, calories, fat, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium) {
        this(servingSize, servings, calories, fat, sodium, 0);
    }

    public NutritionFacts(int servingSize, int servings, int calories, int fat, int sodium, int carbohydrate) {
        this.servingSize  = servingSize;
        this.servings     = servings;
        this.calories     = calories;
        this.fat          = fat;
        this.sodium       = sodium;
        this.carbohydrate = carbohydrate;
    }
}
```


An instance with all parameters 
```
NutritionFacts cocaCola = new NutritionFacts(240, 8, 100, 0, 35, 27);
```

While the *telescoping constructor* pattern works, it is not maintainable and, more importantly, not readable.

Another alternative to solving the problem with a large number of optional parameters is using the *JavaBeans pattern*. In this pattern, a public constructor with no arguments is exposed, and the class attributes are mutated using getters and setters.

**Attributes are initialised to default values**
```
public class NutritionFacts {  
	  
	private int servingSize = -1;    // (mL)             required  
	private int servings = -1;       // (per container)  required  
	private int calories = 0;        // (per serving)    optional  
	private int fat = 0;             // (g/serving)      optional  
	private int sodium = 0;          // (mg/serving)     optional  
	private int carbohydrate = 0;    // (g/serving)      optional  
	  
	public NutritionFacts() { }  
	  
	public void setServingSize(int servingSize) {  
		this.servingSize = servingSize;  
	}  
	  
	public void setServings(int servings) {  
		this.servings = servings;  
	}  
	  
	public void setCalories(int calories) {  
		this.calories = calories;  
	}  
	  
	public void setFat(int fat) {  
		this.fat = fat;  
	}  
	  
	public void setSodium(int sodium) {  
		this.sodium = sodium;  
	}  
	  
	public void setCarbohydrate(int carbohydrate) {  
		this.carbohydrate = carbohydrate;  
	}
}
```


An instance with all parameters 

```
NutritionFacts cocaCola = new NutritionFacts();  
cocaCola.setServingSize(240);  
cocaCola.setServings(8);  
cocaCola.setCalories(100);  
cocaCola.setSodium(35);  
cocaCola.setCarbohydrate(27);
```

The *JavaBeans* pattern addresses the drawbacks of the *telescoping constructor* pattern by improving the readability of the code. However, instances can be in an inconsistent state during construction. Also, by exposing public setter methods, we are making the class mutable, which might require more effort to keep it **thread-safe**.

The third alternative is the Builder pattern.

```
public class NutritionFacts {  

    private final int servingSize;  // (mL)            required
    private final int servings;     // (per container) required
    private final int calories;     // (per serving)   optional
    private final int fat;          // (g/serving)     optional
    private final int sodium;       // (mg/serving)    optional
    private final int carbohydrate; // (g/serving)     optional
	  
	public static class Builder {  
		  
		// Required parameters  
		private final int servingSize;  
		private final int servings;  
		  
		// Optional parameters with default parameters  
		private int calories = 0;  
		private int fat = 0;  
		private int sodium = 0;  
		private int carbohydrate = 0;  
		  
		public Builder(int servingSize, int servings) {  
			this.servingSize = servingSize;  
			this.servings = servings;  
		}  
		  
		public Builder calories(int val) {  
			calories = val;  
			return this;  
		}  
		  
		public Builder fat(int val) {  
			fat = val;  
			return this;  
		}  
		  
		public Builder sodium(int val) {  
			sodium = val;  
			return this;  
		}  
		  
		public Builder carbohydrate(int val) {  
			carbohydrate = val;  
			return this;  
		}  
		  
		public NutritionFacts build(){  
			return new NutritionFacts(this);  
		}  
	}  
	  
	private NutritionFacts(Builder builder) {  
		servingSize = builder.servingSize;  
		servings = builder.servings;  
		calories = builder.calories;  
		fat = builder.fat;  
		sodium = builder.sodium;  
		carbohydrate = builder.carbohydrate;  
	}  
}
```


An instance with all parameters 

```
NutritionFacts cocaCola = new NutritionFacts.Builder(240, 8)  
	.calories(100)  
	.sodium(35)  
	.carbohydrate(27)  
	.build();
```

The above code is easy to read and also provides a stimulation of named optional parameters.

The Builder Pattern is well-suited for class hierarchies.


```
abstract class Pizza {  
	public enum Topping {HAM, MUSHROOM, ONION, PEPPER, SAUSAGE}  
	final Set<Topping> toppings;  
	  
	abstract static class Builder<T extends Builder<T>> {  
		EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);  
		
		public T addTopping(Topping topping) {  
			toppings.add(Objects.requireNonNull(topping));  
			return self();  
		}  
		  
		abstract Pizza build();  
	  
		protected abstract T self();  
	}  
	  
	Pizza(Builder<?> builder) {  
	toppings = builder.toppings.clone();  
	}  
}  
  
class NyPizza extends Pizza {  
	public enum Size {SMALL, MEDIUM, LARGE};  
	private final Size size;  
	  
	public static class Builder extends Pizza.Builder<Builder> {  
	  
		private final Size size;  
			public Builder(Size size) {  
			this.size = Objects.requireNonNull(size);  
		}  
		  
		  
		@Override  
		NyPizza build() {  
			return new NyPizza(this);  
		}  
		  
		@Override  
		protected Builder self() {  
			return this;  
		}  
	}  
	
	NyPizza(Builder builder) {  
		super(builder);  
		size = builder.size;  
	}  
}  
  
class Calzone extends Pizza {  
	private final boolean sauceInside;  
	  
	public static class Builder extends Pizza.Builder<Builder> {  
		  
		private boolean sauceInside = false;  
		  
		public Builder sauceInside() {  
			sauceInside = true;  
			return this;  
		}  
		  
		  
		@Override  
		Calzone build() {  
			return new Calzone(this);  
		}  
		  
		@Override  
		protected Builder self() {  
			return this;  
		}  
	}  
	Calzone(Builder builder) {  
		super(builder);  
		sauceInside = builder.sauceInside;  
	}  
}
```


```
NyPizza nyPizza = new NyPizza.Builder(SMALL)  
	.addTopping(ONION)  
	.addTopping(SAUSAGE)  
	.build();  
  
  
Calzone calzone = new Calzone.Builder()  
	.addTopping(HAM)  
	.sauceInside()  
	.build();
```


The Builder Pattern has several advantages, such as:

Pros:

- It can have multiple varargs parameters, and builders can aggregate the parameters passed in multiple calls into a single field.
- It is flexible and can be used to create multiple objects by calling the builder repeatedly.
- It can automatically fill some fields with default values.

However, there are also some drawbacks to consider:

Cons:

- The builder object needs to be created each time, which might be a problem for performance-critical projects.
- It is more verbose compared to other patterns.
