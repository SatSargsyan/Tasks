# Tasks


<a href=https://msdn.microsoft.com/ru-ru/library/system.threading.tasks.task(v=vs.110).aspx> Wait(CancellationToken) и Wait(Int32, CancellationToken)</a>

```C#
using System;
using System.Threading.Tasks;

public class Example
{
   public static void Main()
   {
      Task t = Task.Run( () => {
                                  // Just loop.
                                  int ctr = 0;
                                  for (ctr = 0; ctr <= 1000000; ctr++)
                                  {}
                                  Console.WriteLine("Finished {0} loop iterations",
                                                    ctr);
                               } );
      t.Wait();
   }
}
// The example displays the following output:
//        Finished 1000001 loop iterations
```



```C#
using System;   
using System.Threading;
using System.Threading.Tasks;

class Program
{
    static Random rand = new Random();

    static void Main()
    {
        // Wait on a single task with no timeout specified.
        Task taskA = Task.Run( () => Thread.Sleep(2000));
        Console.WriteLine("taskA Status: {0}", taskA.Status);
        try {
          taskA.Wait();
          Console.WriteLine("taskA Status: {0}", taskA.Status);
       } 
       catch (AggregateException) {
          Console.WriteLine("Exception in taskA.");
       }   
    }    
}
// The example displays output like the following:
//     taskA Status: WaitingToRun
//     taskA Status: RanToCompletion

```

```C#

using System;
using System.Threading;
using System.Threading.Tasks;

public class Example
{
   public static void Main()
   {
      // Wait on a single task with a timeout specified.
      Task taskA = Task.Run( () => Thread.Sleep(2000));
      try {
        taskA.Wait(1000);       // Wait for 1 second.
        bool completed = taskA.IsCompleted;
        Console.WriteLine("Task A completed: {0}, Status: {1}",
                         completed, taskA.Status);
        if (! completed)
           Console.WriteLine("Timed out before task A completed.");                 
       }
       catch (AggregateException) {
          Console.WriteLine("Exception in taskA.");
       }   
   }
}
// The example displays output like the following:
//     Task A completed: False, Status: Running
//     Timed out before task A completed.
```

```C#
using System;
using System.Threading;
using System.Threading.Tasks;

public class Example
{
   public static void Main()
   {
      var tasks = new Task[3];
      var rnd = new Random();
      for (int ctr = 0; ctr <= 2; ctr++)
         tasks[ctr] = Task.Run( () => Thread.Sleep(rnd.Next(500, 3000)));

      try {
         int index = Task.WaitAny(tasks);
         Console.WriteLine("Task #{0} completed first.\n", tasks[index].Id);
         Console.WriteLine("Status of all tasks:");
         foreach (var t in tasks)
            Console.WriteLine("   Task #{0}: {1}", t.Id, t.Status);
      }
      catch (AggregateException) {
         Console.WriteLine("An exception occurred.");
      }
   }
}
// The example displays output like the following:
//     Task #1 completed first.
//     
//     Status of all tasks:
//        Task #3: Running
//        Task #1: RanToCompletion
//        Task #4: Running
```

```C#
using System;
using System.Threading;
using System.Threading.Tasks;

public class Example
{
   public static void Main()
   {
      // Wait for all tasks to complete.
      Task[] tasks = new Task[10];
      for (int i = 0; i < 10; i++)
      {
          tasks[i] = Task.Run(() => Thread.Sleep(2000));
      }
      try {
         Task.WaitAll(tasks);
      }
      catch (AggregateException ae) {
         Console.WriteLine("One or more exceptions occurred: ");
         foreach (var ex in ae.Flatten().InnerExceptions)
            Console.WriteLine("   {0}", ex.Message);
      }   

      Console.WriteLine("Status of completed tasks:");
      foreach (var t in tasks)
         Console.WriteLine("   Task #{0}: {1}", t.Id, t.Status);
   }
}
// The example displays the following output:
//     Status of completed tasks:
//        Task #2: RanToCompletion
//        Task #1: RanToCompletion
//        Task #3: RanToCompletion
//        Task #4: RanToCompletion
//        Task #6: RanToCompletion
//        Task #5: RanToCompletion
//        Task #7: RanToCompletion
//        Task #8: RanToCompletion
//        Task #9: RanToCompletion
//        Task #10: RanToCompletion
```
