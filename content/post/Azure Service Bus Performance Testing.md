---
title: "Azure Service Bus Performance Testing"
date: 2018-07-18
tags: ["azure", "service", "bus", "cloud", "python", "performance", "testing"]
draft: false
---

## Azure Service Bus Tests In Python

There was a requirement from the team to test the performance of Azure Service Bus using python scripts. It was stated that this approach would make the overall system tests easier to run once it was in production with just a couple of scripts.

List of Tests:

> - Create and Delete 10 Azure Service Bus queues
> - Logging one thousand records to the queue
> - Logging ten thousand records to the queue
> - Check for minimum capacity threshold of the queue
> - Check for maximum ASB queue capacity

Before we start writing the scripts, let's make sure to have the setup ready for test scripts to run.

> - Install Python 3+
> - Install azure-servicebus sdk for python from : [azure-servicebus 0.21.1](https://pypi.org/project/azure-servicebus/)
> - Make sure to have ASB Namespace setup in the Azure portal. Take note of the SharedAccessKeyName and SharedAccessKeyValue associated with it.

Once you have all of the above in place, import the necessary modules.

```
from azure.servicebus import ServiceBusService, Message, Queue
import sys, asyncio, json, datetime
```
Initiate Azure Service Bus connection:
```
bus_service = ServiceBusService(
			        service_namespace='TestBus',
              shared_access_key_name='your_key',
              shared_access_key_value='your_val')  
```
You can use datetime module to track time taken to create and delete queues.

### Test 1 - Create And Delete 10 ASB Queues

```
currentStamp = datetime.datetime

def main():
    try:
         createNewQueue = "sampleTestQueue"
        
         CreateASBQueue(createNewQueue)

         DeleteASBQueue(createNewQueue)

    except Exception as exception:
        print(exception)
        
#CreateQueue(Up to 10)
def CreateASBQueue(queueName):
    for queueCount in range(10):   
      bus_service.create_queue(queueName + str(queueCount += 1))
        
#DeleteQueue(Up to 10)
def DeleteASBQueue(queueName):
    for queueCount in range(10):
      bus_service.delete_queue(queueName + str(queueCount += 1))
        
main()

```

### Test 2 & 3 - Logging One/Ten Thousand Records To The Queue


Let's have a sample event class:


```
class SampleEvent:

    def __init__(self):
        self.FieldTypeID = 1
        self.FieldType = "SUP"
        self.FieldCategory = 2
        self.FieldTime = str(datetime.datetime.now())
        self.FieldIP = "192.111.222.110"
        self.FieldDetails = """In computer science, a tail call is a subroutine call performed as the final action of a procedure. If a tail call might lead to the same subroutine being called again later in the call chain, the subroutine is said to be tail-recursive, which is a special case of recursion. Tail recursion (or tail-end recursion) is particularly useful, and often easy to handle in implementations.Tail calls can be implemented without adding a new stack frame to the call stack. Most of the frame of the current procedure is no longer needed, and can be replaced by the frame of the tail call, modified as appropriate (similar to overlay for processes, but for function calls). The program can then jump to the called subroutine. Producing such code instead of a standard call sequence is called tail call elimination. Tail call elimination allows procedure calls in tail position to be implemented as efficiently as goto statements, thus allowing efficient structured programming. In the words of Guy L. Steele.Peek wil."""
        self.FieldDetailSize = 1024
        self.FieldSignature = "Y89IZY0BwutpsCoOjgLR7CooraKleeaPofg"
```

Make sure to create an Azure Service Bus connection as above before using the below code. We are also converting the `SampleEvent object to json` before pushing it to the Queue:

```
#Entry point to the script
def main():
    try:
        #Sample Event to be sent to the Queue.
        event = Message(json.dumps(SampleEvent().__dict__))

        ClearUpASBQueue()

        LogOneThousandEvents(event)

    except Exception as exception:
        print(exception)

#Log 1k/10k events.
def LogOneThousandRecords(event):
    global countOfEvents
    allEvents = []
    #Change the range to 50 when testing for 10k events.
    for batchList in range(5):
        allEvents.append(BuildEventsInBatch(event))

    asyncEventLoop = asyncio.get_event_loop()
    allEventTasks = []

    for batchCount in range(5):
        #start = time.time()
        eventTask = asyncio.ensure_future(LogEventsToQueue(allEvents[batchCount]))
        allEventTasks.append(eventTask)
        #end = time.time()
    
    print(str(currentStamp.now().time().replace(microsecond=0)) + ": Logging 1k Events..Please Wait.. ")
    asyncEventLoop.run_until_complete(asyncio.wait(allEventTasks))
    print(str(currentStamp.now().time().replace(microsecond=0)) + ": All Events Logged Successfully.. ")
    countOfEvents = 0
```

`Batches of 200 records` are inserted into the Queue in one iteration as `there's a 256 KB max limit on the size of messages that can be inserted in a single insert call`. So, there's a method for creating batches of messages of 200 for every iteration.

```
#Building a batch of events of up to 200.
def BuildEventsInBatch(event):
    eventsList = []
    for msgCount in range(200):
        eventsList.append(event)

    return eventsList
```

Logging of messages had to happen `asynchronously so we have used asyncio` module to achieve it.

```
#Async Logging of events to ASB.
async def LogEventsToQueue(allMessages):
    try:
        global countOfEvents
        countOfEvents += len(allMessages)
        bus_service.send_queue_message_batch(currentQueue, allMessages)
        print(str(currentStamp.now().time().replace(microsecond=0)) + ": Events Logged : " + str(countOfEvents))

    except Exception as exception:
        exceptionMessage = str(exception)
        if "Request Entity Too Large" in exceptionMessage:
            print(str(currentStamp.now().time().replace(microsecond=0)) + ": Please check the event size and try again!")
            print(exception)
            sys.exit()
        else:
            print(exceptionMessage)
```

Finally, we have a method to `clear up the ASB queue` before start and end of any operations on the Queue.

```
#Clear up ASB Queue by reading all the events.
def ClearUpASBQueue():
    totalEventsCount = bus_service.get_queue(currentQueue).message_count
    print(str(currentStamp.now().time().replace(microsecond=0)) + ": Clearing ASB Queue...Please wait..")
    for eventCount in range(totalEventsCount):
        bus_service.read_delete_queue_message(currentQueue)
        print(str(currentStamp.now().time().replace(microsecond=0)) + ": Remaining Events : " + str(totalEventsCount - eventCount) + 
        ", Progress% : " + str(format((eventCount / totalEventsCount) * 100, '.3f')))
    
    print(str(currentStamp.now().time().replace(microsecond=0)) + ": All Events From The Queue Deleted! Queue is Empty Now!")

main()

```

### Test 4 - Minimum Capacity Threshold Of The Queue

For the most part, code remains the same as above with respect to the imports and initial parameters. Minimum capacity threshold will vary for you depending on your requirements. We had a `minimum cap of around 8.6 million records(equivalent to 11.3 GB in size)` to be stored.

```
#QueueName
currentQueue = "testQueue"

#Entry point to the script
def main():
    try:    
        #Sample Event to be sent to the Queue.
        event = Message(json.dumps(SampleEvent().__dict__))

        #Size in bytes, so we'll need to convert it to GB by dividing it by a billion.
        filledQueueSize = (bus_service.get_queue(currentQueue).size_in_bytes)/1000000000

        ClearUpASBQueue()

        print(str(currentStamp.now().time().replace(microsecond=0)) + ": Minimum ASB Queue Size Threshold To Be Achieved - 11.3 GB (8,640,000 events)")           
        print(str(currentStamp.now().time().replace(microsecond=0)) + ": Currently Used ASB Queue Size(GB) - " + str(format(filledQueueSize, '.2f')))
        print(str(currentStamp.now().time().replace(microsecond=0)) + ": Adding more events in the Queue to reach ASB Queue Size for minimal survival threshold!")
        
        LogEventToFillUpASBToMinThreshold(event)

    except Exception as exception:
        print(exception)
```

Method to log events to a minimum threshold limit stated above.

```
#Log a batch of events to the service bus Queue asynchronously.
#Currently set the batch limit to 200.
#Log events up to 11.3 GB.
def LogEventToFillUpASBToMinThreshold(event):
    global countOfEvents
    allEvents = BuildEventsInBatch(event)
    asyncEventLoop = asyncio.get_event_loop()
    allEventTasks = []

    for batchCount in range(45000):
        #start = time.time()
        msgTask = asyncio.ensure_future(LogEventsToQueue(allEvents))
        allEventTasks.append(msgTask)
        #end = time.time()
    
    print(str(currentStamp.now().time().replace(microsecond=0)) + ": Logging Events..Please Wait.. ")
    asyncEventLoop.run_until_complete(asyncio.wait(allEventTasks))
    countOfEvents = 0
```

LogEventsToTheQueue remains the same as the previous tests. Here, there's an additional method to actually `track the progress of the records` being inserted to the Queue.

```
#Async Logging of events to ASB.
async def LogEventsToQueue(allMessages):
    try:
        global countOfEvents
        countOfEvents += len(allMessages)
        bus_service.send_queue_message_batch(currentQueue, allMessages)
        print(str(currentStamp.now().time().replace(microsecond=0)) + ": Events Logged : " + str(countOfEvents)
                + ", Progress% - " +  ReportProgress())

    except Exception as exception:
        exceptionMessage = str(exception)
        if "Request Entity Too Large" in exceptionMessage:
            print(str(currentStamp.now().time().replace(microsecond=0)) + ": Please check the event size and try again!")
            print(exception)
            sys.exit()
        else:
            print(exceptionMessage)

#Responsible for reporting progress of the process.
def ReportProgress():
    currentEventsCount = bus_service.get_queue(currentQueue).message_count
    eventsMaxLimit = 8640000
    if currentEventsCount > eventsMaxLimit:
        print(str(currentStamp.now().time().replace(microsecond=0)) + ": Minimum events survival ASB Queue Size threshold Reached!")
        sys.exit()
    
    else:
        return str(format((currentEventsCount / eventsMaxLimit) * 100, '.3f'))
```

I must admit, it takes quite a while for this particular test to complete as the APIs made available by the SDK are all `REST APIs.`


### Test 5 - Maximum Capacity Of The Queue

In this particular test, we were looking to test for about `11.3 million events`, which would test the maximum limit of the Queue at `16 GB in storage size`. Code mostly remains the same other than the limit factor bound to this test.

```
#QueueName
currentQueue = "testQueue"

#Entry point to the script
def main():
    try:
        ClearUpASBQueue()

        CheckForMaximumASBCapacity()

    except Exception as exception:
        print(exception)


#Log a batch of events to the service bus Queue asynchronously.
#Fill up the ASB.
#Currently set the batch limit to 200.
#Log events up to 11.3 million.
def LogEventToFillUpASBToMaxCapacity(event):
    global countOfEvents
    allEvents = BuildEventsInBatch(event)
    asyncEventLoop = asyncio.get_event_loop()
    allEventTasks = []

    for batchCount in range(70000):
        #start = time.time()
        msgTask = asyncio.ensure_future(LogEventsToQueue(allEvents))
        allEventTasks.append(msgTask)
        #end = time.time()
    
    print(str(currentStamp.now().time().replace(microsecond=0)) + ": Logging Events..Please Wait.. ")
    asyncEventLoop.run_until_complete(asyncio.wait(allEventTasks))
    countOfEvents = 0

#Check for maximum ASB capacity. In our case, 16 GB memory space.
def CheckForMaximumASBCapacity():
    #Sample Event to be sent to the Queue.
    event = Message(json.dumps(SampleEvent().__dict__))
    print(str(currentStamp.now().time().replace(microsecond=0)) + ": Checking for total filled ASB Queue size...")
    #Size in bytes, so we'll need to convert it to GB by dividing it by a billion.
    occupiedQueueSize = (bus_service.get_queue(currentQueue).size_in_bytes)/1000000000
    print(str(currentStamp.now().time().replace(microsecond=0)) + ": Total ASB Queue Size To Be Achieved - 16 GB (12,236,374 events)")
    print(str(currentStamp.now().time().replace(microsecond=0)) + ": Currently Used ASB Queue Size(GB) - " + str(format(occupiedQueueSize, '.2f')))
  
    print(str(currentStamp.now().time().replace(microsecond=0)) + ": Maximum ASB Capacity for Storing Events Not Reached Yet!Logging More Events To Fill Max Capacity now...")
    LogEventToFillUpASBToMaxCapacity(event)
    
```

`ReportProgress method changes the maximum limit` value to handle a higher limit for this test.

```

#Responsible for reporting progress of the process.
def ReportProgress():
    currentEventsCount = bus_service.get_queue(currentQueue).message_count
    eventsMaxLimit = 12236374
    if currentEventsCount > eventsMaxLimit:
        print(str(currentStamp.now().time().replace(microsecond=0)) + ": Maximum ASB Capacity for Storing Events Reached!")
        sys.exit()
    
    else:
        return str(format((currentEventsCount / eventsMaxLimit) * 100, '.3f'))

```


