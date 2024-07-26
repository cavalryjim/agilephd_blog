+++
title = 'Building an LLM Application with Langchain (revisited)'
tags = ["python", "AI", "langchain"]
date = 2024-07-25
+++

This is updated code from an [earlier post about Langchain](https://blog.agilephd.com/posts/llm_langchain/).

The code is being updated for three main reasons:
- Some Windows users were having issues using Chroma for a vectorstore.
- I wanted to pass arguments to the script.
- The script needed more structure.

### Vectorstore

In the earlier version, I used Chroma for storing the vectors.  I liked this solution because it was easy and stored the results in a SQLite database.  Unfortunately, one, or more, students using Windows had an issue with Chroma that we tried to overcome but decided to try another solution.

While there are several storage options, I decided to use [Facebook AI Similarity Search (FAISS)](https://python.langchain.com/v0.2/docs/integrations/vectorstores/faiss/).  One of the main attributes of FAISS is that it is an in-memory vectorstore which, to me, reduces the complexity of installing any additional software such as Postgres.  The refactor for this was relatively simple with an additional import, removing a couple lines of code, and changing the word `Chroma` to `FAISS`.

### Passing Arguments

When demoing the previous version of this code, I was immediately challenged with "How can we pass arguments?".  First, I gave a psuedo explanation mentioning Flask & Heroku or Django & Elastic Beanstalk.  Next, I added `import sys` and `print(sys.argv)` to the code.  If you have never used `sys.argv`, it provides a list object containing the script name and any other objects that happen to be part of the python call to the script.

### Adding Structure

When writing code, things can quickly get out of hand.  Structured code is always preferred over unstructured code.  Structured code is much easier to maintain, test, use, and maintain...did I say "maintain"?  Unstructured code is fine for doing exploritory research or something agilist might call a [spike](https://scaledagileframework.com/spikes/) but any code that will persist should be kept tidy and dry.  With a new structured version, I could even test portions of the script without getting charged by OpenAI!

### Udated Version

Here is the updated script trying to accomplish stated objects:

```python
import os
import sys
from dotenv import load_dotenv
from langchain_community.document_loaders import PyPDFLoader
from langchain.text_splitter import CharacterTextSplitter
from langchain_openai import OpenAIEmbeddings
from langchain_community.vectorstores import FAISS
from langchain.chains import RetrievalQA
from langchain_openai import OpenAI
load_dotenv()


def main():
    question = sys.argv[1]
    pdf_name = sys.argv[2]
    api_key = os.getenv("OPENAI_API_KEY")

    text = extract_data(pdf_name)
    docs = split_text(text)

    docstorage = vectorize_and_store(docs, api_key)
    response = answer_question(question, api_key, docstorage)

    print(response['result'])
    # return response

def extract_data(pdf_name):
    loader = PyPDFLoader(pdf_name)
    data = loader.load()
    policy_text = ""
    for doc in data:
        if isinstance(doc, dict) and 'text' in doc:
            policy_text += doc['text']
        elif isinstance(doc, str):
            policy_text += doc
        else:
            policy_text += repr(doc)
    return policy_text

def split_text(text):
    ct_splitter = CharacterTextSplitter(separator='.', chunk_size=1000, chunk_overlap=200)
    docs = ct_splitter.split_text(text)
    return docs

def vectorize_and_store(docs, api_key):
    embedding_function = OpenAIEmbeddings(openai_api_key=api_key)
    docstorage = FAISS.from_texts(docs, embedding_function)
    return docstorage

def answer_question(question, api_key, docstorage):
    llm=OpenAI(model_name="gpt-3.5-turbo-instruct", openai_api_key=api_key)
    qa = RetrievalQA.from_chain_type(llm=llm, chain_type="stuff", retriever=docstorage.as_retriever())          
    response = qa.invoke(question)
    return response

main()

```

### Running the Script

Notice that we can now pass two additional strings: 1) the question to be answered and 2) the name of the pdf to pull the content. 

```
$ python llm_faiss_vectorstore.py "Can I receive a blanket approval for outside work?" "pm-11.pdf"
```
```
 No, blanket approvals for outside employment will not be granted. Approval for outside work must be obtained through normal administrative channels by the Chancellor or a designated campus administrative officer. 
 ```

