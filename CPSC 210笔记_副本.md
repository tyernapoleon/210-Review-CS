# CPSC 210



## 小撇步

cmd+B: 从method call找method definition

cmd+[: 回到上一步

cmd+]: 撤销回到上一步

cmd+/: 批量注释

cmd+光标放到method名: 查看method来源与return type

cmd+N: Generate Getter and Setter

**批量改名：**选中某名称-》菜单栏Refactor-》Rename:

**并列查看**：Window->Editor Tabs->Split



**查看code coverage:** Run->Edit Configurations

**创建Superclass:** 选择一个Class->Refactor->Superclass->改名并选择Superclass中要有的methods(红色的必须要加入)

**快速创建结构:** 选中一部分代码->Code->Surround With，可以选择要包在这部分code外面的结构

# B1 Program Structure

## Program Structure

**Package**：小文件夹

**Class**：小文件夹中的文件，有的可以运行（有播放标志），有的不能。

运行整个Program：一般从 UI/Main/Usage Package 中寻找可以运行的Class

### 阅读程序的结构

起始点：从可以运行整个Program的Class开始读起

看两个Package的联系：看Import了哪些该Package外的Class（Import是在Package之间）

进入该Class：选中Class名，Cmd+B

看两个Class的联系：Comment out (Cmd+/)一个class，如果另一个class变红了，就说明另一个class会使用该class

# B2 Methods, Calls, and Call Graphs

### 阅读程序的行为

1. Class包含一个单独的概念所有的行为和数据

2. Method Call:

   1. 同Class `someMethod (parameter)`
   2. 不同Class之间 `name.someMethod (parameter)`

3. 按住Cmd把光标移到method名上可以看该method所属class和返回值类型

4. 从Method Call到Method Definition: cmd+B

5. 返回上一个选择: cmd+[，重做上一个选择: cmd+]

6. Method Declaration包含name, parameter list, 大括号之间的method body

   ```java
   public DrawingPlayer(Drawing drawing, Timer timer){
           this.drawing = drawing;
           this.timer = timer;
           playingColumn = 0;
           lastColumnPlayed = new ArrayList<Shape>();
           shapesInColumn = new ArrayList<Shape>();
       }
   ```

7. 有main Method的Class才能执行

### Call Graph

从main method开始，画一个图表示method call的结构（Java library自带的method不用画进去）![call-graph-generate-code](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/call-graph-generate-code.png)

# B3 Classes, Objects and Variables

### Constructor

Constructor可以创建一个class的object或instance，这个object会把所有在class里定义的data和operations复制过来

比如：

Class: Dog包含Data: age和Operations: bark, wagTail

使用Constructor语句`new Dog()`创建一个

Object: DogInstance包含Data: age和Operations: bark, wagTail



Constructor的位置通常就在declaration of constants and fields后面

Constructor的名字和Class相同

```java
class JDayChooser {

   Calendar calendar;
   // declaration of constants and fields
  
   JDayChooser() {
      calendar = new Calendar();
      init();
   }
   // Constructor

```

```java
private int x;
// fields

public Tank(int x){
  this.x = x;
  // 该fields中的x = new Tank(100)传过来的100
  direction = 1;
}
// constructor

Tank myTank;
myTank = new Tank(100);  // 100对应前面constructor里的的int x
```



### Variable

```java
int x;  // declaration
x = 3;  // assignment
int x = 3;  // declaration and assignment

Dog d;  // declaration: the Dog object pointed to by d
				// Dog: Object, d: Object reference
d = new Dog();  // assignment
d.bark();  // method call
d.name = "Jerry";  // assignment

Dog joy;
joy = d;
joy.name = "Joy";
```

### List

```java
ArrayList<Dog> dogs = new ArrayList<Dog>();
// 创建一个叫dogs的ArrayList，其中每个元素都是Dog Class的object
// the ArrayList object pointed to by dogs
  
Dog dog1 = new Dog("dog1");
Dog dog2 = new Dog("dog2");
dogs.add(dog1); // 把dog1放到dogs第一个位置
dogs.add(dog2); // 把dog2放到dogs第二个位置
dogs.get(0);  // return dogs的第一个位置
dogs.get(1);  // return dogs的第二个位置
```

### Class

```java
package model;


import sound.MidiSynth;

import java.awt.*;



public class Shape {

// constants: 关键词final代表immutable
//            关键词static代表它留在该class而不是被object实体化（实体化的意思是提供一个instance。比如 = new List<>()）
    private static final Color PLAYING_COLOR = new Color(60, 230, 224);

// fields: variables or constants declarations
    private int x;
    private int y;
    private int width;
    private int height;
    private boolean selected;
  
    private MidiSynth midiSynth;  
  	// 不是java自带的types，所以开头要Import sound.MidiSynth
  
    private int instrument;
    private int playLineCoord;

// 以下都是methods
    // 这是constructor，也就是在使用new的时候，会call这里
    // 比如Shape rectangle = new Shape();
    public Shape(Point topLeft, MidiSynth midiSynth) {
        this((int) topLeft.getX(), (int) topLeft.getY(), 0, 0);  // note to students: calls the other constructor!
        selected = false;
        this.midiSynth = midiSynth;
        instrument = 0;
        playLineCoord = 0;
    }
}
```

# B4 Data Flow

### Debugger：

1. 设置Breakpoint（开始往下debug)，一般可以设置在main method后面一行
2. Step into进入使用call to a method或者constructor的method位置，而Step over到下一行。（Java library自带的code无法step into）
3. Variables栏会出现该步的信息，小箭头点进去可以看到该object对应的field的值
   1. 需要和用户交互的步骤，会显示is running，这个时候选择Console Tab去进行交互才能进入下一步
4. 工具栏中的Evaluate Expression可以查看
   1. 该步scope（大括号）内某method的result，例如输入jean.getAge()
   2. 该步scope（大括号）内某variable的值，例如说如ageAmount

### Data Flow：Passing

注意区别：

```java
Dog d = new Dog();  // d指向Dog Object
Dog fido = d;  // fido也指向Dog Object

fido.name = "Fido";  // 把Dog Object的name变成Fido
d.name = "Jim";  // 把Dog Object的name从Fido变成Jim
```

```java
Dog d = new Dog();  // d指向Dog Object1
Dog fido = d;  // fido也指向Dog Object1

fido = new Dog();  //fido从Dog Object1指向Dog Object2

fido.name = "Fido";  // 把Dog Object1的name变成Fido
d.name = "Jim";  // 把Dog Object2的name变成Jim
```



注意区别

```java
public void whatever() {
    int x = 3;  // int is a primitive
    notMuch(x); // x's value (3) is copied into num
    int y = x;  // y gets x's value from line 1 (3)
}

public void notMuch(int num) {
    num = 5;    // num changes to 5;
                // num goes away because it's out of scope
}
```

```java
// Version 1
public void whatever() {
    Dog d;
    d = new Dog();      // d is an object reference to a Dog object
    d.name = "elisa";   // d's object's name is set to "elisa"
    bitMore(d);         // we copy d's reference into myDog
    print(d.name);      // d's name has now changed to "norm"
}

public void bitMore(Dog myDog) {    // d's reference is copied into myDog
    myDog.name = "norm";            // d's and myDog's object's name is set to "norm"
                                    // myDog goes away because it's out of scope
}

// Version 2
public void whatever() {
    Dog d;
    d = new Dog();      
  // d is an object reference to a Dog object
    d.name = "elisa";   
  // d's object's name is set to "elisa"
    bitMore(d);         // we copy d's reference into myDog
    print(d.name);      // d's name is still "Elisa"
}

public void bitMore(Dog myDog) {   
  // d's reference is copied into myDog
    myDog = new Dog();              
  // myDog's reference is overwritten equally
    myDog.name = "norm";            
  // myDog's name is changed, d's name is not changed
                                    
  // myDog goes away because it's out of scope
}
```



```java
public void whatever() {
    ArrayList<Integer> nums = new ArrayList<Integer>();
    useList(nums);  
  // nums reference is copied into myList
    nums.add(6);    
  // nums gets an additional Integer (6) in its list,
                    
  // so now the ArrayList looks like [ 5 | 6 | |... | ]
}
public void useList(ArrayList<Integer> myList) { 
  // nums' reference is copied into myList
    myList.add(5);          
  // 5 is added into myList also referenced by nums
                            
  // so the ArrayList looks like [5 | | ... | ]
                   
  // myList goes away because it's out of scope, but nums lives on
}
```



### Data Flow: Returning

```java
public void whatever() {
    int num;
    num = addOne(3);        
  // addOne returns the value 4, which is assigned to num
}

public int addOne(int x) {    // x gets the value 3
    return x + 1;             // addOne returns 4
}
```

`public void`开头的method没有return value

`public int`开头的method结束后会return一个int

```java
public void whatever() {
    Dog d;          
  // d is declared, but not initialized
    d = getDog();   
  // getDog returns a reference to a Dog object
                    
  // That reference is assigned to d
}

public Dog getDog() {
    Dog myDog = new Dog();  
  // myDog is assigned the reference to a new Dog
    return myDog;           
  // myDog's object reference is returned
}
```



### Data Flow: Constructor

```java
public void whatever() {
    Dog d = new Dog(7);
}

public Dog(int age) {
    this.age = age;  // this.age表示在Object的field
  									 // age表示int age，也就是上面下来的7
    // "this" is used to distinguish between
}   // the field "age" and the parameter "age"
```



### Designing Operations on the Data

```java
  // Represents a rectangle having a width and height
  class Rectangle {
    int width;
    int height;

      // EFFECTS: constructs a rectangle of width w and height h
      Rectangle(int w, int h) {
        width = w;
        height = h
      }

      // EFFECTS: returns the area of this rectangle
      int area() {
        // consume no parameters and produce an int
          return width * height; 
      }

      // EFFECTS: returns true if this rectangle is tall; false otherwise
      boolean isTall() {
          return height > width;
      }
  }
```

### Producing examples

```java
// Unit tests for the rectangle class
  class RectangleTest {
    // 开始建立3个examples
      Rectangle r1;
      Rectangle r2;
      Rectangle r3;

      @BeforeEach
      void runBefore() {
        // 建立完3个examples
          r1 = new Rectangle(10, 15);
          r2 = new Rectangle(5, 2);
          r3 = new Rectangle(10, 10);
      }

      @Test
      void testArea() {
        // 这里assertEquals等于(check-expect xxx 某值)
          assertEquals(10 * 15, r1.area());
          assertEquals(5 * 2, r2.area());
      }

      @Test
      void testIsTall() {
        // 这里assertTrue等于(check-expect xxx true)
          assertTrue(r1.isTall());
          assertFalse(r2.isTall());
          assertFalse(r3.isTall());
      }
  }
```

# B5 Conditions, Loops, Execution Flow and Flowcharts

## if, else, else if

```java
public void printNum(int i) {
    if (i == 3) {
        print("three");
    } else if (i == 4) {
        print("four");
    } else {
        print("not 3 or 4!");
        print(i);
    }
    print("done");
}	
```

## switch

```java
switch (expression) { 
  case A: 
    // blockA 
    break; 
  case B: 
    // blockB 
    break; 
  default:
    // default 
}
```

## while

```java
while (boolean) {  // 如果是true则循环
  // body of loop 
}
```

## do-while

```java
do {
  // body of loop 
} while (boolean);
```

## for

```java
for (Type next : collection) { // 比如：list
  // body of loop - 
  // typically do something with next item in collection 
}
```

```java
for (init; boolean; post) {
  // body of loop 
}
```

# A1: Specifying and Using a Data Abstraction

**Specification**

REQUIRES: used to specify a condition that must be true when the call to the method is made, if the method is to provide the behaviour specified in the EFFECTS clause

MODIFIES: used to specify what objects (if any) are modified as a result of the call to the method, only in cases where that modification is maintained after the call to the method ends

- If a method mutates its own object, we indicate that it `// MODIFIES: this`
- If **A.a()** calls **B.b()**, and **B.b()** specifies that it modifies **this**, then **A.a()** would specify that it modifies **B**. 
- **Constructors** omit MODIFIES

EFFECTS: used to specify what will happen when this method is called (not how it will happen)

# A2: Testing a Data Abstraction

```java
// Unit tests for the rectangle class
class RectangleTest {
  // 开始建立3个examples
  Rectangle r1;
  Rectangle r2;
  Rectangle r3;

  @BeforeEach
  void runBefore() {
    // 建立完3个examples
    r1 = new Rectangle(10, 15);
    r2 = new Rectangle(5, 2);
    r3 = new Rectangle(10, 10);
  }

  @Test
  void testArea() {
    // 这里assertEquals等于(check-expect xxx 某值)
    assertEquals(10 * 15, r1.area());
    assertEquals(5 * 2, r2.area());
  }

  @Test
  void testIsTall() {
    // 这里assertTrue等于(check-expect xxx true)
    assertTrue(r1.isTall());
    assertFalse(r2.isTall());
    assertFalse(r3.isTall());
  }
}
```

1. Test只能使用满足REQUIRES的条件
2. 考虑边界情况，以及特别的情况（比如中间）
3. Test所有EFFECTS中的情况
4. 对于MODIFIES中会变化的情况（比如坦克每次call都移动），要Test多几次

# A3: Implementing a Data Abstraction

Q: 请问以下表达式有几个branch？

```java
if (boolA && boolB) {
  // do if true
} else {
  // do if false
}
```

A: 三个：

1. boolA = T, boolB = F, 结果为T
2. boolA = T, boolB = F, 结果为F
3. boolA = F, 不管boolB是什么结果都为F



**public 和 private的区别：**

public定义的methods和variables可以在所有Class中使用

private定义的只能在当前Class中使用，所以可以在当前Class更改不用担心别的地方出错



**Sets和Lists的区别：**

**Sets** do not allow duplicate elements but elements are not stored in insertion order, while **Lists** preserve insertion order but do allow duplicates.

# A4: Types and Interfaces

## Java interfaces

定义：只包含Specification不包含Implementation

**作用：**record similarity between different types（比如ArrayList和LinkedList）

**例子：**

ArrayList and LinkedList implement the List interface

ArrayList and LinkedList are subtypes of List

List is a super type of ArrayList and LinkedList

**特点：**

ArrayList和LinkedList必须implement List Interface所有的methods

ArrayList和LinkedList可以implement List Interface没有的methods



Java Interface**不能被instantiated**

```java
List<Integer> myList = new List<>();
        可以↑              不行↑
```

但是**可以使用Substitution**

```java
List<Integer> myList = new ArrayList<>();
   Apparent Type↑         Actual Type↑
```

## Apprent Type和Actual Type

```java
List<Integer> myOtherList = new LinkedList<>();
```

对这个myOtherList来说，只能call List interface的methods，不能call LinkedList中的List interface以外的methods。



**规则：**

当Assign value的时候，等号右边的Apparent type得是等号左边的Apparent type的subtype才可以compile

```java
List<Integer> data = new ArrayList<>();
//因为ArrayList是List的subtype所以可以compile
ArrayList<Integer> myList = new ArrayList<>();
//因为ArrayList是自己的subtype所以可以compile
```

```java
    data   =   myList
//  List      ArrayList    可以compile
  
   myList   =   data
//ArrayList     List       不可以compile
```

```java
void addAll(ArrayList<Integer> list) {
  //stub
}

.addAll(data);
//不可以compile因为是把data(List)assign给ArrayList

.addAll(myList);
//可以compile因为是把myList(ArrayList)assign给ArrayList
```

```java
public class Collection {
	List<Integer> makeList() {
//↑return的是List(Apparent Type)
    return new ArrayList<>();  //stub
            //↑这个是Actual Type
  }
  
  data = myCollection.makeList();
//List            List         可以compile
  myList = myCollection.makeList();
//ArrayList       List         不可以compile
}
```

## Type Hierarchy

![A4-LearnerStudentTypeHierarchy](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/Lectures/A4/A4-LearnerStudentTypeHierarchy.png)

# A5: Multiple Interfaces

一个Class可以有两个supertype

假设PackageNode同时属于ParentNode和ChildNode，以下三个都能compile：

```java
PackageNode node = new PackageNode();
ParentNode parent = node;
ChildNode child = node;

// node -> 可以使用Both ParentNode和ChildNode里的methods
// parent -> 只能使用ParentNode的methods
// child -> 只能使用ChildNode的methods
```

**例子1：**

Learner Interface: `learn()`    

HasCourse Interface: `getCourses(), register()`

Student(subtype of above): `learn(), getCourses(), register()`

Q：以下哪些可以compile?

```java
//a)
Learner student = new Student();  
// student's apparent type是Learner
student.getCourses();
// getCourses()不是Learner的，不行

//b)
Student student = new Student();
// student's apparent type是Student
student.getCourses();
// getCourses()是Student的，行

//c)
HasCourse somebodyWithCourses = new Student();
// apparent type是HasCourse
somebodyWithCourses.getCourses();
// getCourses()是HasCourses的，行
```

**例子2：**

Learner Interface: `learn()`    

HasCourse Interface: `getCourses(), register()`

Student(subtype of above): `learn(), getCourses(), register()`

```java
public class University {
  public void teach(Learner learner) {
    // ...
  }
  public void register(HasCourses somebodyWithCourses) {
    // ...
  }
}
```

Q：以下哪些可以compile?

a) 可以
`University uni = new University();`
`Learner learner = new Student();`
`uni.teach(learner);`

b) 不可以因为somebodyWithCourses是HasCourses，不是Learner的subtype
`University uni = new University();`
`HasCourses somebodyWithCourses = new Student();`
`uni.teach(somebodyWithCourses);`

c) 可以
`University uni = new University();`
`Student student = new Student();`
`uni.teach(student);`

d) 不可以因为learner是Learner，不是HasCourses的subtype
`University uni = new University();`
`Learner learner  = new Student();`
`uni.register(learner);`

e) 可以
`University uni = new University();`
`Student student = new Student();`
`uni.register(student);`

f) 可以
`University uni = new University();`
`HasCourses somebodyWithCourses = new Student();`
`uni.register(somebodyWithCourses);`

# A6: Extends and Overriding

## Extends

When ClassB extends ClassA:

- ClassB becomes a subtype of ClassA
- ClassA becomes a super type of ClassB 

比如：AlarmClock extends Clock 用AlarmClock继承Clock的所有methods，可以在此基础上增加它自己的。

```java
public class AlarmClock extends Clock {
	private int alarmHrs;
	private int alarmMins;
	private boolean alarmOn;
	private boolean isAlarmSounding;
  // 这些fields都是Clock里没有的
}
```

## Overridden method:

*Override* the tick method: 在AlarmClock中把Clock中已经有的tick method重写（可能增加了功能或者减少了功能，或者完全不同）

```java
Clock myClock = new AlarmClock();
myClock.tick();  //对于这一行:

// does it compile?
// 看Apparent type: Clock有没有define tick()

// what will happen when it runs?
// implement AlarmClock 中的tick(), 如果AlarmClock中没有, 就往上一层: Clock中找，再没有就再上一层，直到出现。

```

## The *protected* keyword:

The protected keyword specifies that a class field or method is visible to 

- all code in the same package as the class that contains that field or method

- all subclasses of the class that contains that field or method

  Apparent type中把private改成protected，Actual type中就能override 这两个methods：

  ```java
  protected int x;
  protected int y;
  protected int width;
  protected int height;
  
  //EFFECTS: draws the shape
  protected void drawGraphics(Graphics g) {
      g.drawRect(x, y, width, height);
      }
  
  //EFFECTS: fills the shape
  protected void fillGraphics(Graphics g) {
      g.fillRect(x, y, width, height);
      }
  ```

不能override一个private的method，需要是protected的才可以

## The *super* keyword:

The super keyword is used in two contexts

1. As a call to the constructor of the immediate superclass (必须是subclass constructor中第一条执行语句)

   ```java
   public AlarmClock() {
   	super();
     alarmHrs = 0;
     alarmMins = 0;
     // etc
   }
   ```

2. As a way to access a memeber of a superclass

   ```java
   @Override  // this tag is optional
   public void tick() {
   	super.tick(); // call tick method on 
           	      // superclass
     // etc
   }
   ```

# A7: Abstract Classes & Overloading Methods

## Abstract class:

- defines a new type of data (像interface和class一样
- can optionally not provide an implementation for  one or more of its methods (作为abstract methods)
- cannot be instantiated (just like an interface)

```java
public abstract class Shape {
  
  //EFFECTS: draws the shape
  protected abstract void drawGraphics(Graphics g);
  
  //EFFECTS: fills the shape
  protected abstract void fillGraphics(Graphics g);
}
```

A class that extends an abstract class:

- must provide an implementation for all of the abstract methods in that abstract class

  OR

- must itself be declared abstract

abstract type可以是not abstract的subtype

## Method overloading

2 methods with the same name but different parameter lists

```java
public boolean contains(Point point);

public boolean contains(int x, int y);
```

## Abstract Class和Interface的区别：

Abstract Class: 

1. methods可以有也可以没有implementation
2. 当一个class extends了Abstract Class，它就不能有别的parent了

Interface: 

1. methods不能有implementation
2. 当一个class object implement一个Interface，这个class还可以有别的abstract class或者superclass作为parent

## Abstract Class和Superclass的区别：

Abstract Class: 

1. 不能被Instantiate
2. 所有的abstract methods（不包含implementation）都要被implement

Superclass:

1. 可以被Instantiate
2. 不能包含abstract methods，并且可以有methods（包含implementation）被Override也可以不被Override

# C1: Throwing and Catching Exception

## Robust methods:

1. Specifies a behaviour for every possible input
2. a method with a requires clause that excludes certain values from input is not robust
3. 用中文讲，就是无论input什么，都有一个对应的behaviour，所以有REQUIRES的就是不robust的

## Exceptions

1. Java自带一个Exception class
2. exception是Exception class的一个object或者一个subclass
3. An exception can be thrown by a method when it encounters a problem that it wants the caller to handle. The caller handles the exception by:
   - catching the exception and running code to address the problem, or
   - propagating the exception back to its caller
4. 包含checked和unchecked exceptions（这章不讲）

## 如何使Methods robust:

1. 找哪个method有REQUIRES
2. 在EFFECTS中添加：REQUIRES以外的情况下要throw哪个exception（不包含type不正确的情况，因为Java compiler会帮我们）
3. 去掉REQUIRES

```java
/*
 * MODIFIES: this
 * EFFECTS: if amount < 0 throws NegativeInputException
 
 *          else if amount > getBalance() throws InsufficientFundsException
 
 *          otherwise amount is withdrawn from account and updated balance is returned
 */
```

4. 创建一个package叫exception，在exception里创建Class：NegativeInputException和InsufficientFundsException，让其extends Exception（作为Exception Class的subclass）

```java
public class NegativeInputException extends Exception {
    public NegativeInputException() {
        super();
    }
}
```

5. 在method中写明在REQUIRES以外的情况要throw什么exception

```java
public double withdraw(double amount) {
	if (amount < 0) {
    throw new NegativeInputException();
  }
  if (amount > balance) {
    throw new InsufficientFundsException();
  }
  balance = balance - amount;
  return balance;
}
```

6. 两个throw不能compile：如果想要在这个method的caller中解决这个问题，则在红色小灯泡中选Add exception to method signature。如果想要在本method中解决，就选Surround with try/catch。（本例中，withdraw的input是要取的钱，是由它的caller传入的。所以我们要在caller里解决这个钱的数目出错的问题）

```java
public double withdraw(double amount) throws NegativeInputException, InsufficientFundsException {
  if (amount < 0) {
    throw new NegativeInputException();
  }
  if (amount > balance) {
    throw new InsufficientFundsException();
  }
  balance = balance - amount;
  return balance;
}
```

7. 进入Caller，选择Surround with try/catch，在catch后面一行输入想要执行的步骤，比如System.out.println

```java
private void doWithdrawal() {
  Account selected = selectAccount();
  System.out.print("Enter amount to withdraw: $");
  double amount = input.nextDouble();

  try {
    selected.withdraw(amount);
  } catch (NegativeInputException e) {
    System.out.println("Sorry, negative amount");
  } catch (InsufficientFundsException e) {
    System.out.println("Sorry, Insufficient amount");
  }

  printBalance(selected);
}
```

注意：

```java
public class ClassB {
  public void methodB() throws RainException { 
    ClassA myA = new ClassA();

    try {
      System.out.println("1");  // 不管怎样都会执行
      myA.methodA();
      System.out.println("2");  // A里没问题才会执行，A里有问题直接跳到catch，这条不执行
    } catch (WindException e) {
      System.out.println("3");  // A里有WindException的问题时执行，有别的问题的时候不执行
    } finally {
      System.out.println("4");  // 不管怎样都会执行
    }
    System.out.println("5");  //只有这里的catch执行后才会从这里跳出try/catch结构，执行这条语句。换句话说，哪里的catch执行了，就从哪里跳出try/catch结构
  }
}
```



# C2: Exception Hierarchy / Unchecked exceptions / Assertions

## Exception Hierarchy

以下的代码存在问题：如果要用withdraw method取出一个账户内的amount，再用deposit method存入另一个账户，比如从0元的账户取出100万，会报Sorry Insufficient，然后继续执行deposit，最后会把100万存入第二个账户。

```java
try {
  source.withdraw(amount);
} catch (NegativeInputException e) {
  System.out.println("Sorry, negative amount");
} catch (InsufficientFundsException e) {
  System.out.println("Sorry, Insufficient amount");  // 执行这条
}

try {
  destination.deposit(amount);  // 然后执行了这条
} catch (NegativeInputException e) {
  System.out.println("Cannot transfer negative amount...\n");
}
```

应该这么写：

```java
try {
  source.withdraw(amount);  // 这里出了问题
  destination.deposit(amount);  // 跳过这条
} catch (NegativeInputException e) {
  System.out.println("Sorry, negative amount");
} catch (InsufficientFundsException e) {
  System.out.println("Sorry, Insufficient amount");  // 执行这条
}
```

如果想要让两个catch输出相同的东西：

```java
try {
  source.withdraw(amount);
  destination.deposit(amount);
} catch (BankingException e) {
  System.out.println("Sorry, transaction could not be completed");
}
// BankingException是NegativeInputException和InsufficientFundsException的Superclass
```

## Class Invariants

- A class invariant is a property that is always true about every instance of that class，比如Account class中，存款always大于0
- Invariants can be checked using Java assert statements

在class的最前面写Invariant：

```java
// Represents an account having an id, owner name and balance (in dollars)
// Invariant: getBalance() >= 0
public class Account implements Saveable {
}
```

**如何测试Invariant：**

1. 在该Class最后加一条method：

```java
private void checkBalanceInvariant() {
  assert (balance >= 0);
}
// 括号里的是Boolean，如果是True就没问题
```

2. 在所有使用到balance的method最后加一条（需要加在return之前，不然run不到）

```java
checkBalanceInvariant();
```

3. 先Run一遍Main
4. 打开Run -> Edit Configuration ->  在VM options中输入 -ea （代表enable assertion）
5. 设置完毕后再Run，如果有问题，Run之后会显示`AssertionError`

**What does it mean for the design of a class to be robust?**

First, **all of the methods in the class must be robust**. A method is robust if its specification covers all possible input values to that method. In addition, we must **specify class invariants** and ensure that they holdbefore any method in the class is called and immediately after any of those methods executes. The invariants specify what an operation can assume about the state of an instance of that class at any time.

## Checked & Unchecked exceptions

- RuntimeException和它的subclasses都是Unchecked exceptions

- 除此之外都是Checked exceptions
- 对于checked exceptions一定要解决或者传给下一个caller，而对于unchecked exceptions我们不用解决

**RuntimeException:**

创建一个Exception class extends RuntimeException，它就变成了一个Unchecked exception。

在method中，出了某问题的情况下我们throw这个unchecked exception，可以选择在caller中catch也可以不catch。如果在caller链中没有一个caller catch了这个exception，那么程序就会终止。

**例子：**

```java
/**
* Represents an exception raised when an illegal index
* is used.
*/
public class IllegalIndexPosition extends RuntimeException {
  /**
    * Constructor
    * @param index  the illegal index
    */
  public IllegalIndexPosition(int index) {
    super("The index " + index + " is not valid.");
  }
}
```

# C3: Testing Exceptions

测试`setColor("blue")`应该throw ColourException

```java
@Test
public void testBadColourExpectColourException() {
  try {
    light.setColour("blue");
    fail("not throw any");  // 没有throw任何Exception
  } catch (ColourException e) {
    // expected
  } catch (SequenceException e) {
    fail("throw wrong");  // throw了SequenceException
  }
}
```

测试`setColor("red")`应该不throw任何Exception

```java
@Test
public void testBadColourExpectColourException() {
  try {
    light.setColour("red");
    // expected
  } catch (ColourException e) {
    fail("throw wrong");  // throw了ColourException
  } catch (SequenceException e) {
    fail("throw wrong");  // throw了SequenceException
  }
}
```

# C4: Extracting Hierarchy and Associations

![extracting-hierarchy](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/extracting-hierarchy.png)

**Extends:** A是B的subclass

**Implements:** A implements interface B

**Association:** 两者有关

- 包含uni-directional和bidirectional association

**Aggregation:** 一个属于另一个 (B is part of A)，比如Tyre属于Car

- we consider B to be the "whole" and A objects to be the "parts"
- B can access ("knows about") services/behaviours provided by the A class but not vice-versa

Association和Aggregation看Code是区分不出的，所以按个人理解来画图。





Class A有1个Class C的field，Class C有1个Class A的field，

Class A有3个Class B的field

![截屏2020-10-29 上午1.03.37](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/截屏2020-10-29 上午1.03.37.png)

## Multiplicity

**0..1:** single object 在它的lifetime中有变成过null，或者Collection（比如List, Set）中有0或1个元素

**0..*:** Collection中有0到任意(*)个元素

**10..100: **Collection中有10到100个元素



## 例子

看AlarmController Class和Alarm Class的关系：

1. 先看AlarmController Class的fields有没有用到Alarm Class? 有

   ```java
   public class AlarmController {
   
   	private Collection<AlarmCode> codes;
   	private Collection<Sensor> sensors;
   	private boolean isArmed;
   	private boolean allSensorsClosed;
   	private Alarm theAlarm;  // 这里
   ```

2. 看Alarm Object是否在它的lifetime中有变成过null?

   1. 看Constructor是否有给Alarm Object初始值为null? 没有
   2. 看Constructor里的update() method有没有改变Alarm Object后续的值为null? 没有
   3. 选中Alarm Object，看后续所有methods里用到它的地方有没有改变它的值为null? 没有

   没有变成null

3. AlarmController————>Alarm，给箭头处标上1(multiplicity)，代表在它的lifetime中一直不是null。（如果有变成过null，则标0..1）

4. *如果有两个从未变成null的Alarm Objects, multiplicity为2（如果有一个可以变成null就是1..2，以此类推）



对于Collection来说，要看它最少会有几个元素，最多会有几个元素：

1. 看Constructor执行完后，它产生几个元素? 一个

   ```java
   public AlarmController() {
     codes = new HashSet<AlarmCode>();  // 空Set
     sensors = new HashSet<Sensor>();
     isArmed = false;
     theAlarm = new Alarm();
     codes.add(new AlarmCode("1111"));  // 有一个元素
     update();
   }
   ```

2. 再看后面的methods对元素的数量会有什么改变（能最多增加到多少，能最少减少到多少）?可以是任意多，最少一个

3. AlarmController————>AlarmCode，箭头处标上1..*（1到任意多）



注意：当我们看一个abstract class和某class的association时，如果abstract class中存在protected的field，那么不仅要在该abstract class下查看它的multiplicity，还要在它所有的subclasses和同package的classes中查看它的multiplicity。

例子：看Tool class和Drawing Editor class的association

```java
public abstract class Tool {

	protected JButton button;
	protected DrawingEditor editor;
	private boolean active;
```

由于editor是protected，就要去Tool class所有的subclasses和同package的classes中查看editor的multiplicity

# C5: Extracting Sequence Diagrams

**Sequence diagram**: models inter-method control flow (like a call graph) in addition to modelling data

- By identifying the objects on which methods are called

**Example**

![截屏2020-11-08 下午5.33.47](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/截屏2020-11-08 下午5.33.47.png)

```java
public void printLog(EventLog el) {
  for (Event next : el) {
    logArea.setText(logArea.getText() + next.toString() + "\n\n");
  }

  repaint();
}
```

**注意点：**

1. 按照括号内至括号外的顺序：getText(), toString(), setText()
2. getText()的执行对象logArea是Java Library中的，所以不再往里看。相反，toString()的执行对象next不在Java Library里，所以需要往里深入。
3. getText()返回的值去它的caller里找，应该是String，这里写错写成了text
4. 返回void箭头上就什么都不写
5. for loop要用框画出来（if也要）
6. repaint()是对this使用的，所以这么画。由于是Java Library中的method，不再往里看
7. Extra: 如果有重复的overloading methods，要在sequence diagram里写出他们的parameter用以区分

# C6: Implementing a Hierarchy, Simple Fields (and optional sequences)

## Maps

- The keys in the collection are unique
- A key is used to look-up the corresponding value

**Map<K, V>**

- K - represents the type for the key
- V - represents the type for the value

**Lists, Sets, Maps的对比**

- List里的items有顺序
- Set里没有顺序，items不能重复
- Map把key和value配对

# C7: Overriding Equals

## equals() & hashCode()

所有的Class都是Object class的subclass

Object class提供这两个methods：

1. `boolean equals(Object other)`
2. `inte hashCode()`

**可以override equals，但是要同时override hashCode**



**.equals()**

`a.equals(b)` is equivalent to a == b



**.hashCode()**

给它一个对象，会返回一个hashcode值

介绍：

https://www.cnblogs.com/dolphin0520/p/3681042.html

如果a equals b，那么他们的hashcode一定一样



**例子：如何override equals和hashCode**

Code -> Generate -> equals() and hashCode() -> default -> 选择判断是否equal的依据（比如根据学号判断改过名字的一个人是否和没改名字之前是同一个人） -> 选择not null的field（不确定可以不选）

# C8: Implementing Bi-Directional Relationships and Sequence Diagrams

Recipe  0..*  ------------------------  0..*  Ingredient

```java
public class Recipe {
  private List<Ingredient> ingredients;
  
  //...
  
  public void addIngredient(Ingredient ingredient) {  //给某recipe加入ingredient
    if (!this.ingredients.contains(ingredient)) {
      ingredients.add(ingredient);
      ingredient.addRecipe(this); //该ingredient也要加入该recipe
    }
  }
  //...
}

public class Ingredient {
  private List<Recipe> recipes;
  
  //...
  
  public void addRecipe(Recipe recipe) {  //给某ingredient加入recipe
    if (!this.recipes.contains(recipe)) {
      recipes.add(recipe);
      recipe.addIngredient(this); //该recipe也要加入该ingredient
    }
  }
  //...
}
```

Recipe  0..1  ------------------------  0..*  Ingredient

```java
public class Recipe {
  public void addIngredient(Ingredient ingredient) {
    if (!this.ingredients.contains(ingredient)) {
      ingredients.add(ingredient);
      ingredient.addRecipe(this);
    }
  }
  //...
}

public class Ingredient {
  public void addRecipe(Recipe recipe) {
    if (this.recipe != recipe) { //这里有所不同
      this.recipe = recipe;  //这里有所不同
      recipe.addIngredient(this);
    }
  }
  //...
}
```

## Example

![截屏2020-11-19 上午10.36.44](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/截屏2020-11-19 上午10.36.44.png)

```java
public class Airport {
  private final String code;

  public Airport(String code) {
    this.code = code;
  }

  public String getCode() {
    return code;
  }

  // Generate these methods using IntelliJ!
  @Override
  public boolean equals(Object o) {
    if (o == null)
      return false;

    if (this.getClass() != o.getClass())
      return false;

    Airport other = (Airport) o;
    return (code.equals(other.code));
  }

  @Override
  public int hashCode() {
    return code.hashCode();
  }
}



public class Route {
  private final Airport departure;
  private final Airport arrival;

  public Route(Airport dep, Airport arr) {
    departure = dep;
    arrival = arr;
  }

  public Airport getDepartureAirport() {
    return departure;
  }

  public Airport getArrivalAirport() {
    return arrival;
  }

  // Generate these methods with IntelliJ!
  @Override
  public boolean equals(Object o) {
    if (o == null)
      return false;

    if (this.getClass() != o.getClass())
      return false;

    Route other = (Route) o;
    return (other.arrival.equals(this.arrival)&& other.departure.equals(this.departure));
  }

  @Override
  public int hashCode() {
    return arrival.hashCode() * 13 + departure.hashCode();
  }
}



public class FlightMap {
  private Set<Route> routes;

  public FlightMap() {
    routes = new HashSet<Route>();
  }

  public void addRoute(Route r) {
    routes.add(r);
  }

  public void removeRoute(Route r) {
    routes.remove(r);
  }
}
```

# D1: Cohesion and Coupling

## Cohesion: 

The Single Responsibility Priciple

- Each class should have one, clearly defined purpose

## Coupling:

- A measure of the degree to which one part of the system depends on other parts of the system. Shows up when a change in one class results in another class not compiling (so such cases show up in an obvious way).
- For example, you have multiple classes that depend on ClassA. Changing the signature of a method in ClassA (e.g., changing the type of a parameter) will result in classes that call that method not compiling.

## Semantic coupling: 

- depends on the specific implementation of another class. A change may cause bug (not easy to identify)
- For example, a method in ClassA returns items in a collection in some order ( when no particular order is specified) and ClassB depends on that order in its implementation. If the order is changed, ClassB may no longer function as specified.

## Example:

![截屏2020-11-18 下午10.13.06](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/截屏2020-11-18 下午10.13.06.png)

**1) You come up with the following design. Is there too much coupling? If so, where?**

Hint: 是否有unnecessary association

Account和CallLog的Association是多余的，因为每个Phone的CallLog和该Phone的Account的CallLog是相同的。

**2) Can you find other problems in this design?**

1. Customer和Phone应该是bidirectional，Phone也应该存储它的Owner
2. Account和Customer应该有Association因为“For each account the company needs to know the customer who is responsible for paying the bill”
3. Account和MonthlyStatement应该有Association因为“Each moth, one statement is sent to the customer contact for each account”

![截屏2020-11-18 下午11.34.47](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/截屏2020-11-18 下午11.34.47.png)

**3) 如何增加一个InternationalCall，使用不同的MonthlyStatement**

![截屏2020-11-19 上午12.06.11](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/截屏2020-11-19 上午12.06.11.png)

# D2: Liskov Substitution Principle

## Definition

In a nutshell: when a subtype is substituted for its super type, the subtype must provide the expected behaviours of the super type.

Any test that is designed based on the specification of a method must pass when run against an overridden version of that method in a subtype

In particular, each overridden method in the subtype:

- must **not narrow** the range of inputs that it accepts (specified in the requires clause / **precondition**)
- must **not widen** the range of outputs that it produces (specified in the effects clause / **postcondition**)

## Example

**Narrowing and Widening Preconditions**

```java
// Superclass

// REQUIRES: input >= 0

// EFFECTS: sets the input
public void setInput(int input) {
  this.input = input;
}

//Subclass

// REQUIRES version1: input >= 100, narrowing the precondition
Narrowing the precondition不行，LSP Violated{}

// REQUIRES version2: input >= -100, widening the prevondition
Widening the precondition行，LSP holds{}

// EFFECTS: sets the input
@Override
public void setInput(int input) {
  super.setInput(input)
}
```

因为Subclass的input应该支持所有Superclass中的requires的范围。上例中由于Superclass中的范围是>=0，在Subclass里我们避免不了使用该范围的input，比如10。但是如果Overridden  method中的input=10，该method就会有bug。

**Widening and Narrowing Postconditions**

```java
// Superclass

// EFFECTS: returns an integer
// in the range [0, 100)

public int produceValue() {
  return input % 100;
}

// Subclass

// EFFECTS version1: returns an integer
// in the range [0, 200)
// 
Widening the postcondition不行，LSP Violated{}

// EFFECTS version2: returns an integer
// in the range [0, 50)
Narrowing the postcondition行，LSP Holds{}

@Override
public int produceValue() {
  return input % 200;
}
```

很简单，因为Overridden method不能return Superclass中相同method范围以外的值。

**如何判断是否widening：**

当我们test superclass中的produceValue method（上例）时，会判断它是否落在0到100区间内。所有在区间外的情况都是widening。

同理，当我们test superclass中的生成包含一个元素的list的method时，会判断生成的list是否有且仅有一个元素。所有此外的情况都算widening。

所以判断是否widening的依据就是，看是否会落在该superclass method test的区间以外。

# D3: Refactoring

## Defintion

improve the design of the code without changing functionality

by

- reducing repetitive code using helper methods
- reducing coupling
- increasing cohesion (single responsibility principle)

## Produce helper methods 

(从一个很长的method中提取一部分出去作为helper methods)

全选你想要的提取作为method的部分 -> Refactor -> Extract -> Method -> 给method取名

## Increase cohesion

(从一个class中提取本该新建一个class存放的method)

选中你想要提取method的class -> Refactor -> Extract -> Delegate ->  给新的class取名 -> 选中你想转移的methods

(注意：新建的class会associate with原来的class，并且原来的class中的methods不会消失，而是直接call了新class中的methods)

# D4: Composite Pattern

## Design Pattern

不是Library也不是类似ArrayList和HashSet

而是对应某个particular design problem

《Design Patterns》: 好书 on Software Development (C++)

《Head First Design Patterns》(Java):  free on UBC Library

## Composite Pattern

Arbitrary-arity tree: composite nodes, leaf nodes

Composite nodes: 有任意数量children（可以是0个）

Leaf nodes: 没有children



![截屏2020-12-14 上午2.12.26](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/截屏2020-12-14 上午2.12.26.png)

1. Component (Superclass): record common behavior of Leaf and Composite

1. Composite是Component的Aggregation

   （Component是Composite的一部分，也就是说Composite包含Component，而Component可以是leaf也可以是Composite）

## BranchesLeavesNoCompositeStarter

1. Identify composite和leaf
2. 然后创建一个abstract class或者interface作为superclass来captures他们的共同点。
3. 当iterate到leaf进行操作

## CompositeFileSystemExample

1. 重复上面的步骤1 2
2. iterate到leaf或composite都要进行操作
3. 不能添加重复名字的component进composite

# D5: Observer Pattern

## Observer Pattern

在一个One-to-many dependency：当一个Object change state，所有的dependents are notified and updated automatically（without introducing coupling）

**the subject (the observable)**: 变化的东西（FavouriteColourData）

**the observers**: 被notified的dependents（PieChar和MaxVoitesPanel）

## 太长不看版本：

![截屏2020-12-14 下午8.51.13](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/截屏2020-12-14 下午8.51.13.png)

## 详细版本：

以下这个图会introduce coupling：**

![截屏2020-12-14 下午6.48.39](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/截屏2020-12-14 下午6.48.39.png)

**因为如果想要增加一个功能，就要改动subject class**

但是PieChart和MaxVotesPanel有共同点，所以要用Interface record这些共同点，这样就**没有coupling**了：

![截屏2020-12-14 下午8.42.25](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/截屏2020-12-14 下午8.42.25.png)

Subject class增加两个method：addObserver和notifyObservers

addObserver是用来把PieChart和MaxVotesPanel放进Observer里

notifyObservers是用来iterate through observers，并且对每个进行update

**现在如果想增加一个功能，只需要把新的Class extends interface就行了**

**把关于Observer的methods提取出来：**

![截屏2020-12-14 下午8.47.50](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/截屏2020-12-14 下午8.47.50.png)

## ZooObserversStarter

以上步骤的例子在这个文件里

Subject: Zoo

Observer: EmailSubscriber, Organization

# D6: Java's Observer

Java Library里自带Observable Class

![截屏2020-12-14 下午10.20.29](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/截屏2020-12-14 下午10.20.29.png)

```java
void notifyObservers();
// calls update on each of the observers with the following arguments:
observer.update(this, null);

void notifyObservers(Object arg);
// calls update on each of the observers with the following arguments:
observer.update(this, arg);
```

## ObservableLecLabStarter

1. 只要Leaf变颜色，就告诉LeafMonitor执行update

2. 所以Subject是Leaf，Observer是LeafMonitor

3. 创建LeafMonitor Class，implements util.Observer

   在该Class创建Override method:

   `void update(Observable o, Object arg)`

   如果执行update中的内容的时候要用到Leaf的field，要这样写：

   ```java
   public void update(Observable o, Object arg) {  
     // 这里的o代表一个Observable Object
     if (o instanceof Leaf) {  // 因为Branch和Leaf都是Observable，这里要防止o是Branch而不是Leaf
       Leaf leaf = (Leaf) o; 
       // 这里需要把o从Observable转换成Leaf赋给leaf
       // 下面才可以对leaf使用getName()和getColour()
       System.out.println(name + " --- " + leaf.getName() + " changed to " + leaf.getColour());
     } else {  // 如果是Branch执行下面的：
       Branch branch = (Branch) o;
       // ...
     }
   ```

4. 让Leaf extends util.Observable（由于Leaf已经extends了Offshoot，所以让Offshoot extends Observable）

   在该Class变颜色的那条method里加上：

   ```java
   setChanged();  // notify之前必须有这条
   notifyObservers();  // Leaf换颜色后就告诉Observers (LeafMonitor)让它update
   ```

5. 在Leaf被Instantialize之后对它使用addObservers()

## SpaceInvadersRefactored

```java
notifyObservers(EVENT_INVADER_HIT);
// 可以给notifyObservers传argument进去，这样在不同情况下就可以传不同的argument

public void update(Observable o, Object arg) {
  if (EVENT_INVADER_HIT.equals(arg)) {
    System.out.println("Ouch! Invader Hit!");
  }
}
//只有在传进去的argument等于EVENT_INVADER_HIT的情况下才执行这条update
```

# D7: Iterator Pattern

## Iterator Pattern

访问一个collection的elements（without exposing its underlying implementation）

```java
// for-each loop其实等同于：
Iterator<Item> itr = collection.iterator();
while (itr.hasNext()) {
  Item item = itr.next();
  // do something with nextItem
}

// the collection must have the method:
// Iterator<E> iterator();

// the iterator must have the methods:
// boolean hasNext();
// E next();
```

![截屏2020-12-15 上午4.08.12](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/截屏2020-12-15 上午4.08.12.png)

1. 所有的List和Set都有它们自己的`iterator() `method，都返回`Iterator<E>`。这个method来自于它们的interface：Iterable
2. 不同类型的List和Set返回的东西不一样，这个由`Iterator<E>` interface决定

## 我们今天要做的：

![截屏2020-12-15 上午4.10.23](/Users/seg/Desktop/UBC BCS/2020 W1/CPSC 210/截屏2020-12-15 上午4.10.23.png)

```java
for (Item next : myCollection) {
  // do something with next
}

// 1.确定想要iterate over什么collection（myCollection），这就是你的Iterable
// 2.确定collection是什么type，这就是Iterable<Item>和Iterator<Item>中的Item的type
```

## BasicIterator-LectureLab - ThingCollection

1. Main里面对ThingCollection Object: things使用了for-each loop，但是没有compile
2. 使ThingCollection Class implements `Iterable<Thing>` 
3. 在该Class Override iterator method，返回一个`Iterator<Thing>`
4. 这下可以对things使用for-each loop了

```java
public void removeThingsThatStartWith(String prefix) {
  for (Thing thing : things) {
    if (thing.getName().startsWith(prefix)) {
      things.remove(thing);
    }
  }
}
// 不能边iterate一个collection，边modify一个collection，因为这样会影响iteration
```

```java
public void removeThingsThatStartWith(String prefix) {
  Iterator<Thing> itr = things.iterator();

  while (itr.hasNext()) {
    Thing thing = itr.next();
    if (thing.getName().startsWith(prefix)) {
      itr.remove();
      // 这里让Iterator来执行remove，这样remove的就是cursor前面一个东西，这样不会影响接下来的iteration
    }
  }
}
```

# D8: Advanced Iterator

## OddsIteratorStarter - OddsIterator

现在Iterable Class中的iterator() method需要返回一个自己创建的Iterator Class

```java
public Iterator<Long> iterator() {
        return new OddsIterator();
    }
```

```java
public class OddsIterator implements Iterator<Long> {

  @Override
  public boolean hasNext() {
    return true;
  }
  // 这里implement如何确定是否有下一个元素

  @Override
  public Long next() {
    return 0L; // TODO: implement me!
  }
  // 这里implement下一个元素的返回值
}
```

## MoreIteratorsStarter - TwoLists

```java
// 两个List先iterate第一个再iterate第二个
private class OneThenTwoIterator implements Iterator<Integer> {
  private Iterator<Integer> oneIt;
  private Iterator<Integer> twoIt;

  OneThenTwoIterator() {
    oneIt = listone.iterator();
    twoIt = listtwo.iterator();
    // 因为listone和listtwo本来就是ArrayList所以可以直接对他们使用.iterator()
  }
  @Override
  public boolean hasNext() {
    return oneIt.hasNext() || twoIt.hasNext();
  }
  // 两个list只要有一个还有元素就没完

  @Override
  public Integer next() {
    return oneIt.hasNext() ? oneIt.next() : twoIt.next();
  }
  // 第一个list有元素就输出第一个list的next，第一个list没有元素了就输出第二个list的next
}
```

```JAVA
// 两个list交替iterate
private class AlternatingIterator implements Iterator<Integer> {
  private Iterator<Integer> oneIt;
  private Iterator<Integer> twoIt;
  private int whichList;  // NEW

  AlternatingIterator() {
    oneIt = listone.iterator();
    twoIt = listtwo.iterator();
    whichList = 1;  // NEW
  }

  @Override
  public boolean hasNext() {
    return oneIt.hasNext() || twoIt.hasNext();
  }
  // SAME

  @Override
  public Integer next() {
    if ((whichList == 1 && oneIt.hasNext()) || !twoIt.hasNext()) {
      // 如果目前是第一个list并且第一个list还有元素，
      // 或者不管目前轮到哪个list只要第二个list没有元素了，
      // 都输出第一个list的next
      whichList = 2;
      return oneIt.next();
    } else {
      whichList = 1;
      return twoIt.next();
    }
  }
  // DIFFERENT
}
```

```java
// 两个List交替相乘：1x3 1x4 1x5 2x3 2x4 2x5
private class CartesianIterator implements Iterator<Integer> {
  private Iterator<Integer> oneIt;
  private Iterator<Integer> twoIt;
  private Integer one;

  CartesianIterator() {
    oneIt = listone.iterator();
    twoIt = listtwo.iterator();
    one = oneIt.hasNext() ? oneIt.next() : null;
  }

  @Override
  public boolean hasNext() {
    return one != null && twoIt.hasNext();
  }

  @Override
  public Integer next() {
    int ans = one * twoIt.next();

    if (!twoIt.hasNext() && oneIt.hasNext()) {
      // 如果第二个list没元素了并且第一个list还有元素
      one = oneIt.next();
      // 第一个list向前进一位
      twoIt = listtwo.iterator();
      // 重置第二个list的iterator

    return ans;
  }
}
```



