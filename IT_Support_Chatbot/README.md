# **IT Support Chatbot**

* In this document will guide you through the process of creating a unique RAG (Retrieval Augmented Generation) chatbot. Unlike typical chatbots, this one is specifically designed for handling queries related to very specific topics or articles. By utilising the RAG technique, our chatbot can generate̥ responses to complex queries that standard chatbots might find challenging.
  
* Let’s narrow our focus to a topic that typically falls outside the capabilities of a generic chatbot. We are delving into a niche area, such as providing horoscope predictions for individuals born under the Sagittarius sign in the year 2024. Imagine having a chatbot that can answer questions from Sagittarius individuals about what the year 2024 holds for them. While this may seem highly specific, it serves as an example of the type of specialised chatbot we aim to create. With our target set on a fortune-telling chatbot for Sagittarius folks, let’s dive into the tools and techniques we’ll employ to create this bot.

## **What is LLM and RAG**

* With the rise of generative AI, “LLM” has turned into a key term for many developers, particularly those who are interested in or are currently working in the AI field. But what exactly is LLM?

### **LLM**

* Large Language Models (LLMs) form a specific category within the broader field of Natural Language Processing (NLP). These models specialise in generating text by analysing and processing vast datasets. Their notable strength lies in their capacity to comprehend and generate language in a broad and versatile manner. LLMs use something called the transformer model. The transformer model is a neural network that learns context and semantic meaning in sequential data like text.

* A well-known example of a chatbot using LLM technology is ChatGPT, which incorporates the GPT-3.5 and GPT-4 models.

* As for this blog, we will be using a model from MistralAI called Mixtral8x7b. This model is capable of matching or surpassing the performance of Llama 70B and GPT-3.5 and it is available for free use.

#### **Problems with Generic LLM**

* When it comes to Large Language Models (LLMs), there are two possible scenarios involving topics that they may be less knowledgeable about.

* Firstly, the model may straightforwardly admit that it lacks information on a particular subject because it hasn’t been trained on that specific data.

* Secondly, there’s the potential for what’s known as “hallucination”, where the model generates responses that are inaccurate or misleading due to its lack of specialised knowledge. This is because generic LLMs are not trained with detailed information in certain areas, such as specific legal rules or medical data, which typically fall outside the scope of a general-purpose LLM’s training data.

* To address this issue, one method is to fine-tune the model by adding specific data to it and tailor it for particular needs. However, this blog will focus on a simpler approach called RAG, or Retrieval-Augmented Generation.

### **Introducing RAG**

* RAG, short for Retrieval-Augmented Generation, is a way to boost what Large Language Models (LLMs) know by adding more data to them. It’s made up of two main components:

	*  **Indexing:** This is about taking in data from various sources and organising it in a way that the system can easily use, which is indexing.

	*  **Retrieval and Generation:** To delve deeper into how RAG functions, let’s understand its two primary processes: retrieval and generation. The retrieval component acts like a focused search engine, scanning a database of indexed information to find relevant data related to the user’s query. This data is then fed into the Large Language Model. The model uses this context, along with its trained knowledge base, to generate a response that’s more informed and accurate. This synergistic process allows RAG to provide more precise answers by supplementing its extensive but generalised training with specific, targeted information.

* In simpler terms, RAG helps LLMs to be more knowledgeable by pulling in extra information when needed to answer questions better. This is how the architecture of the chatbot will look:

![Retrieval and Generation](Images/arch.png)
 
## Useful tools

### **LangChain**

![LangChain](https://miro.medium.com/v2/resize:fit:786/format:webp/0*Cmji4vMthUO1uH_I)

* LangChain is an open-source framework written in Python and JavaScript, designed for building applications centred around language models. LangChain provides components that allow non-AI experts to be able to implement existing AI language models into their applications. This framework is versatile and supports various functions such as text summarisation, tagging, and others. However, this blog will specifically concentrate on the creation of RAG creation.

### **AWS Bedrock**

* AWS Bedrock is a fully managed service that makes it easy to build and scale generative AI applications. It provides access to a variety of foundation models from leading AI companies, allowing developers to quickly integrate advanced AI capabilities into their applications without needing extensive machine learning expertise.

### **FAISS: Vector database**

![FAISS](Images/faisspng.png)

* Before we delve into FAISS, let’s clarify what a vector database is. A vector database, such as FAISS, stores data in the form of vectors, which are an arrays of numbers — e.g. [0.1, 3.21, -1.3, 9.2, …]. This approach allows for efficient similarity searches, as it groups similar data and enables models or applications to retrieve relevant information effectively.

* FAISS (Facebook AI Similarity Search) is a library that allows developers to quickly search for embeddings of multimedia documents that are similar to each other. It solves limitations of traditional query search engines that are optimized for hash-based searches, and provides more scalable similarity search functions.

## **Let’s build something!**

### **Setting up**

* Before implementing the code, make sure you have set up the following:

**AWS account setup**

1. To create an AWS account, you can go to this link: https://aws.amazon.com/ and sign up.

2. After signing up, go to the AWS Management Console and create an IAM user with programmatic access. Attach the necessary policies to this user to allow access to the services you will be using (e.g., S3, Lambda).

3. Once the user is created, you will receive an Access Key ID and Secret Access Key. Make sure to keep these credentials safe and do not share them with anyone.

**Project structure and environment**

* After completing the account setup, you can create a directory called “Chatbot”. Inside the Chatbot directory, create a file called `.env`. The context inside `.env` should look like the code snippet below (replace xxx with your AWS access tokens). We will be using this .env file in later steps.

```python
# .env file
AWS_ACCESS_KEY_ID=xxxxx
AWS_SECRET_ACCESS_KEY=xxxxx
AWS_DEFAULT_REGION=xxxxx
```

* Finally, create a file called `main.py`, and create an empty class called Chatbot inside it. This class is going to be called when we implement the frontend UI. For now, just create the class without adding any more code to it.

![main](https://miro.medium.com/v2/resize:fit:786/format:webp/0*e9Sc_PDQdXtmTlfL)

### **Importing dependencies**

* Here is the list of dependencies you should install prior to the implementation:

  * **langchain :** To be able to import components and chain from the langchain library.

  * **streamlit :** Used for creating UI pages with Python code.
  
  * **python-dotenv :**  To be able to use the environment variable stored in .env file.

* You can store this list inside requirements.txt as shown below (dependencies versions are optional):

``` python
langchain==0.1.6
langchain-community==0.0.19
langchain-core==0.1.23
python-dotenv==1.0.0
streamlit==1.29.0
faiss-cpu
langchain_aws
```

* After you get your requirements.txt inside your project directory, install the dependencies using this command:

```python
pip install -r requirements.txt
```

### **Data indexing**

* Before we start indexing, we need to first have the data that our model will use to answer questions. In this case, we need a text file or a pdf about _It support_ (pre defined faq's), as our chatbot will be answering questions related to this.

  ```txt
  ## it_sector.txt
  ## Troubleshooting Network Connectivity Issues
  
  Q: How do I troubleshoot network connectivity issues?
  A: Start by checking if your device is properly connected to the network. Restart your router and device, and ensure there are no physical   obstructions. Check for IP conflicts or DNS issues as well.
  
  ## Software Installation and Configuration
  
  Q: How do I install and configure software on my system?
  A: Download the software from the official website or trusted source. Follow the installation wizard instructions. Configure settings as per   your requirements, ensuring compatibility with your operating system.
  
  ## System Performance Optimization
  
  Q: My computer is running slow. How can I optimize its performance?
  A: Check for background processes consuming resources. Update your operating system and drivers. Remove unnecessary startup programs and perform   disk cleanup. Consider upgrading hardware if necessary.
  
  ## Data Backup and Recovery
  
  Q: How can I back up and recover my data?
  A: Use reliable backup software to create regular backups of important files. Store backups securely, either on external drives or cloud   services. For data recovery, use the backup copies to restore lost or corrupted files.
  
  ## Email Configuration and Troubleshooting
  
  Q: I'm having trouble setting up my email account. What should I do?
  A: Verify your email settings, including server addresses and ports. Check your internet connection and antivirus/firewall settings, as they can   sometimes block email services. Contact your email provider for specific configuration details.
  
  ## Security and Virus Protection
  
  Q: How can I protect my computer from viruses and malware?
  A: Install reputable antivirus software and keep it updated. Avoid clicking on suspicious links or downloading files from unknown sources.   Regularly scan your system for malware and perform security updates.
  
  ## Hardware Issues Diagnosis
  
  Q: My printer is not working. How can I diagnose and fix hardware issues?
  A: Check connections and power supply. Ensure drivers are installed correctly and up to date. Clear any paper jams or printer queue issues. Test   with different devices if possible to isolate the problem.
  
  ## VPN Setup and Configuration
  
  Q: How do I set up and configure a VPN on my system?
  A: Choose a VPN provider and sign up for a plan. Download and install the VPN client software. Follow instructions to configure the VPN   settings, including server selection and authentication.
  
  ## Mobile Device Support
  
  Q: I'm experiencing issues with my smartphone/tablet. How can I troubleshoot them?
  A: Restart your device and check for software updates. Clear cache and temporary files. Verify network connectivity and adjust settings for   optimal performance. Contact device support or visit an authorized service center if problems persist.
  ```

* Having gathered the textual content for our RAG application, it’s time to move on to the data indexing phase. Initially, we’ll break down the text files into manageable segments. This is done by deploying a text splitter where we define the dimensions of these segments. In this example, we’re setting the chunk_size to 1000 and chunk_overlap to 4.

* Next, we introduce an embedding utility - specifically the HuggingFaceEmbedding tool. This will be instrumental in embedding our text segments.

  ```python
  from langchain.text_splitter import CharacterTextSplitter
  from langchain_aws import BedrockEmbeddings
  file_path = './it_sector.txt'
    with open(file_path, 'r', encoding='utf-8') as file:
          text = file.read()
    text_splitter = CharacterTextSplitter(chunk_size=1000, chunk_overlap=4)
    docs = text_splitter.split_text(text)

    embeddings = BedrockEmbeddings(
        model_id="amazon.titan-embed-text-v2:0"
    )
  ```

* Following the embedding process, the next step involves depositing these embedded text fragments into our vector database, FAISS, for efficient storage and retrieval.

  ```python
  from langchain.vectorstores import FAISS
  
  vectorstore = FAISS.from_texts(texts=docs, embedding=embeddings)
  ```

### **Model setup**

* Now that we have our embedded texts on the vector database, let’s move on to the model setup part. Of course, we don’t want to create, train, and deploy the LLM from scratch locally. This is why we are using HuggingFaceHub, which is a platform we can connect and call the model without having to deploy it on our machine.

* With AWS Bedrock, we can easily access and use various pre-trained models, including those from MistralAI. For our chatbot, we will be using the `mistral.mistral-large-2402-v1:0` model, which is a powerful language model that can handle complex queries and generate human-like responses.

  * **temperature:** which controls the randomness in the output

```python
from langchain_aws import ChatBedrock  
  
llm = ChatBedrock(
    model_id="mistral.mistral-large-2402-v1:0",
    model_kwargs={"temperature": 0.5}
)
```

### **Prompt engineering**

* For LLM to answer our question, we need to define a prompt that will contain all of the necessary information. This allows us to customise the model to fit our needs. In our case, we will tell the model to be a IT support chatbot and answer only relevant questions. Additionally, we need to pass {context} and {question} to the prompt. These values will be replaced with the data chunk we retrieve from our vector database for {context} and the question the user asked for the {question}.

* With this template created, we then define the PromptTemplate object taking our template and input variables (context and questions) as a parameter.

  ```python
  from langchain.prompts import PromptTemplate 
  
  template = """
  You are an IT support chatbot. If the user greets you, respond politely and let them know you are here to assist with IT support questions.
  If they ask a question about the IT sector, use the following context to answer the question.
  If the question is out of context, respond politely that you are an IT support chatbot and can only assist with IT-related questions.
  If you don't know the answer, just say you don't know.
  You should respond with short and concise answers, no longer than 2 sentences.
  
  Context: {context}
  Question: {question}
  Answer:
  """
  
  prompt = PromptTemplate(template=template, input_variables=["context", "question"])
  ```

### **Chaining it all together**

* Now that we have:

  * FAISS index object(  `vectorstore`  )
  * PromptTemplate (  `prompt`  )
  * Model (  `llm`  )

* We are ready to chain them together. The process starts with  `vectorstore`  pulling relevant documents to provide context. Then, the query goes through unchanged using  `RunnablePassthrough`. Next, a  `prompt`  step refines or modifies the query before it's processed by our model,  `llm`. Finally, the response from the model is turned into text with  `StrOutputParser`.


  ```python
  from langchain.schema.runnable import RunnablePassthrough
  from langchain.schema.output_parser import StrOutputParser
  
  rag_chain = (
    {"context": vectorstore.as_retriever(),  "question": RunnablePassthrough()} 
    | prompt 
    | llm
    | StrOutputParser() 
  )
  ```

## Finalising model

* Now that we have our rag_chain ready, let’s put it into our previously created Chatbot() class. Simply put all of the code we have added into the Chatbot class.

  ```python
  from langchain.text_splitter import CharacterTextSplitter
  from langchain_aws import BedrockEmbeddings, ChatBedrock
  from langchain.vectorstores import FAISS
  from langchain.schema.runnable import RunnablePassthrough
  from langchain.schema.output_parser import StrOutputParser
  from langchain.prompts import PromptTemplate 
  from dotenv import load_dotenv
  import os

  class ChatBot:
      def __init__(self):
          load_dotenv()

          file_path = './it_sector.txt'
          with open(file_path, 'r', encoding='utf-8') as file:
              text = file.read()

          text_splitter = CharacterTextSplitter(chunk_size=2000, chunk_overlap=4)
          docs = text_splitter.split_text(text)

          embeddings = BedrockEmbeddings(
              model_id="amazon.titan-embed-text-v2:0",
          )
          vectorstore = FAISS.from_texts(texts=docs, embedding=embeddings)

          llm = ChatBedrock(
              model_id="mistral.mistral-large-2402-v1:0",
              model_kwargs={"temperature": 0.5}
          )

          template = """
          You are an IT support chatbot. If the user greets you, respond politely and let them know you are here to assist with IT support  questions.
          If they ask a question about the IT sector, use the following context to answer the question.
          If the question is out of context, respond politely that you are an IT support chatbot and can only assist with IT-related questions.
          If you don't know the answer, just say you don't know.
          You should respond with short and concise answers, no longer than 2 sentences.

          Context: {context}
          Question: {question}
          Answer:
          """

          prompt = PromptTemplate(template=template, input_variables=["context", "question"])

          self.rag_chain = (
              {"context": vectorstore.as_retriever(), "question": RunnablePassthrough()}
              | prompt
              | llm
              | StrOutputParser()
          )
  ```

* Now that we got the Chatbot ready, we can test it out. In  `main.py`  , add the following code at the end (this is just for testing purposes and should be removed this later):

  ```python
  # Outside ChatBot() class  
  bot = ChatBot()  
  input = input("Ask me anything: ")  
  result = bot.rag_chain.invoke(input)  
  print(result)
  ```

### **Streamlit frontend**

* Here is how I implement the model with Streamlit frontend. As this blog is about the RAG LLM chatbot, I won’t go deep into implementing the frontend side, but here is the Streamlit template I use for creating Chat UI and some basic functions I use for calling the model. Make sure to use this code in another file — in this example, this code belongs to  `streamlit.py` .

  ```python
  from main import ChatBot
  import streamlit as st
  import re

  bot = ChatBot()

  st.title('IT Tech Support')

  # Function for generating LLM response
  def generate_response(input):
      result = bot.rag_chain.invoke(input)
      return result

  # Store LLM generated responses
  if "messages" not in st.session_state.keys():
      st.session_state.messages = [{"role": "assistant", "content": "Welcome to IT Tech Support"}]

  # Display chat messages
  for message in st.session_state.messages:
      with st.chat_message(message["role"]):
          st.write(message["content"])

  # User-provided prompt
  if input := st.chat_input():
      st.session_state.messages.append({"role": "user", "content": input})
      with st.chat_message("user"):
          st.write(input)

  # Generate a new response if last message is not from assistant
  if st.session_state.messages[-1]["role"] != "assistant":
      with st.chat_message("assistant"):
          with st.spinner("Analyzing your question..."):
              response = generate_response(input)
              print(response)  # Extracting the answer
              st.write(response)  # Displaying the extracted answer

      message = {"role": "assistant", "content": response}
      st.session_state.messages.append(message)
  ```

* After completing all the steps, you should be able to create something like this by running:

`streamlit run streamlit.py`

## **Conclusion**

As you can see, the model isn’t perfect and there are still many things to add to and improve the model in future. However, this will hopefully give you a basic understanding of how to create an RAG chatbot and how vector databases work.