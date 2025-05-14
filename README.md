# Restaurant Review Semantic Search

## Description

This project implements a semantic search system for restaurant reviews. It leverages vector embeddings to understand the contextual meaning of reviews, allowing users to search for reviews based on similarity in content rather than just keyword matching. The system reads reviews from a CSV file, generates embeddings for them, stores these embeddings in a Chroma vector database, and provides a retriever to find the most relevant reviews for a given query.

## Key Features

*   **Semantic Search**: Finds reviews based on contextual similarity.
*   **Vector Embeddings**: Uses `OllamaEmbeddings` with the `mxbai-embed-large` model to convert text into numerical representations.
*   **Vector Store**: Utilizes `Chroma` for efficient storage and retrieval of vector embeddings.
*   **Persistent Storage**: Saves the vector database locally in `./chrome_langchain_db` for reuse.
*   **Data Ingestion**: Loads review data from a `realistic_restaurant_reviews.csv` file.

## Technologies Used

*   Python 3.x
*   Langchain (`langchain_ollama`, `langchain_chroma`, `langchain_core`)
*   Pandas
*   Ollama (with `mxbai-embed-large` model)

## Prerequisites

1.  **Python**: Ensure Python 3.x is installed.
2.  **Ollama**:
    *   Install Ollama from [https://ollama.com/](https://ollama.com/).
    *   Pull the `mxbai-embed-large` model:
        ```bash
        ollama pull mxbai-embed-large
        ```
3.  **CSV Data File**: A CSV file named `realistic_restaurant_reviews.csv` must be present in the root directory. It should contain at least the following columns: `Title`, `Review`, `Rating`, and `Date`.

## Setup

1.  **Clone the repository (if applicable) or download the project files.**
2.  **Install Python dependencies**:
    Create a `requirements.txt` file with the following content:
    ```
    langchain
    langchain-ollama
    langchain-chroma
    pandas
    ```
    Then, install the packages:
    ```bash
    pip install -r requirements.txt
    ```
3.  **Ensure Ollama is running** and the `mxbai-embed-large` model is available.

## Usage

1.  **Place your `realistic_restaurant_reviews.csv` file in the project's root directory.**
    The CSV should have columns: `Title`, `Review`, `Rating`, `Date`.
2.  **Run the script**:
    ```bash
    python vector.py
    ```
    *   On the first run, the script will process the CSV file, generate embeddings for each review, and store them in the Chroma database located at `./chrome_langchain_db`. This might take some time depending on the number of reviews.
    *   Subsequent runs will load the existing database, skipping the embedding generation step unless the database directory is removed.
3.  **Integrate the retriever**:
    The `vector.py` script sets up a `retriever` object. You can use this retriever in your application code to perform semantic searches. For example:
    ```python
    # Assuming 'retriever' is initialized as in vector.py
    query = "restaurants with great ambiance for a date night"
    relevant_reviews = retriever.invoke(query)
    for review in relevant_reviews:
        print(f"Title: {review.page_content.split(' ')[0]}") # A bit simplistic, better to store title separately if possible
        print(f"Review Snippet: {' '.join(review.page_content.split(' ')[1:50])}...")
        print(f"Rating: {review.metadata['rating']}")
        print(f"Date: {review.metadata['date']}")
        print("-" * 20)
    ```

## Data

The project expects a CSV file named `realistic_restaurant_reviews.csv` in the root directory. The file should contain the following columns:

*   `Title`: The title of the review.
*   `Review`: The full text of the review.
*   `Rating`: The rating given in the review (e.g., 1-5).
*   `Date`: The date the review was posted.

The `Title` and `Review` fields are combined to create the content for vector embedding. `Rating` and `Date` are stored as metadata.

## Vector Store

The Chroma vector store is persisted in the `./chrome_langchain_db` directory. If this directory exists, the script will attempt to load the existing database. If it does not exist, the script will create it and populate it with embeddings from the CSV file.
The collection name used is `restaurant_reviews`. 