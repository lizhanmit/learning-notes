# Java Advanced Note

### Variable Arguments (VarArgs)

introduced in JDK 1.5 

```java
public static int add(int x, int ...args) { // ...args refers to variable parameter, namely potential multiple number of parameters
    
    int sum = x;
    for (int i : args) { // args is regarded as an array
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

introduced in JDK 1.5 

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

Why to use reflection: 

- Used in the scenario that the name of class is unknown.
- Increase the flexibility of program.
- Used to implement framework.

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

#### Field 类

```java
public class Main() {
    public static void main(String[] args) {
        ReflectionPoint point1 = new ReflectionPoint(3, 5);
        
        Field fieldY = point1.getClass().getField("Y");
        System.out.println(fieldY.get(point1));  // output should be 5
        
        Field fieldX = point1.getClass().getField("X");
        System.out.println(fieldX.get(point1));  // exception, as X is private, so getField() method can only access public field
        
        Field fieldX = point1.getClass().getDeclaredField("X");  // can access field X here
        System.out.println(fieldX.get(point1));  // but cannot get the value of field X, as X is private
        
        // in order to get field X value of point1 
        fieldX.setAccessible(true);
        System.out.println(fieldX.get(point1));  // output should be 3
        
        
        // for all String type fields, change "a" to "b" of its value
        changeAtoB(point1);
        System.out.println(point1);  // output should be "bpple, bbnbnb"
    }
    
    public static void changeAtoB(Object obj) {
        Field[] fields = obj.getClass().getFields();
        for (Field field : fields) {
            if (field.getType == String.class) {
                String oldValue = (String)field.get(obj);
                String newValue = oldValue.replace("a", "b");
                field.set(obj, newValue);
            }
        }
    }
}

public class ReflectionPoint() {
    private int x; 
    public int y;
    public String s1 = "apple";
    public String s2 = "banana";
    
    public ReflectionPoint(int x, int y) {
        super();
        this.x = x;
        this.y = y;
    }
    
    @Override
    public String toString() {
        return s1 + ", " + s2;
    }
}
```

#### Method 类

```java
public static void main(String[] args) {
    String str1 = "abc";
    // in general 
    // str1.charAt(1);
    // using reflection 
    Method methodCharAt = String.class.getMethod("charAt", int.class);
    System.out.println(methodCharAt.invoke(str1, 1));  // output should be "b"
}
```

If the 1st parameter of `invoke()` is `null`, then the method is a static method. 

#### Array 类

```java
public static void main(String[] args) {
	String[] s = new String[]{"a", "b", "c"};
    printObject(s);
    // output should be 
    // a
    // b
    // c
}

public static void printObject(Object obj) {
    Class cls = obj.getClass();
    // if the type of obj is Array, iterate it and print all elements
    if (cls.isArray()) {
        int len = Array.getLength(obj);
        for (int i = 0; i < len; i++) {
            System.out.println(Array.get(obj, i));
        }
    } else {
        System.out.println(obj);
    }
}
```

#### Manage Config File

Create config.properties file.  Write `className=java.util.ArrayList` in it.

```java
public class Main() {
    public static void main(String[] args) {
        // do not do this - relative path - in real projects
        // InputStream inputStream = new FileInputStream("config.properties");  

		InputStream inputStream = Main.class.getClassLoader().getResourceAsStream("<project_relative_path>/config.properties");
        // alternative way 
        // InputStream inputStream = Main.class.getResourceAsStream("<Main.class_relative_path> or <project_absolute_path>/config.properties");
        Properties props = new Properties();
        props.load(inputStream);
        props.close();

        String className = props.getProperty("className");
        Collection coll = (Collection)Class.forName(className).newInstance();

        coll.add(1);
        coll.add(2);
        System.out.println(coll.size());  // output should be 2
    }
}
```

#### Introspection & JavaBean

Get and set properties:

```java
public class Main() {
    public static void main(String[] args) {
        ReflectPoint pt1 = new ReflectPoint(3, 5);
        
        String propertyName = "x"; 
        
        PropertyDescriptor pd = new PropertyDescriptor(propertyName, pt1.getClass());
        
        Method methodGetX = pd.getReadMethod();
        Object retVal = methodGetX.invoke(pt1);
        System.out.println(retVal);  // output should be 3
        
        Method methodSetX = pd.getWriteMethod();
        methodSetX.invoke(pt1, 7);
        System.out.println(pt1.getX());  // output should be 7      
    }
}

// JavaBean
public class ReflectPoint() {
    private int x;
    private int y;
    private Date birthday = new Date();
    
    public ReflectPoint(int x, int y) {
        super();
        this.x = x; 
        this.y = y;
    }
    
    public int getX() {
        return x;
    }
    
    public void setX(int x) {
        this.x = x;
    }
    
    public int getY() {
        return y;
    }
    
    public void setY(int y) {
        this.y = y;
    }
    
    public int getBirthday() {
        return birthday;
    }
    
    public void setBirthday(Date birthday) {
        this.birthday = birthday;
    }
}
```

#### BeanUtils & JavaBean

Use BeanUtils tool package to get and set properties of JavaBean.

Need to import BeanUtils .jar package. 

Advantages of using BeanUtils:

- You can set type of all parameters as String.
- You are able to access properties of Java Bean in a cascade way. 

If the type of property is not correct when using BeanUtils to transfer, you can use PropertyUtils.

```java
public static void main(String[] args) {
	// continue with the above example 
    System.out.println(BeanUtils.getProperty(pt1, "x").getClass().getName());  // output should be java.lang.String 
    // when using BeanUtils, type of parameters is regarded as String
    
    BeanUtils.setProperty(pt1, "x", "9");  // note: type of "9" is String
    System.out.println(pt1.getX());  // output should be 9
    
    
    BeanUtils.setProperty(pt1, "birthday.time", "111");
    System.out.println(BeanUtils.getProperty(pt1, "birthday.time"));  // output should be 111
    
    
    // when using PropertyUtils, type of parameters is regarded as its original type
    PropertyUtils.setProperty(pt1, "x", 10);  // note: type of 10 is not String 
    System.out.println(PropertyUtils.getProperty(pt1, "x").getClass().getName());  // output should be java.lang.Integer    
}

```

### Annotation 

introduced in JDK 1.5 

Using an annotation is essentially invoking a class.

![apply-annotation-structure.png](F:/ITProjects/learning-notes/java-advanced-note/img/apply-annotation-structure.png)

```java
@AnnotationDemo(color = "red", arrayAttr= {4,5}, annotationAttr=@MetaAnnotationDemo("I am the new value attr."))
public class AnnotationDemoApp {

	public static void main(String[] args) {
		if (AnnotationDemoApp.class.isAnnotationPresent(AnnotationDemo.class)) {
			AnnotationDemo AnnoDemo = (AnnotationDemo)AnnotationDemoApp.class.getAnnotation(AnnotationDemo.class);
			System.out.println(AnnoDemo);  // output should be @DemoAnnotation()
			System.out.println(AnnoDemo.color());  // output should be "red"
			System.out.println(AnnoDemo.value());  // output should be "aaa"
			System.out.println(AnnoDemo.arrayAttr().length);  // output should be 2
			System.out.println(AnnoDemo.annotationAttr().value());  // output should be "I am the new value attr."
		}
	}
}


import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Retention(RetentionPolicy.RUNTIME)
@Target({ElementType.METHOD, ElementType.TYPE})
public @interface AnnotationDemo {
	String color();  // attribute of annotation, then you are able to set attribute when applying the annotation
	String value() default "aaa";
	int[] arrayAttr() default {1,2,3};
	MetaAnnotationDemo annotationAttr() default @MetaAnnotationDemo("I am the value attr of MetaAnnotationDemo."); 
}


public @interface MetaAnnotationDemo {
	String value();
}
```

#### Build-in Annotations

@SuppressWarnings("deprecation") 

- Write out of functions.

- Use it when you are using deprecated methods but you do not want to be warned about it.

@Deprecated 

- Write out of functions.

- Use it when you do not want others to use the method anymore but you do not want to influence the previous program.

#### Meta Annotation 

@Retention() - determine retention of the annotation. 

- @Retention(RetentionPolicy.RUNTIME)
- @Retention(RetentionPolicy.SOURCE)
- @Retention(RetentionPolicy.CLASS)

@Target() - determine where the annotation can be applied.

- @Target(ElementType.METHOD) - can only be used for methods.
- @Target(ElementType.TYPE) - can only be used for types (classes, interfaces, enums).

### Generic

