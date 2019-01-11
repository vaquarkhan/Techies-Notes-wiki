* Beside implementing the Future interface, CompletableFuture also implements the CompletionStage interface.

* Future represents the pending result of an asynchronous computation. It offers a method — get — that returns the result of the computation when it's done.

* The problem is that a call to get is blocking until the computation is done. This is quite restrictive and can quickly make the asynchronous computation pointless.

* CompletableFuture can be explicitly completed by calling the complete() method without any synchronous wait. It allows values of any type to be available in the future with default return values, even if the computation didn’t complete, using default / intermediate results.

* CompletableFuturealso allows you to build a pipeline data process in a series of actions. You can find a number of patterns for CompletableFutures such as creating a CompletableFuture from a task, or building a CompletableFuture chain. [https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CompletableFuture.html).

****Building asynchronous systems****

When building bigger asynchronous systems, things work a bit differently.

You'll usually want to compose new pieces of code based on smaller pieces of code. Each of these pieces would typically be asynchronous — in our case returning CompletionStages.

Until now, sendMsg has been a normal blocking function. Let's now assume that we got a sendMsgAsync method that returns a CompletionStage.

If we kept using thenApply to compose the example above, we would end up with nested CompletionStages.

             CompletableFuture.supplyAsync(this::findReceiver)  
                 .thenApply(this::sendMsgAsync);

         // Returns type CompletionStage<CompletionStage<String>> 

We don't want that, so instead we can use thenCompose which allows us to give a Function that returns a CompletionStage. This will have a flattening effect like a flatMap.

              CompletableFuture.supplyAsync(this::findReceiver)  
                   .thenCompose(this::sendMsgAsync);

// Returns type CompletionStage<String>
This way we can keep composing new functions without losing the one layered CompletionStage.

Callback as a separate task using the async suffix
Until now all our callbacks have been executed on the same thread as their predecessor.

If you want to, you can submit the callback to the ForkJoinPool.commonPool() independently instead of using the same thread as the predecessor. This is done by using the async suffix version of the methods CompletionStage offers.

Let's say we want to send two messages at one go to the same receiver.

           CompletableFuture<String> receiver  
             = CompletableFuture.supplyAsync(this::findReceiver);

           receiver.thenApply(this::sendMsg);  
           receiver.thenApply(this::sendOtherMsg);  

In the example above, everything will be executed on the same thread. This results in the last message waiting for the first message to complete.

Now consider this code instead.

             CompletableFuture<String> receiver  
            = CompletableFuture.supplyAsync(this::findReceiver);

           receiver.thenApplyAsync(this::sendMsg);  
           receiver.thenApplyAsync(this::sendMsg);  

By using the async suffix, each message is submitted as separate tasks to the ForkJoinPool.commonPool(). This results in both the sendMsg callbacks being executed when the preceding calculation is done.

The key is — the asynchronous version can be convenient when you have several callbacks dependent on the same computation.

**What to do when it all goes wrong**

As you know, bad things can happen. And if you've worked with Future before, you know how bad it could get.

Luckily CompletableFuture has a nice way of handling this, using exceptionally.

                CompletableFuture.supplyAsync(this::failingMsg)  
                 .exceptionally(ex -> new Result(Status.FAILED))
                 .thenAccept(this::notify);

exceptionally gives us a chance to recover by taking an alternative function that will be executed if preceding calculation fails with an exception.

This way succeeding callbacks can continue with the alternative result as input.

If you need more flexibility, check out whenComplete and handle for more ways of handling errors.

Callback depending on multiple computations
Sometimes it would be really helpful to be able to create a callback that is dependent on the result of two computations. This is where thenCombine becomes handy.

thenCombine allows us to register a BiFunction callback depending on the result of two CompletionStages.

To see how this is done, let’s in addition to finding a receiver also execute the heavy job of creating some content before sending a message.

         CompletableFuture<String> to =  
            CompletableFuture.supplyAsync(this::findReceiver);

        CompletableFuture<String> text =  
         CompletableFuture.supplyAsync(this::createContent);

        to.thenCombine(text, this::sendMsg);  

First, we've started two asynchronous jobs — finding a receiver and creating some content. Then we use thenCombine to say what we want to do with the result of these two computations by defining our BiFunction.

It's worth mentioning that there is another variant of thenCombine as well — called runAfterBoth. This version takes a Runnable not caring about the actual values of the preceding computation — only that they're actually done.

Callback dependent on one or the other
Ok, so we've now covered the scenario where you depend on two computations. Now, what about when you just need the result of one of them?

Let’s say you have two sources of finding a receiver. You’ll ask both, but will be happy with the first one returning with a result.

         CompletableFuture<String> firstSource =  
              CompletableFuture.supplyAsync(this::findByFirstSource);

        CompletableFuture<String> secondSource =  
              CompletableFuture.supplyAsync(this::findBySecondSource);

        firstSource.acceptEither(secondSource, this::sendMsg);  

As you can see, it's solved easily by acceptEither taking the two awaiting calculations and a Function that will be executed with the result of the first one to return.


**Code Example :**

https://gist.github.com/danveloper/5398943
https://gist.github.com/mohanr/43a6204640aaf86a1c0b
https://gist.github.com/ben-barbier/00133fbae5b18d6cd2020a8e1e6e78e9
http://www.nurkiewicz.com/2013/05/java-8-definitive-guide-to.html
