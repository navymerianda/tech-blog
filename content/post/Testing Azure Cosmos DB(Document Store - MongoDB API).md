---
title: "Testing Azure Cosmos DB(Document Store - MongoDB API)"
date: 2018-07-20
tags: ["mongo", "azure", "cosmos", "database"]
draft: false
---

This was about  a time when the team was planning to store logs in MongoDB and were looking at different alternatives to achieve the same. We stumbled across a new multi-model database called 'Azure Cosmos DB'. It supports a lot of good features that include but not limited to independent scaling and storing data across geographical regions for better throughput. It follows a pay-as-you-use model which does help in reducing the costs. They also provide native support for various data models of NoSQL, which include Column-family, Key-Value, Graph and document based. For instance, if you were to move your existing MongoDB to Azure cosmos DB, it would be as simple as just changing the connection string with respect to the associated DB. You can use the existing mongo APIs for all your operations. You data access layer code remains intact and doesn't have to be changed in the process of migration.

Now, let's look into setting up the DB and running the code in python to test the performance of Azure cosmos DB.

First and foremost, you're required to setup an Azure cosmos DB account, before we move on to creating a DB. In the process of setting up the account, you'll be given an option to enable geo-redundancy— in case you want Microsoft to replicate the databases for you across different regions for better throughput, availability and disaster recovery options. Next up, there's another option to enable multi-master support, which is essential to be enabled for the reasons owing to databases being able to update and maintain consistency across regions with very minimal read and write throughput of less than < 10 ms. 

Once you have setup the account, you can then go ahead and create a database based on your requirements, here we'll be making use of document based database, would choose the MongoDB API at the creation window. Once that is done, we move on to creating collections. In the collection creation window, we are given a choice between selecting a fixed storage of 10 GB or unlimited. As this is pay-as-you-use model, we were happy to stick to the 10 GB limit for the initial operations, and the cost is computed by a currency known as the request units per second. This unit varies from read operations to write operations. Cost is computed differently for read, write and delete operations. You can find the link to the calculator below based on your requirement, it'll show you the RU/s that will be charged for each operation on the DB.

[Azure Cosmos DB Currency Calculator](https://www.documentdb.com/capacityplanner)

Maximum RU/s possible for a fixed size of 10 GB is capped at 10k RUs. As per the sample document by Microsoft, they have said that for a document of 1 KB each read operation would require 1 RU whereas write and delete operations would take up close to 5 RU.

In case you wish to provide a unique key yourself for the collection, that can be set as well at the time of collection creation.

You'll need to have pymongo installed to continue further. It's a library essential for you to carry out any operations on MongoDB.
[PyMongo]http://api.mongodb.com/python/current/installation.html

Import the libraries and initialize the variables required as below.

```
import json, pymongo, datetime, sys, bson, random

url = 'https://testCollectionAzure.documents.azure.com:443/'
key = 'bb44LVqsg8W3ejyRiYAvufGndrujbvYSY1pxE5151cVl0lOLwsiGKaEORXu4141iEDTbokRje39zJFv5Mi24141==' # primary key

mongoUri = "mongodb://bb44LVqsg8W3ejyRiYAvufGndrujbvYSY1pxE5151cVl0lOLwsiGKaEORXu4141iEDTbokRje39zJFv5Mi24141==@testCollectionAzure.documents.azure.com:10255/?ssl=true&replicaSet=globaldb"

databaseName = 'EventTestStorage'
collectionName = 'Event_Records'

```
Next up, we move on to main function where we load the sample json file to insert some data to the DB.

```
def main():
    #Reading json document - Event Data
    with open('eventDocument.json') as eventDoc:
        eventRecord = json.load(eventDoc)
    

    LogEvents(eventRecord)
```

Once we have the document loaded, we move to establishing the connection with the DB and log a sample record.

```
def LogEvents(eventRecord):
    #Initialize pymongo client
    try:
        pyMongoclient = pymongo.MongoClient(mongoUri)
        pyMongodb = pyMongoclient[databaseName]

        pyMongoCollection = pyMongodb[collectionName]
        #pyMongoCollection.drop_indexes()
        
        #Insert a document
        eventId = pyMongoCollection.insert_one(eventRecord)
        print(eventId.inserted_id)

        #Get Total Count
        print(pyMongoCollection.count())

        #Log 1M Events
        #InsertRecords(pyMongoCollection)
		#print("Inserted 1M Records!")

    except Exception as bwe:
        print(bwe)
```

We can even drop indexes of collections and set up your own indexes for better search outputs if required. You can see that we supported insertion of a million records as well to test the throughput and RUs for our collection.

In case, there is a need for you to check the individual document size in MongoDB, you can use the following code.

```
#def CheckSingleRecordSize():
    sampledoc = pyMongoCollection.find_one({'event.SourceIP': '192.111.222.90'})
    print(str(len(bson.BSON.encode(sampledoc))))
```
And, finally here's the code for inserting over a million events into the DB.

```
def InsertRecords(pyMongoCollection):
    print("Each batch has 1000 records.")
    for batchCount in range(1000):
        allRecords = GenerateSampleData()
        pyMongoCollection.insert_many(allRecords)
        print("Inserted Batch Count : " + str(batchCount + 1))

def GenerateSampleData():
    allEvents = []

    try:
        for eventCount in range(1000):
            with open('eventDocument.json') as eventDoc:
                eventRecord = json.load(eventDoc)

            eventRecord['InsertedRecordTime'] = str(datetime.datetime.now())
            eventRecord['previousHmac'] = str(eventRecord['previousHmac'] + str(random.randint(1,700)))
            eventRecord['currentHmac'] = str(eventRecord['currentHmac'] + str(random.randint(1,700)))
            eventRecord['event']['SourceIP'] = str(eventRecord['event']['SourceIP'] + str(random.randint(1,700)))
            eventRecord['event']['SourceType'] = str(random.randint(1,3))
            eventRecord['event']['Category'] = str(random.randint(1, 4))
            allEvents.append(eventRecord)


    except Exception as eventRecordExc:
        print(eventRecordExc)

    return allEvents

main()

```

You can find a sample event json below:

```
{
  "previousHmac":"20d2f6f6d9d4b0b536f74ca47410d69685d28139aa31aa88e504371d2d392",
  "event":{
     "SourceType" : "1",
     "SourceName" : "Test",
     "Category" : "2",
     "EventDateTime" : "04/01/2018 9:00:12",
     "SourceIP" : "192.111.222.123",
     "EventDetails" : "In computer science, a tail call is a subroutine call performed as the final action of a procedure. If a tail call might lead to the same subroutine being called again later in the call chain, the subroutine is said to be tail-recursive, which is a special case of recursion. Tail recursion (or tail-end recursion) is particularly useful, and often easy to handle in implementations.Tail calls can be implemented without adding a new stack frame to the call stack. Most of the frame of the current procedure is no longer needed, and can be replaced by the frame of the tail call, modified as appropriate (similar to overlay for processes, but for function calls). The program can then jump to the called subroutine. Producing such code instead of a standard call sequence is called tail call elimination. Tail call elimination allows procedure calls in tail position to be implemented as efficiently as goto statements, thus allowing efficient structured programming. In the words of Guy L. Steele.Peek wil.",
     "EventDetailSize" : 1024,
     "Signature" : "Y89IZY0BwutpsCoOjgLR7CooraKleeaPofg"
  },
  "eventstatus": 1,
  "currentHmac": "20d2f6f6d9d4b0b536f74ca47410d69685d28139aa31aa88e504371d2d392",
  "InsertedRecordTime" : "04/1/2018 9:45:06"
}
```

In case of any queries, please feel free to drop your comments below.