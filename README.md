# Conversational RAG with PDF Uploads and Chat History

A Streamlit application that lets users upload PDF documents and ask
questions about their content. It uses Retrieval-Augmented Generation
(RAG), a Chroma vector database, Hugging Face embeddings, and a
Groq-hosted language model to generate concise, context-grounded answers
while maintaining chat history.

## Application Screenshot

Add a screenshot of your running application at
`screenshots/app-screenshot.png`. The image will then appear here on
GitHub.

![Conversational RAG application
screenshot](screenshots/app-screenshot.png)

> **To add your screenshot:** create a `screenshots` folder in the
> repository, save your screenshot as `app-screenshot.png`, and commit
> both the image and this README. You can use a different filename, but
> update the image path above to match it.

## Features

-   Upload one or more PDF files.
-   Extract text from PDFs using `PyPDFLoader`.
-   Split document text into chunks with
    `RecursiveCharacterTextSplitter`.
-   Create vector embeddings using the Hugging Face `all-MiniLM-L6-v2`
    model.
-   Store document chunks in Chroma.
-   Retrieve relevant chunks to help answer questions about the PDFs.
-   Use a history-aware retriever to rewrite follow-up questions using
    previous conversation context.
-   Maintain in-memory chat history by session ID.
-   Generate concise answers using a Groq-hosted language model.

## Architecture

``` text
PDF Upload
    ↓
PyPDFLoader
    ↓
PDF Documents
    ↓
RecursiveCharacterTextSplitter
    ↓
Text Chunks
    ↓
Hugging Face Embeddings
    ↓
Chroma Vector Database
    ↓
History-Aware Retriever ← Chat History + Current Question
    ↓
Standalone / Contextualized Question
    ↓
Relevant PDF Chunks
    ↓
Question-Answer Chain + LLM
    ↓
Answer
    ↓
Chat History
```

## Tech Stack

-   **Python** --- application logic
-   **Streamlit** --- user interface
-   **LangChain** --- RAG chains and conversation history
-   **PyPDFLoader** --- PDF text extraction
-   **RecursiveCharacterTextSplitter** --- chunking
-   **Hugging Face Sentence Transformers** --- embeddings
-   **Chroma** --- vector storage and similarity retrieval
-   **Groq** --- language-model inference
-   **python-dotenv** --- environment-variable loading

## Getting Started

### 1. Clone the repository

``` bash
git clone https://github.com/YOUR-USERNAME/YOUR-REPOSITORY.git
cd YOUR-REPOSITORY
```

Replace the repository URL and folder name with your actual GitHub
repository details.

### 2. Create and activate a virtual environment

**Windows (PowerShell):**

``` powershell
python -m venv venv
.\venv\Scripts\Activate.ps1
```

**Windows (Command Prompt):**

``` bat
python -m venv venv
venv\Scripts\activate
```

### 3. Install dependencies

Create a `requirements.txt` file containing the packages used by the
application, then run:

``` bash
python -m pip install --upgrade pip
pip install -r requirements.txt
```

Packages used by this project include:

``` text
streamlit
langchain-classic
langchain-chroma
langchain-community
langchain-core
langchain-groq
langchain-huggingface
langchain-text-splitters
sentence-transformers
pypdf
python-dotenv
```

Use compatible versions for your Python environment and test the
complete dependency set before deployment.

### 4. Configure environment variables

The application asks for a Groq API key in the Streamlit interface.
Enter your key there when prompted.

If you use a `.env` file for other environment variables, keep it
private. Never commit API keys, access tokens, or other secrets to
GitHub. Add `.env` to `.gitignore`.

The code loads `HF_TOKEN` from the environment, although the public
`all-MiniLM-L6-v2` embedding model may not require a Hugging Face token.

### 5. Run the application

``` bash
streamlit run app.py
```

Streamlit will show a local URL in the terminal, usually
`http://localhost:8501`. Open it in your browser, enter your Groq API
key, upload PDF files, and ask questions about their contents.

## How It Works

1.  **Upload:** The user selects one or more PDF files.
2.  **Extract:** `PyPDFLoader` reads the PDFs into LangChain document
    objects.
3.  **Chunk:** The text splitter divides documents into smaller
    overlapping sections.
4.  **Embed:** The Hugging Face embedding model converts chunks into
    vectors.
5.  **Index:** Chroma stores the vectors and associated document text.
6.  **Contextualize:** The history-aware retriever uses chat history to
    interpret follow-up questions.
7.  **Retrieve:** Chroma returns document chunks relevant to the
    contextualized question.
8.  **Generate:** The LLM answers using the retrieved context.
9.  **Remember:** `RunnableWithMessageHistory` manages the conversation
    history for the session.

## Important Notes

-   **Chat history is in memory:** the current implementation stores
    history in `st.session_state`, so it is not durable storage across
    server restarts or separate application instances.
-   **PDF processing:** the current code processes uploaded files during
    Streamlit reruns. For a larger application, cache or manage document
    processing so the same PDFs are not embedded repeatedly.
-   **Temporary files:** the current implementation writes each upload
    to `temp.pdf`. Use unique temporary filenames for multiple files and
    remove temporary files when processing is complete.
-   **Vector database persistence:** `Chroma.from_documents(...)`
    creates a vector store for the current processing flow. Configure a
    persistent Chroma directory if you need to reuse the index between
    runs.
-   **Session handling:** ensure the history function uses the session
    ID passed to it, and keep each user's data isolated in a deployed
    multi-user application.
-   **Privacy:** upload only documents you are authorized to process.
    Review your model provider's data-handling terms before using
    sensitive PDFs.

## Future Improvements

-   Add source citations and page numbers to answers.
-   Display chat messages in a conversational interface.
-   Support document removal and re-indexing.
-   Add persistent vector storage and durable conversation history.
-   Add file-size limits, upload validation, and clearer error handling.
-   Add tests, logging, and deployment configuration.

## License

Choose and add a license before distributing this project publicly.
