Object − Objects have states and behaviors. An object is an instance of a class. Example − A dog has states - color, name, breed as well as behaviors - wagging, barking, and eating.

* Class − A class can be defined as a template/blueprint that describes the behaviors/states that are related to the class.

* Methods − A method is basically a behavior. A class can contain many methods. It is in methods where the logics are written, data is manipulated and all the actions are executed.

* Fields − Each object has its unique set of instance variables, which are called fields. An object's state is created by the values assigned to these fields.

* Closure − A closure is a function, whose return value depends on the value of one or more variables declared outside this function.

* Traits − A trait encapsulates method and field definitions, which can then be reused by mixing them into classes. Traits are used to define object types by specifying the signature of the supported methods.

* We can execute a Scala program in two modes: one is ****interactive mode and another is script mode****.

****Interactive Mode****

       \>scala
       scala> println("Hello, Scala!");


****Script Mode****

         object HelloWorld {
             /* This is my first java program.  
              * This will print 'Hello World' as the output
              */
              def main(args: Array[String]) {
                println("Hello, world!") // prints Hello World
             }
          }

Save the file as − HelloWorld.scala

      \> scalac HelloWorld.scala
      \> scala HelloWorld


* Case Sensitivity − Scala is case-sensitive, which means identifier Hello and hello would have different meaning in Scala.

* Class Names − For all class names, the first letter should be in Upper Case. If several words are used to form a name of the class, each inner word's first letter should be in Upper Case.

       Example − class MyFirstScalaClass.

* Method Names − All method names should start with a Lower Case letter. If multiple words are used to form the name of the method, then each inner word's first letter should be in Upper Case.

       Example − def myMethodName()

* Program File Name − Name of the program file should exactly match the object name. When saving the file you should save it using the object name (Remember Scala is case-sensitive) and append ‘.scala’ to the end of the name. (If the file name and the object name do not match your program will not compile).

      Example − Assume 'HelloWorld' is the object name. Then the file should be saved as 'HelloWorld.scala'.

* def main(args: Array[String]) − Scala program processing starts from the main() method which is a mandatory part of every Scala Program.

**Alphanumeric Identifiers**
An alphanumeric identifier starts with a letter or an underscore, which can be followed by further letters, digits, or underscores. The '$' character is a reserved keyword in Scala and should not be used in identifiers.

Following are legal alphanumeric identifiers −

               age, salary, _value,  __1_value

* Scala Packages
A package is a named module of code. For example, the Lift utility package is net.liftweb.util. The package declaration is the first non-comment line in the source file as follows −

         package com.liftcode.stuff

Scala packages can be imported so that they can be referenced in the current compilation scope. The following statement imports the contents of the scala.xml package −

        import scala.xml._

You can import a single class and object, for example, HashMap from the scala.collection.mutable package −

        import scala.collection.mutable.HashMap

You can import more than one class or object from a single package, for example, TreeMap and TreeSet from the 
 
       scala.collection.immutable package −

       import scala.collection.immutable.{TreeMap, TreeSet}



***** Variable Scope****
Variables in Scala can have three different scopes depending on the place where they are being used. They can exist as fields, as method parameters and as local variables. Below are the details about each type of scope.

***** Fields****
Fields are variables that belong to an object. The fields are accessible from inside every method in the object. Fields can also be accessible outside the object depending on what access modifiers the field is declared with. Object fields can be both mutable and immutable types and can be defined using either var or val.

***** Method Parameters****
Method parameters are variables, which are used to pass the value inside a method, when the method is called. Method parameters are only accessible from inside the method but the objects passed in may be accessible from the outside, if you have a reference to the object from outside the method. Method parameters are always immutable which are defined by val keyword.

***** Local Variables****
Local variables are variables declared inside a method. Local variables are only accessible from inside the method, but the objects you create may escape the method if you return them from the method. Local variables can be both mutable and immutable types and can be defined using either var or val.





****What is the difference between a var and val definition in Scala?****
mutable and immutable types 
As so many others have said, the object assigned to a val cannot be replaced, and the object assigned to a var can. However, said object can have its internal state modified. For example:

     class A(n: Int) {
       var value = n
     }

     class B(n: Int) {
       val value = new A(n)
     }

         object Test {
            def main(args: Array[String]) {
                val x = new B(5)
                 x = new B(6) // Doesn't work, because I can't replace the object created on the line above with this new one.
                x.value = new A(6) // Doesn't work, because I can't replace the object assigned to B.value for a new one.
               x.value.value = 6 // Works, because A.value can receive a new object.
             }
           }
So, even though we can't change the object assigned to x, we could change the state of that object. At the root of it, however, there was a var.

