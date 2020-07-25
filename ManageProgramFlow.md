# 1 Managing Program Flow

## 1.1 Implement Multithreading and Asynchronous Processing

### Threads

- On windows each application runs in it's own **process**. A process is used to isolate applications, each has its own virtual memory.
- Support for threads is found in the namespace System.Threading
- Should not be used unless you have a special need

Creating a thread:

``` csharp
Thread t = new Thread(new ThreadStart(ThreadMethod);

for(int i = 0; i < 4; i++)
{
    Console.WriteLine("Do some work");
    // thread sleep ois used to signal that a thread is 
    // complete and it will switch to another thread
    Thread.Sleep(0);
}

// starting the thread
t.Start();
// waiting for the thread to complete
t.Join();

 ```

 **Background/Foreground Threads** - Foreground thread keep an application alive. Only when all threads have completed does the CLR shutdown the application.

**ParameterizedThreadStart** - Used instead of TheadStart. Used to allow the creation of thread with arguments.

**ThreadStaticAttribute** - Used to define data in the class body where each thread will get its own copy of the variable.

**Thread Pools**

- Creating a new thread every time adds additional overhead to an application. A solution to this is to use thread pools.
- You queue task to be processed when task become available

``` csharp
ThreadPool.QueueUserWorkItem(() => 
{
    Console.WriteLine("Working on thread from a threadpool");
});
```

### Tasks

- For threads there is no built in way to see if an operation has finished and what the results is
- Tasks provide this
- A _task scheduler_ is responsible for starting and managing tasks

```csharp
Task t = Task.Run(() => return 42);

// Waits for task to complete
t.Wait();

// OR you can use Result property

// 42
Console.WriteLine(t.Result)
```

**Continue With**

- The continue with property on a task allows to define another operation that is run on the completion of the current task
- Adding TaskCancellationOptions enum allows you to specify when to continue with an operation. Task cancellation options includes the following:
    - OnlyOnCancelled
    - OnlyOnFaulted
    - OnlyOnRunToCompletion

**Attaching a child task**

You can attach child task to a parent task by using TaskCreationOptions in the Task constructor
```csharp
Task<Int32[]> parent = Task.Run(() => 
{
    var results = new Int<32>[3];
    new Task(() => results[0] = 0, 
    TaskCreationOptions.AttachedToParent).Start();
    new Task(() => results[1] = 1, 
    TaskCreationOptions.AttachedToParent).Start();
    new Task(() => results[2] = 2, 
    TaskCreationOptions.AttachedToParent).Start();
    return results;
});
```


### The Parallel Class

```csharp
Parallel.For(0, 10, i => 
{
    Thread.Sleep(1000);
});

// OR

IEnumerable<int> numbers = Enumerable.Range(0, 1000);
Parallel.ForEach(numbers, n => 
{
    Thread.Sleep(1000);
});

// With ParallelLoopState
Parallel.For(0, 1000, (int i, ParallelLoopState loopState)  => 
{
    if(i == 500)
    {
        loopState.Break();
    }
    Thread.Sleep(1000);
});

```

### Async and Await

### PLINQ

### Concurrent Collections

