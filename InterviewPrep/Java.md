# Java Foundation Q & A

#### 1. Private Constructor

Q: Interms of **inheritance**, what is the effect of keeping a constructor private?

A: Declaring a constructor *private* on class *A* means that you can only access the private constructor if you could also access *A*'s private methods. Those who can access would be *A*'s inner classes. Additionally, if *A* is an inner class of *B*, the *B*'s other inner classes can. For inheritance, since a subclass calls its parent's constructor. **The class *A* can be iherited, but only by its parent's inner classes**.



#### 2. Return from Finally

Q: In Java, does the finally block get executed if we insert a return statement inside the try block of a try-catch-finally?

A: **Yes**. The *finally* block gets executed when the *try* block exits. Even when we attempt to exit within the *try* block (via a *return* statement, a *continue* statement, a *break* statement or any exception), the *finally* block will still be executed.

*Note that there are some cases in which the *finally* block will not get executed, such as the following:

- If the Java virtual machine (JVM) exits during <u>try/catch</u> block execution.
- If the thread which is executing during the <u>try/catch</u> block gets killed.



#### 3. Final, Finally, Finalize()

Q: What is the difference between these three key words?

A: In general, *final* is used to control whether a variable, method, or class is "changeable." The *finally* keyword is used in a <u>try/catch</u> block to ensure that a segment of code is always executed. The *finalize()* method is called by the garbage collector once it determined that no more references exist.

#### 4. Object Reflection

Q: Explain what object reflection is in Java and why it is useful.

A: Object Reflection is a feature in Java that provides a way to get reflective information about Java classes and objects, and perform operations such as:

- Getting information about the methods and fields present inside the class at run time.
- Creating a new instance of a class.
- Getting and setting the object fields directly by getting field reference, regardless of what the access modifier is.

**Code Example**

```java
/* Parameters */
Object[] doubleArgs = new Object[] {4.2, 3.9};

/* Get class */
Class rectangleDefinition = Class.forName("MyProj.Rectangle");

/* Equivalent: Rectangle rectangle = new Rectangle(4.2, 3.9); */
Class[] doubleArgsClass = new Class[] {double.class, double.class};
Constructor doubleArgsConstructor = rectangleDefinition.getConstructor(doubleArgsClass);
Rectangle rectangle = (Rectangle) doubleArgsConstructor.newInstance(doubleArgs);

/* Equivalent: Double area = rectange.area(); */
Method m = rectangleDefinition.getDeclaredMethod("area");
Double area = (Double) m.invoke(rectangle);
```

