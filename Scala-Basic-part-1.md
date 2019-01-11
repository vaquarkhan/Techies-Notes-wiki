- val is not reassignable.
- var is reassiganable (rarley used)
- lazy val is lazyly update value example 

           lazy val a={println("evaluated");5}
           evaluated
           a: Int = <lazy>
           
           a
           a: Int = <lazy>
           res1: Int = 5

- lazy val will not evaluated until referenced 
- Any subsequent call to the val will return the same value when initially called upon
- There is no such initially called upon 
- lazy val can  be forgivingif an excetion happends 


Valid OPCHAR : Unicode Character from \u0020-\u007F

http://unicode.org/charats/PDF/U000.pdf


---------------------------------------------------

- If Else If

Example 1:

      val num =10;
      val reult ="";

      if(num<10){
        result ="less then 10";
       }else if(num >10){
        result ="greter then 10";
       }else{
        result ="is 10";
       }


Example 2:

      val num =10;
      val reult ="";

      if(num<10)  result ="less then 10";
      else if(num >10)  result ="greter then 10";
      else result ="is 10";
      

Example 3:

      val num =10;
      val reult =   if(num<10)  "less then 10";
      else if(num >10)  "greter then 10";
      else "is 10";

--------------------------------------------------------------------
- While 

     var a=100;
     var result ="";

     while(a > 0){
     result =result +a;
     if(a < 0) result =result +",";
     a=a-1;
     }

    print(result)

or 


        val result =( 1 to 100).reverse.mkString(",")
       println(result )

or 

       println(( 100 to 1 by -1 ).reverse.mkString(","))
      

-------------------------------------------------

- Do while 


       var a=100
       var result=""
       do{
        result =result +a
        if(a > 1)   result = result +","
        a=a-1
          }while (a >0)
         println(result)


Note : var a = 0 then only execute at least once but while not execute once
Not using in scala as we can achive same using function programming

-------------------------------------------------
- For loop

       var result ="";
       for(a <- 1 to  100){
       result = result +a
       if( a < 1000 result=result +","
       }
       println(result)

or 

    val xs= List(1,2,3,4)
    var result=List[Int]()  //since List is empty we need to add List[Int]() to make it Int List

    result = result :+ (a + 1)
    println(result)

or functional for loop

      val xs = List(1,2,3,4)
      val result =for ( a<- xs) yield( a + 1)
      println(result)

-------------------------------------------------------

String Interpolation

       val a= 99
        print( s" {$a} test String interpolation")


or 

       val a= 99
        print( s" $a test String interpolation")



--------------------------------------------------------

- Method

         def add(x:Int ,y:Int) :int = {
         //body of the method
          return (x+y)
          }

      
      def <method start > (x:int< input param namr>,y:Int) : Int <mehod return type > =<suppose to return something>{
       
       //method body
        return (x+y)
      }


or 

         def add(x:Int ,y:Int) return (x+y) 




![](https://3.bp.blogspot.com/-g5jr3Rdgd_s/WevrAA3vueI/AAAAAAAAADI/usKPAH3ba_ohr3lHZnoYo2sW0O_5oY6wwCLcBGAs/s640/Capture.PNG)


- Types returned from a method are inferred
- Type inferencer will make the best judgment
- If types are diffrent it will find a common ancestor

c
- Unit :  

     val g = ()
     q:Unit=()

Unit will get when nothing to get , as compare to java  "Unit =Void " 


------------------------------------------------------------------------------
