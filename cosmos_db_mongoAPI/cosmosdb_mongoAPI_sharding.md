**Overview**
In scenarios where you need to vectorize millions of documents using Azure Cosmos DB vector index. Determining the most efficient method for creating a large collection of vector indexes is crucial. 
This document provides a detailed, step-by-step guide on sharding a collection to distribute data across multiple servers. 
This horizontal scaling approach enables parallelized query execution, thereby unleashing the full potential of Cosmos DB MongoAPI.

**Steps:**

**Step 1:**
In order to shard a collection, we need to configure the shard setting during the creation of the Cosmos DB for MongoDB service. When creating the service, please take notes of the below shard and cluster tier setting. 
The shard setting should be set to “2” and the tier at the highest tier as the below image.  Please also note that you should have the same storage size as your current instance. 
![image](https://github.com/user-attachments/assets/bc9260f7-42d2-4841-b983-124ff13cd7c5)

**Step 2:**
Once the new Cosmos DB for MongoDB cluster instance is created, we need to pre-stage the shard distribution before loading the collection. We need to use the admin DB to shard the collection. We can leverage the below command to do so, the breakdown is as follows: 
•	Command: The command needs to be invoked on the admin DB. 
•	shardCollection: Database name and collection name that will be shard separated by a dot. 
•	Key: The shard key that will be used to distribute the data. With the vector index it will be the results returned by the embedding. 

db.runCommand({
    shardCollection: "test101.cosmos_comicbooks_past25years",
    key: { company_name: "hashed" }
})

**Step 3:**
Verify the shard(s) on the instance, you can use the following to verify which collections are being shard:
sh.status()  or  db.printShardingStatus()

**Step 4:**
Do not create the vector index prior, we will create it after. Import the data to the same collection name of the above shard to have the data get distributed via the above shard parameters. 

**Step 5:**
Create the vector index using a static numLists value of 500. This seems to provide the best mixture of performance and results for very large collections. E.g. 250 million plus vector coordinates.  
The reason why you may use 500 is that the M80 has a set memory allocation and higher numLists and large data volumes may cause out of memory exceptions. 
Once higher tiers are released, this will not be a consideration, and we can increase the numLists to a higher value.

db.runCommand(
{
  "createIndexes": "cosmos_comicbooks_past25years",
  "indexes": [
    {
      "name": "cosmos_comicbooks_past25years_index",
      "key": {
        "embedding": "cosmosSearch"
      },
      "cosmosSearchOptions": {
        "kind": "vector-ivf",
        "numLists": 500,
        "similarity": "COS",
        "dimensions": 1536
      }
    }
  ],
  maxTimeMS: 7200000
})


Please note that index kind HNSW is available as well, which has been documented to have better result output.


