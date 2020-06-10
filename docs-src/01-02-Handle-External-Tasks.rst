.. _01-02-Handle-External-Tasks:

=====================
Handle External Tasks
=====================

Handling External Tasks is your bread and butter mechanism for process
automation. We'll introduce you to the basics.

Create the External Task Client
===============================

Create an ``ExternalTaskClient`` instance using this

.. sourcecode:: csharp

    var addressOfAtlasEngine = new Uri("[Engine Endpoint]");
    var client = new ExternalTaskClient(addressOfAtlasEngine);

You can specify a custom identity accessor and a logger implementation.

.. note::

    Since we omitted the identity accessor parameter, we are working anonymously.

.. note::

    By not specifiying a logger, no diagnosable information is disclosed. We 
    are recommending specifying a logger, but not forcing it onto you. There's
    also a ``ConsoleLogger`` implementation dumping to ``StdOut`` and ``Debug``.

Subscribe to a topic
====================

The External Tasks you have modeled, will have a topic assigned, which you
can subscribe to

.. sourcecode:: csharp

    client.SubscribeToExternalTaskTopic(
        "[Topic]",
        p => p.[Choose call strategy]);
        
    client.StartAsync();

    client.StopAsync();

Choosing a call strategy
========================

Since handler activation is highly depending on your needs, there is no 
appropriate default. But don't worry, there is some help provided!
To avoid a type analysis at runtime, we encourage you to provide us the type
information.

Methods handlers
----------------

To use a method as your handler pass in a delegate. 

.. sourcecode:: csharp

    CallStrategySelector.UseHandlerMethod<TInput, TOutput>([Your Method])

.. note::

    It may be async by returning a ``Task<TOutput>``, too.

Typed handlers
----------------

Switching to typed handlers requires you to implement `IExternalTaskHandler<TInput, TOutput>`.
For activation you can specify a factory method as the call strategy:

.. sourcecode:: csharp

    CallStrategySelector.UseHandlerFactory<THandler, TInput, TOutput>(() => new THandler())

Typed handlers using custom activators
--------------------------------------

For more advanced scenarios, you can specify an custom Activator.
This is the way to resolve handlers using an IoC container.

.. sourcecode:: csharp

    CallStrategySelector.UseHandlerActivator<THandler, TInput, TOutput>([Some Activator])

Customize the behavior
======================

Fetching tasks and dispatching them to your handlers involves some more work,
but we have chosen to hide it in the library. You can tweak it though by providing
``WorkerSettings`` when configuring the topic subscription...

*   ``WorkerId`` 
    This is useful on clusters. If you'd like to trace which worker handled a
    specific task, you can provide it here. An empty worker ID is substituted
    by an uniquely generated ID.

*   ``MaxParallelWorkerCount``
    As soon as an External Task is fetched, we'll dispatch it to your handler.
    You can have multiple handlers active at the same time to scale out.
    The default is set to ``5``.

*   ``LongPollingDuration``
    Sometimes there's just no work to be done. We'll use long-polling to keep 
    the connection open while fetching. The default is ``30s``.

*   ``LockDuration``
    Your handler may take some time to process. A self-terminating lock keeps
    other handlers from claiming the task while you are busy. The lock is 
    periodically refreshed as some sort of keep-alive. The default is ``15s``.
