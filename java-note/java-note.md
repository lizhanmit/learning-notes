# Java Note

## protected 修饰符

protected修饰的属性或方法表示：在同一个包内或者不同包的子类可以访问。

“不同包中的子类可以访问”，是指当两个类不在同一个包中的时候，继承自父类的子类内部且主调（调用者）为子类的引用时才能访问父类用protected修饰的成员（属性或方法）。

---

## Code Execution Order

Code execution order in class after JVM loading:

1. super static block - execute only once
2. child static block - execute only once
3. super non-static block - when instantiating
4. super constructor
5. child non-static block - when instantiating
6. child constructor

Non-static block is used to organize common code in multiple constructors. 

Java的“类加载”是一个类从被加载到虚拟机内存中开始，到卸载出虚拟机内存为止的整个生命周期中的一个过程，包括加载，验证，准备，解析，初始化五个阶段。而“加载”指的是类加载的第一个阶段。类中的静态块会在整个类加载过程中的初始化阶段执行，而不是在类加载过程中的加载阶段执行。

---

## Interfaces

Interfaces can have properties, but they must be `public static final`.  

### Interface Naming Conventions

- Interface should be a good object name.
- **DO NOT** start with "I", e.g. `IList`.
