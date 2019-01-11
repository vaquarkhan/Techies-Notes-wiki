* Why does ArrayList class implements List even if it's paren class AbstractList already implements List?

When using the instanceof operator, the JVM will first check the current class and then its declared interfaces, and then move to its subclass and repeat the checks, all the way up to Object. There are more checks for if (o instanceof List) in various core classes than most would think. By declaring the ArrayList as explicitly implementing List, the developers sought to gain a few cycles of performance relatively cheaply.


**HashMap and hashset**

* http://www.java-success.com/hashmap-and-how-it-works/