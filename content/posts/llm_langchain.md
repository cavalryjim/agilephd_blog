+++
title = 'Building an LLM Application with Langchain'
tags = ["python", "AI", "langchain"]
date = 2024-07-22
+++

Primarily developed in Python, LangChain is a robust framework designed to simplify the development of applications that utilize large language models (LLMs). It enables developers to connect these models with various data sources and external APIs, facilitating the integration of advanced natural language processing capabilities into their applications. LangChain provides tools for constructing LLM-powered workflows, handling memory and state management, and ensuring scalability and efficiency. By offering a streamlined interface for working with LLMs, LangChain empowers developers to create sophisticated AI-driven solutions in areas such as chatbots, data analysis, content generation, and more, without needing extensive expertise in machine learning or AI model development. 

Let's dive into building an LLM-based application!

### Getting Started

In this example, we will create a foundational LLM-based application that ingests information from a PDF file and enables interactive questioning about its content.

You can find the code for this post on [GitHub](https://github.com/cavalryjim/langchain_llm_app).  Depending on your experience with LangChain, you might need to run a few `pip install` commands or simply use my `requirements.txt` file. Feel free to use a PDF file of your choice; I have also included a policy memo, PM-11, from the university where I teach.

The smart play would also be to create a virtual envrionment for this project.  I like pipenv but you are welcome to use something different.

```
$ pipenv shell
$ pip install -r requirements.txt
```

Use whatever manner you want to edit & run the code.  For projects like this, I usually boot up a session using Jupyter Notebooks.

```
$ jupyter notebook
```

First, we need to import the necessary libraries and load environment variables:

```python
# Import necessary libraries
import os
from dotenv import load_dotenv
from langchain_community.document_loaders import PyPDFLoader
from langchain.text_splitter import CharacterTextSplitter
from langchain_openai import OpenAIEmbeddings
from langchain_community.vectorstores import Chroma
from langchain.chains import RetrievalQA
from langchain.llms import OpenAI
from langchain_openai import OpenAI

load_dotenv()
```

In the block above, we're importing essential modules for handling PDFs, text processing, embeddings, vector storage, and the language model. The `load_dotenv` function loads environment variables from a `.env` file, which is useful for securely managing API keys.

### Extract Contents from PDF

Next, we'll load the PDF and extract its contents:

```python
loader = PyPDFLoader("pm-11.pdf")
data = loader.load()

policy_text = ""
for doc in data:
    if isinstance(doc, dict) and 'text' in doc:
        policy_text += doc['text']
    elif isinstance(doc, str):
        policy_text += doc
    else:
        policy_text += repr(doc)
```

Here, we use `PyPDFLoader` to load the PDF file. We then iterate through the loaded data, concatenating text from the document into a single string. This step ensures we have all the text content in a manageable format.

### Preprocess the Text

Now, we preprocess the extracted text and prepare it for further processing:

```python
ct_splitter = CharacterTextSplitter(separator='.', chunk_size=1000, chunk_overlap=200)
docs = ct_splitter.split_text(policy_text)
```

In this section, `CharacterTextSplitter` is used to split the text into smaller chunks. By specifying a separator, chunk size, and overlap, we ensure that the text is divided into manageable pieces, which helps in handling large documents effectively.

### Vectorize and Store in DB

Next, we vectorize the text chunks and store them in a database for efficient retrieval:

```python
api_key = os.getenv("OPENAI_API_KEY")
embedding_function = OpenAIEmbeddings(openai_api_key=api_key)

vectordb = Chroma(persist_directory='LSU', embedding_function=embedding_function)

docstorage = Chroma.from_texts(docs, embedding_function)
```

Here, we retrieve the OpenAI API key from environment variables and initialize the `OpenAIEmbeddings` function to convert text into embeddings. We then use `Chroma` to create a vector database, storing the embeddings of our text chunks, making it easier to retrieve relevant information later.

As a note, Chroma creates a SQLite database file inside your project in a subfolder designated by the `persist_directory` input.  In my case, it would be a subfolder called "LSU".

### Fine-Tune a Language Model

We then configure the language model to interact with our vector database:

```python
llm=OpenAI(model_name="gpt-3.5-turbo-instruct", openai_api_key=api_key)

qa = RetrievalQA.from_chain_type(llm=llm, chain_type="stuff", retriever=docstorage.as_retriever())  
```

In this block, we initialize the OpenAI language model (`gpt-3.5-turbo-instruct`) and set up a retrieval-based question-answering system (`RetrievalQA`). The `docstorage.as_retriever()` method allows the model to fetch relevant information from our vector database.

### Query the LLM

Finally, we can query the language model and get answers based on the document content:

```python
question = "Can I get a blanket approval for work outside of LSU?"
response = qa.invoke(question)

print(response['result'])
```

```
No, blanket approvals for outside employment will not be granted.
```

Here, we pose a question to the language model, which retrieves relevant information from the vector database and provides a response. This demonstrates the application's ability to interactively answer questions based on the ingested PDF content.

By following these steps, you can harness the power of LangChain to build a versatile, LLM-powered application capable of interactive and dynamic querying of document contents.

