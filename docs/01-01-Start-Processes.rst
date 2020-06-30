.. _01-01-Start-Processes:

==============================
Starting new process instances
==============================

If you plan on invoking processes programatically, this is for you. By reading
this section, you'll gain knowledge of how to start process instances.

Create the API Client
=====================

You'll need to create an Process Control API Client instance as follows

.. sourcecode:: csharp

    var addressOfAtlasEngine = new Uri("[Engine Endpoint]");
    var client = ApiClientFactory.CreateProcessControlApiClient(addressOfAtlasEngine);

.. note::

    Since we omitted the identity accessor parameter, we are working anonymously.

Start process instances
=======================

The API Client can be used to start a new instance of a given process model using

.. sourcecode:: csharp

    await client.StartProcessInstanceAsync("[Process Model Name]");

This will complete as soon as the process instance has been created and return information
about the process instance like the instance ID or the correlation ID.

Run process instances to the end
================================

The API Client can also be used to run a process instance until completion

.. sourcecode:: csharp

    await client.ExecuteProcessInstanceAsync("[Process Model Name]");

This will wait for the process instance to finish and return the final token content as well
as the end event being hit.

Customize the start
===================

The API Client has several parameters you won't have to specify. Let's investigate those:

*   ``StartEventId`` 
    You won't need to specify this if the process model only has a single start event. In other
    cases you can specify an ID to address a start event explicitly.

*   ``Payload``
    If the process requires any kind of input value, you can specify it here.

*   ``CorrelationId``
    To trace single process instances you can specify an own correlation ID. This will help
    tracking it in your code or while inspecting using Atlas Studio.

*   ``CallerId``
    If the process you are calling is to be treated as a sub-process of another process, you 
    should refer to the parent instance using this ID.
