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
- Split task to be run concurrently 
- Only worth doing if you have a lot of work to do

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

- Running a task using the async 
- **async** and **await** keywords were introduced in C# 5
- the **await** keyword means that the compiler will generate code to see if the task is complete. If complete the code continues synchronously if not he compiler hooks up a continuation method.
- If you are building a client application that must remain responsive you can use async await to offload long running tasks on to another thread
  
 **Synchronisation Context** - Connects application model to its threading model.
- a WPF application uses a single thread for the UI and potentially multiple background threads
- an ASP.NET application uses threads from a thread pool to serve incoming applications
- await makes sure that the current synchronsiation context is saved and restored at the end of the task
- You can disable the flow of the synchronisation context to improve performance this can be done by using ConfigureAwait method on the task

``` csharp
// WPF Event Handler
private async void Button_Click(object sender, RoutedEventArgs e)
{
    HttpClient httpClient = new HttpClient();
    string content = await httpClient
        .GetStringAsync("http://www.microsoft.com")
        .ConfigureAwait(false);

    // Because configure await is set to false 
    // this line is not executed on the UI thread
    Output.Content = context;
}
```

- You should only return void from an async method when dealing with an asynchronous event

### PLINQ
- PLINQ is a parallel implementation of LINQ
- PLINQ returns ParallelEnumerable object

**.AsParallel()** - used to access PLINQ from a IEnumerable

**.AsSequential** - specifies the rest of the query should occur as none parallel linq

**.WithDegreeOfParallelism** - specifies the maximum number of concurrent tasks that can occur at once

**.AsOrdered** - specifies that that order of the collection be maintained in the query

**.AsUnordered** - specifies that the order of the collection does not have to preserved by the query

**ForAll** - iterate over the collection in a parallel way

```csharp
var parallelResults = Enumerable.Range(0, 20)
                                .AsParallel()
                                .Where(i => i % 2);

parallelResults.ForAll(e => Console.WriteLine(e));
```

All exceptions thrown in a PLINQ statement are collected in an **AggregatedException**

### Concurrent Collections
- When working with multiple thread that share a collection you need to synchronise access to the data. This can be done through concurrent collections.


**Blocking Collection**
- Blocks access to the data until data comes available
- In reality a wrapper around other concurrent collections. By default it will use ConcurrentQueue
- Methods:
  - Adds - adds to the collection
  - Take - either takes an returns and removes an item from the collection or waits for data to be added
  - CompleteAdding - signal no more items will be added allowing the collection to stop blocking other threads
  - GetConsumingEnumerable - waits data to be returned and then returns it as an IEnumerable

**Concurrent Bag**
- A thread safe unordered collection of items
- Methods include **Peek**, **TryPeek** (not very useful in a multi threaded environment) and **TryTake** 
- Implements IEnumerable<T> by taking a snapshot of the collection

**Concurrent Stack (LIFO)**  
- Push and TryPop
- Can add and remove a range by using PushRange and TryPopRange
- Implements IEnumerable by taking a snapshot

**Concurrent Queue (FIFO)**
- Enqueue and TryDequeue
- Implements IEnumerable by taking a snapshot

**Concurrent Dictionary**
- A thread safe implementation of the dictionary
- Contains atomic actions to ensure thread safe
  - AddOrUpdate - Ensures an item is added if it is not there
  - GetOrAdd - Gets the value of teh item if it is available otherwise it will add the item
  
## 1.2 Manage Multithreading

### Synchronising Resources

### Volatile Class

### The Interlocked Class

### Canceling Tasks



