# Java AI chatbot that uses your documents as context 

This app demonstrates how you can create a custom AI chatbot that can use your own documents to answer questions using RAG (retrieval augmented generation).
The chatbot uses [LangChain4j](https://github.com/langchain4j/langchain4j) and the OpenAI API to generate responses and [Vaadin](http://vaadin.com/) to create the user interface.

> [!IMPORTANT]
> Before you can use the application you need to:
> 1. Configure the documentation location
> 2. Configure either OpenAi or a local LLM

## 🛠️ Configuration

### Configuring documentation location

Update the `docs.location` property in `application.properties` to point to a folder with relevant documents. 
LangChain4j uses Apache Tika internally when processing the files, so most file types work.

### Chat responses

Chat responses can be provided through either Open AI or a local LLM.

#### Using Open AI

OpenAI gives you better quality answers but requires you to send data to a 3rd party.

To use OpenAI, get an [API key](https://platform.openai.com/api-keys) and configure it in `application.properties`. 
Optionally, you can also configure the model in the properties. 

#### Using a local LLM

Using a local model allows you to keep your data on your local computer, but the quality of answers will not be as good as with OpenAI.

Install [Ollama](https://ollama.com/) and the `llama3` model.
Comment out the OpenAI section of `application.properties` and uncomment the Ollama section.

### Embedding model

By default, the application uses a small and local embedding model (`dev.langchain4j.model.embedding.onnx.bgesmallenv15q.BgeSmallEnV15QuantizedEmbeddingModel`) this model leverages 384 dimensions.

#### Using OpenAI

You can opt to use an OpenAI based embedding model (`text-embedding-3-small`) leveraging 1536 dimensions. This **should** produce better quality embeddings.

### Embedding store (Vector DB)

By default, the application uses an in-memory embedding store. This is fine for demos and small amounts of data. 
If you need to store more documents, consider using any of the [embedding stores that LangChain4j supports](https://docs.langchain4j.dev/integrations/embedding-stores/).

#### Pinecone

To use Pinecone, use or set up an index according to the leveraged embedding model (with 384 or 1536 dimensions). Also, provide the following details in the `application.properties` file.

- [API key](https://www.pinecone.io/)
- index name
- set `ai.embedding-store` to `pinecone`

## ▶️ Running the application

The project is a standard Maven project. To run it from the command line,
type `mvnw` (Windows), or `./mvnw` (Mac & Linux), then open
http://localhost:8080 in your browser.

You can also import the project to your IDE of choice as you would with any
Maven project. Read more on [how to import Vaadin projects to different IDEs](https://vaadin.com/docs/latest/guide/step-by-step/importing) (Eclipse, IntelliJ IDEA, NetBeans, and VS Code).

## Running the Application Using Docker

Follow the steps below to run the application using Docker:

1. **Build the Project**: Clean and package the project using Maven.
    ```sh
    mvn clean package -Pproduction
    ```

2. **Build the Docker Image**: 
    ```sh
    docker build -t your-app-name:latest .
    ```

3. **Run the Docker Container**: Run the Docker container with the necessary environment variables.
    ```sh
    docker run -d -p 8080:8080 --name ai-app-container \
      -e OPENAI_API_KEY=your-openai-api-key \
      -e PINECONE_API_KEY=your-pinecone-api-key \
      -e LOCAL_DOC_DIRECTORY=/path/to/local/doc/directory \
      -e GITHUB_ACCESS_TOKEN=your-github-access-token \
      your-app-name:latest
    ```

Replace the placeholders with your actual values:
- `your-openai-api-key`: Your actual OpenAI API key.
- `your-pinecone-api-key`: Your actual Pinecone API key.
- `/path/to/local/doc/directory`: The path to your local documentation directory.
- `your-github-access-token`: Your actual GitHub access token.

### Import Pinecone embeddings

In case of using Pinecone, the following argument has to be provided to generate and import the document embeddings. As the index is available, we do not need to recreate it each time.

```
./mvnw spring-boot:run -Dspring-boot.run.arguments=--import-docs
```

