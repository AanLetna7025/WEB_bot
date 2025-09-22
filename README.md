# WEB_bot

#  The project automates the process of turning a website's content into a searchable knowledge base and then uses a powerful language model to answer questions about that content, ensuring the answers are grounded in the information scraped from the site.

This project is designed to create a question-answering system that can answer questions about the content of a specific website (in this case, "toobler.com").


Environment Setup: The initial cells install the necessary libraries like Playwright for web scraping, Langchain for building the Q&A pipeline, Sentence-Transformers for creating embeddings, FAISS for the vector store, and Google Generative AI for the language model. It also sets up access to your Google Drive to save the created knowledge base and configures access to the Gemini API.
Web Scraping (quick_scrape and fast_crawl):
quick_scrape is a utility function using Playwright to visit a single URL, wait for the page content (including JavaScript-rendered parts) to load, extract the main text content, and find internal links.
fast_crawl orchestrates the scraping process. It starts from a root URL and explores internal links in a breadth-first manner. It uses a visited set to ensure each unique page is scraped only once. It collects the scraped content as Langchain Document objects.
Knowledge Base Creation (create_gpu_vectorstore):
This function takes the scraped Document objects.
It uses a RecursiveCharacterTextSplitter to break down the long document content into smaller, manageable chunks.
It employs a HuggingFace embedding model (optimized for GPU if available) to convert these text chunks into numerical vector representations (embeddings).
It uses FAISS, a library for efficient similarity search, to create a vector store from these embeddings. This allows for fast retrieval of relevant text chunks based on a user's query.
Finally, it saves the created FAISS vector store to your Google Drive, so you can reuse it later without re-scraping.
Question Answering Setup (setup_qa):
This function loads the saved FAISS vector store from your Google Drive.
It initializes a Google Generative AI model (Gemini 2.5 Flash) to act as the question-answering engine.
It defines a ChatPromptTemplate that provides instructions to the language model, emphasizing that it should answer questions only based on the provided context from the vector store and avoid making up information.
It creates a Langchain chain that connects the prompt, the language model, and an output parser.
It returns an ask function.
Interactive Q&A (main execution and interactive loop):
The main asynchronous function runs the scraping and knowledge base creation steps.
After the knowledge base is ready, it calls setup_qa to get the ask function.
The code then enters an interactive loop where you can type questions related to the website content.
When you ask a question, the ask function uses the vector store to find the most relevant text chunks from the scraped website, provides these chunks as context to the Gemini model, and the model generates an answer based only on that context.
