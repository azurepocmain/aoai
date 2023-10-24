# Grounding language model on your data 
<p align="center">  
<img src="https://github.com/azurepocmain/aoai/assets/91505344/e386c8cf-e0fb-414d-a65b-dc90b8d3f06a" width="500" height="300"></p>

As organizations attempt to unlock their data corpus with Azure Open AI LLM. A unique challenge has arisen. How to properly ground user’s questions to reduce the probability of hallucination and ensure that the proper context for the LLM. 
_______________________________________________________________________________________
While this is a complex topic, this repo takes a very unique approach of solving this issue by leveraging Azure Open AI function argument. With a vector database cosine similarity search. This allows us to break down a user’s questions to tokens, select the required tokens, then process that token against a vector index. 
_______________________________________________________________________________________
You may be tempted to use an VM open source vector database, however, I would caution you against such an action. While working with a number of clients, they were unable to experience satisfactory results. We ultimately ended up going with an enterprise level vector store as Azure Cosmos DB Mongo DB Vcore or Azure Cognitive Search. 

