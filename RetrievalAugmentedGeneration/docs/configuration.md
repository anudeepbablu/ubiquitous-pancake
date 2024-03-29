## Configuration Guide

### Chain Server Configuration

In this section, we explore the configurations for the [Chain Server](./chat_server.md). Chain server interaction with other components can be controlled by config. Chain Server interacts with components such as the `milvus` vector store and `triton` server, which hosts the Large Language Model (LLM). Additionally, we'll delve into customization options to fine-tune the behavior of the query server. These options include settings for the embedding model, chunk size, and prompts for generating responses.

You can refer to [sample config](../deploy/config.yaml) to see the structure.

#### Milvus Configuration
`Milvus` serves as a vector database for storing embeddings.

    url: Configure the HTTP URI where the Milvus server is hosted.

#### Triton Configuration
LLM Inference server hosts the Large Language Model (LLM) with triton backend.

    server_url: Specify the url of the LLM Inference Server.

    model_name: Provide the name of the model hosted on the Triton server.
    Note: Changing the value of this field may need code changes.

#### Text Splitter Configuration
This section covers the settings for the Text Splitter component.

    chunk_size: Define the size at which text should be split before being converted into embeddings.

    chunk_overlap: Specify the overlap between two consecutive text chunks to prevent loss of context.

#### Embeddings Configuration
The Embeddings section contains information required for generating embeddings.

    model_name: Indicate the name of the model used to generate embeddings.
    Note: Note that this may also necessitate changes in the model's dimensions, which can be adjusted in the chain_server/utils.py file.

#### Prompts Configuration
Customize prompts used for generating responses.

    chat_template: The chat prompt template guides the model to generate responses for queries.
    rag_template: The RAG prompt Template instructs the model to generate responses while leveraging a knowledge base.

You set path to use this config file to be used by chain server using enviornment variable `APP_CONFIG_FILE`. You can do the same in [compose.env](../deploy/compose.env) and source the file.

### Configuring docker compose file
In this section, we will look into the environment variables and parameters that can be configured within the [Docker Compose](../deploy/docker-compose.yaml) YAML file. Our system comprises multiple microservices that interact harmoniously to generate responses. These microservices include LLM Inference Server, Jupyter Server, Milvus, Query/chain server, and Frontend.

#### Triton Configuration
The LLM Inference Server is used for hosting the Large Language Model (LLM) with triton backend. You can configure the model information using the [compose.env](../deploy/compose.env) file or by setting the corresponding environment variables. Here is a list of environment variables utilized by the llm inference server:

    MODEL_DIRECTORY: Specifies the path to the model directory where model checkpoints are stored.
    MODEL_ARCHITECTURE: Defines the architecture of the model used for deployment.
    MODEL_MAX_INPUT_LENGTH: Maximum allowed input length, with a default value of 3000.
    MODEL_MAX_OUTPUT_LENGTH: Maximum allowed output lenght, with a default of 512.
    INFERENCE_GPU_COUNT: Specifies the GPUs to be used by Triton for model deployment, with the default setting being "all."

#### Jupyter Server
This server hosts jupyter lab server. This contains notebook explaining the flow of chain server.

#### Milvus
Milvus serves as a GPU-accelerated vector store database, where we store embeddings generated by the knowledge base.

#### Query/Chain Server
The Query service is the core component responsible for interacting with the llm inference server and the Milvus server to obtain responses. The environment variables utilized by this container are described as follows:

    APP_MILVUS_URL: Specifies the URL where the Milvus server is hosted.
    APP_TRITON_SERVERURL: Specifies the URL where the Triton server is hosted.
    APP_TRITON_MODELNAME: The model name used by the Triton server.
    APP_CONFIG_FILE: Provides the path to the configuration file used by the Chain Server or this container. Defaults to /dev/null

#### Frontend
The Frontend component is the UI server that interacts with the Query/Chain Server to retrieve responses and provide UI interface to ingest documents. The following environment variables are used by the frontend:

    APP_SERVERURL: Indicates the URL where the Query/Chain Server is hosted.
    APP_SERVERPORT: Specifies the port on which the Query/Chain Server operates.
    APP_MODELNAME: Name of the Large Language Model utilized for deployment. This information is for display purposes only and does not affect the inference process.
