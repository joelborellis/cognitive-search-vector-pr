---
page_type: sample
languages:
  - javascript
name: Vector search in Node.js
products:
  - azure
  - azure-cognitive-search
  - azure-openai
description: |
  Using @azure/search-documents and Node.js, index and query vectors in a RAG pattern or a traditional search solution.
urlFragment: vector-search-javascript
---

# Vector search using Node.js  (Azure AI Search)  

The JavaScript demo in this repository creates vectorized data that can be indexed and queried on Azure AI Search.

| Samples | Description |
|---------|-------------|
| **azure-search-vector-sample.js** | [End-to-end sample](#run-the-end-to-end-sample-program). It uses **@azure/search-documents** in the Azure SDK for JavaScript. It calls the next two JavaScript functions, which access a deployed model on your Azure OpenAI resource. It calls Azure AI Search to create and query an index. |
| **docs-text-openai-embeddings.js** | Generates embeddings for an index. Input is `data\text-sample.json`. Output is sent to `output\docVectors.json`. The output is usable as a request payload on a document upload action to Azure AI Search, but there are no calls to Azure AI Search in this code. |
| **query-text-openai-embeddings.js** | Generates an embedding for a query. Output is a vector that can be pasted into a vector query request. There are no calls to Azure AI Search in this code. |

## Prerequisites

+ An Azure subscription, with [access to Azure OpenAI](https://aka.ms/oai/access). You must have the Azure OpenAI service name and an API key.

+ A deployment of the **text-embedding-ada-002** embedding model. We use API version 2023-05-15 in this demo. For the deployment name, the deployment name is the same as the model, "text-embedding-ada-002".

+ Model capacity should be sufficient to handle the load. We successfully tested this sample on a deployment model having a 33K tokens per minute rate limit.

+ Node.js (these instructions were tested with version Node.js version 16.0)

+ For the end-to-end sample, you also need an Azure AI Search service. Provide the full endpoint, an Admin API key, and an index name as environment variables.

You can use [Visual Studio Code with the JavaScript extension](https://code.visualstudio.com/docs/nodejs/extensions) for this demo. For help setting up the environment, see this [JavaScript quickstart](https://learn.microsoft.com/azure/search/search-get-started-javascript).

## Setup

1. Clone this repository.

1. Create a .env file in the *demo-javascript/JavaScriptVectorDemo* directory and include the following variables

   ```bash
   AZURE_OPENAI_SERVICE_NAME=YOUR-AZURE-OPENAI-SERVICE-NAME
   AZURE_OPENAI_DEPLOYMENT_NAME=YOUR-AZURE-OPENAI-DEPLOYMENT-NAME
   AZURE_OPENAI_API_VERSION=YOUR-AZURE-OPENAI-API-VERSION
   AZURE_OPENAI_API_KEY=YOUR-AZURE-OPENAI-API-KEY
   AZURE_SEARCH_ENDPOINT=YOUR-AZURE_SEARCH_ENDPOINT
   AZURE_SEARCH_ADMIN_KEY=YOUR-AZURE_SEARCH_ADMIN_KEY
   AZURE_SEARCH_INDEX_NAME=YOUR-AZURE_SEARCH_INDEX_NAME
   ```

   **Key points**:

   + Service name should be the short name. For example, if the endpoint is `https://my-openai-svc.openai.azure.com/`, the service name is `my-openai-svc`.
   + Deployment name can be found in Azure AI Studio. Azure portal provides a link. We used `text-embedding-ada-002` for our deployment name. 
   + API version used for testing is `2023-05-15`.
   + Keys and endpoints can be found in the Azure portal pages for your Azure OpenAI resource.

1. Select **Terminal** and **New Terminal** to get a command line prompt. Install `npm` dependencies:

   ```bash
   cd demo-javascript/code
   npm install
   ```

## Run the vectorization code

This section explains how to run the separate vectorization programs that call Azure OpenAI. One program generates embeddings for a documents payload for indexing. The second program generates an embedding for a vector query.

### Document vectorization

Enter the following statement at the command line:

```bash
node docs-text-openai-embeddings.js
```

Output should look similar to this:

```bash
PS C:\Users\username\cognitive-search-vector-pr\demo-javascript\JavaSCriptVectorDemo\code> node docs-text-openai-embeddings.js
Reading data/text-sample.json...
Generating embeddings with Azure OpenAI...
Success! See output/docVectors.json
PS C:\Users\username\cognitive-search-vector-pr\demo-javascript\JavaSCriptVectorDemo\code> 
```

If you get an error, such as error code 429 or a server error, verify the model deployment capacity is sufficient to process the sample input. 

The generated output consists of embeddings for the title and content fields of the input data (`data/text-sample.json`).

### Query vectorization

Run the following program to generate a query embedding and execute vector queries:

```node query-text-openai-embedding.js```

Modify the `userQuery` variable in `query-text-openai-embedding.js` to customize the query.

## Run the end-to-end sample program

1. Modify the `.env` file in the `demo-javascript` directory to have the following variables

   ```bash
   AZURE_OPENAI_SERVICE_NAME=YOUR-AZURE-OPENAI-SERVICE-NAME
   AZURE_OPENAI_DEPLOYMENT_NAME=YOUR-AZURE-OPENAI-DEPLOYMENT-NAME
   AZURE_OPENAI_API_VERSION=YOUR-AZURE-OPENAI-API-VERSION
   AZURE_OPENAI_API_KEY=YOUR-AZURE-OPENAI-API-KEY
   AZURE_SEARCH_ENDPOINT=YOUR-AZURE_SEARCH_ENDPOINT
   AZURE_SEARCH_ADMIN_KEY=YOUR-AZURE_SEARCH_ADMIN_KEY
   AZURE_SEARCH_INDEX_NAME=YOUR-AZURE_SEARCH_INDEX_NAME
   ```

   **Key points**:

   + Azure OpenAI service name should be the short name. For example, if the endpoint is `https://my-openai-svc.openai.azure.com/`, the service name is `my-openai-svc`.
   + Azure OpenAI deployment name can be found in Azure AI Studio. Azure portal provides a link. We used `text-embedding-ada-002` for our deployment name. 
   + Azure OpenAI API version used for testing is `2023-05-15`.
   + Azure OpenAI keys and endpoints can be found in the Azure portal pages for your Azure OpenAI resource.
   + Azure AI Search endpoint should be the full URL, starting with `https://`.
   + Azure AI Search admin API key can be found in the **Keys** page in the Azure portal.
   + Azure AI Search index name should be unique, and start with a lowercase letter (no spaces or slashes).

   This end-to-end JavaScript sample shows you how to create a search index, generate documents embeddings, and upload them to an index. It also demonstrates several vector queries. It attemps to run a hybrid query that invokes semantic search. If you want that query to run, be sure to enable semantic search on your search service.

   All code is one file, split among functions, on purpose. Though the file is longer this way, the code is easier to follow when it's all together.

1. Select **Terminal** and **New Terminal** to get a command line prompt. Install `npm` dependencies:

   ```bash
   cd demo-javascript/code
   npm install
   ```

1. Run `node azure-search-vector-sample.js` to execute the program. The code takes several minutes to run. It creates index, loads the raw sample data, generates embeddings, loads the index with vector and non-vector content, and then begins a series of vector queries.

Output of the first several lines should look similar to this:

```bash
PS C:\Users\username\cognitive-search-vector-pr\demo-javascript\JavaSCriptVectorDemo\code> node azure-search-vector-sample.js
Creating ACS index...
Reading data/text-sample.json...
Generating embeddings with Azure OpenAI...
Uploading documents to ACS index...

Pure vector search results:
Title: Azure DevOps
Score: 0.8333178
Content: Azure DevOps is a suite of services that help you plan, build, and deploy applications. It includes Azure Boards for work item tracking, Azure Repos for source code management, Azure Pipelines for continuous integration and continuous deployment, Azure Test Plans for manual and automated testing, and Azure Artifacts for package management. DevOps supports a wide range of programming languages, frameworks, and platforms, making it easy to integrate with your existing development tools and processes. It also integrates with other Azure services, such as Azure App Service and Azure Functions.
Category: Developer Tools


Title: Azure App Service
Score: 0.808263
Content: Azure App Service is a fully managed platform for building, deploying, and scaling web apps. You can host web apps, mobile app backends, and RESTful APIs. It supports a variety of programming languages and frameworks, such as .NET, Java, Node.js, Python, and PHP. The service offers built-in auto-scaling and load balancing capabilities. It also provides integration with other Azure services, such as Azure DevOps, GitHub, and Bitbucket.
Category: Web

```

You can search the output for other query outcomes:

+ `Pure vector search results:`
+ `Pure vector search (multilingual) results:`
+ `Cross-field vector search results:`
+ `Vector search with filter results:`
+ `Hybrid search results:` (requires semantic search)

You can also use the Azure portal to explore the index definition or delete the index if you no longer need it.

## Troubleshoot errors

If you get error 429 from Azure OpenAI, it means the resource is over capacity:

+ Check the Activity Log of the Azure OpenAI service to see what else might be running.

+ Check the Tokens Per Minute (TPM) on the deployed model. On a system that isn't running other jobs, a TPM of 33K or higher should be sufficient to generate vectors for the sample data. You can try a model with more capacity if 429 errors persist.

+ Review these articles for information on rate limits: [Understanding rate limits](https://learn.microsoft.com/azure/ai-services/openai/how-to/quota?tabs=rest#understanding-rate-limits) and [A Guide to Azure OpenAI Service's Rate Limits and Monitoring](https://clemenssiebler.com/posts/understanding-azure-openai-rate-limits-monitoring/).
