# Java Advanced Note

### Variable Arguments (VarArgs)

```java
public static int add(int x, int ...args) { // ...args refers to variable parameter, namely potential multiple number of parameters
    
    int sum = x;
    for(int i : args) { // args is regarded as an array
        sum += i;
    }
    return sum;
}
```

### 自动装箱、拆箱

```java
Integer x = 1; 
Integer y = 1; 
System.out.println(x == y); // true, only for x and y between -128 and 127 

Integer x = 200; 
Integer y = 200; 
System.out.println(x == y); // false
```

### Enum 

```java
public static void main(String[] args) {

    Weekday weekday1 = Weekday.SUN;
    System.out.println(weekday1); // output is SUN
    
    System.out.println(Weekday.values().length); // Weekday.values() returns Weekday[]
}

public enum Weekday {
    MON,
    TUE,
    WED,
    THUR,
    FRI,
    SAT,
    SUN; // must be on top in the enum
}
```

### Enum with Abstract Methods

```java
public enum TrafficLight {
    RED(30) { // child class
        public TrafficLight nextLight() {
            return GREEN;
        }
    }, 
    GREEN(45) {
        public TrafficLight nextLight() {
            return YELLOW;
        }
    }, 
    YELLOW(5) {
        public TrafficLight nextLight() {
            return RED;
        }
    };
    
    public abstract TrafficLight nextLight();
    
    private int time; 
    
    private TrafficLight(int time) {
        this.time = time;
    }
}
```

If there is only one member in the enum, this can be used as an implementation of singleton pattern. 

---

### Reflection（反射）

Reflection will decrease the performance of program. 

如何得到字节码对应的实例对象： 

- `<Class_name>.class`, e.g. `Date.class` 
- `<object>.getClass()`, e.g. `new Date().getClass()`
- `Class.forName("full_class_name")`, e.g. `Class.forName("java.lang.String")`

```java
String str = "abc"; 
Class cls1 = str.getClass(); 
Class cls2 = String.class; 
Class cls3 = Class.forName("java.lang.String");

System.out.println(cls1 == cls2);  // true
System.out.println(cls2 == cls3);  // true

System.out.println(cls1.isPrimitive());  // false 
System.out.println(int.class.isPrimitive());  // true
System.out.println(int.class == Integer.class);  // false 
System.out.println(int.class == Integer.TYPE);  // true
System.out.println(int[].class.isPrimitive());  // false
System.out.println(int[].class.isArray());  // true 
```

#### Constructor 类

```java
// create an object by using Constructor Class with reflection
Constructor constructor1 = String.class.getConstructor(StringBuffer.class);
String str1 = (String)constructor1.newInstance(new StringBuffer("abc"));
System.out.println(str1.charAt(2));  // output should be "c"


// if you only use non-param constructor, do not need to create a constructor, but use internal default constructor instead 
String str2 = (String)Class.forName("java.lang.String").newInstance();

```

