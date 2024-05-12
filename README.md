# Objective

Design a custom RAG pipeline to answer questions from this textbook -

> https://openstax.org/details/books/concepts-biology

## Important Pointers:

1. Download the pdf from the link above
2. To make indexing faster, you can pick any 2 chapters from the pdf and treat it as a source.
3. Use any in-memory vector database if required.
4. Use any open source HuggingFace model as the LLM Model

## Output

* Entire codebase in GitHub with links to access artifacts we need for evaluation:
    - Please add docstrings wherever necessary.
* Additional Colab notebook to run the backend logic and evaluations:
    - Please add text blocks in your Colab to add scenarios/assumptions etc to make it readable.
* Any additional artifacts like system design architecture, assumptions, list of issues you couldn’t solve because of time constraints and how you can fix it in future.

## Additional (bonus):

1. Streamlit/Gradio Frontend to interact with your pipeline
2. Wrap the entire application inside a docker container
3. Draft and implement all the necessary APIs using FastAPI or any other python web framework of choice
4. Produce alternative way to do the RAG without using any library like Langchain, LLamaIndex or Haystack

# Approach

The entire working code for the implementation is available in **RAG_pipeline.ipynb**. There is a link inside the Jupyter notebook to open it in *Google Colab*. The approach taken to implement the solution is explained below.

## Indexing Pipeline

The RAG consists of two parts, namely Information Retrieval and Text Generation. The data needs to be indexed into a Vector database as the first step for Information Retrieval. The steps involved in indexing the data are listed below:

### Step 1: Data preprocessing

* The PDF document was downloaded using the url
* PyMuPDF was used to extract tables and text from the PDF and convert it to a markdown format for easier parsing

### Step 2: Chunking

* The markdown file was then passed to *Unstructured* library for parsing and *Chunking*
* Unstructured takes into consideration the structure of the markdown file while chunking using the *chunk by title* strategy.
    - this method is better than using strategies like *RecursiveTextSplitter* since the latter may split the text randomly making it difficult to have similar meaning text together.

### Step 3: Indexing data into Vector Database

* Use [all-MiniLM-L6-v2](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2) for mapping the text to dense vector space.
* The text along with metadata and embeddings are then pushed to *Chroma* vector database

## Architecture Diagram for RAG Pipeline

![Architecture](./Architecture_Diagram.png)

# Future Scope

Some improvements that can be made to the project are explained below

## Parsing images and tables from PDF

The input PDF document seems to have a lot of images and tables along with regular text. Some of these images has text inside them that might be useful for answering questions. Therefore its vital to design an PDF parsing mechanism that deals with Image and table parsing from the PDF. 

This can be done using a library called [Unstructured.io](https://unstructured.io/). The library does this using Advanced NLP and computer vision models like [OCR, LayoutLMV3, DONUT](https://unstructured.io/blog/an-introduction-to-vision-transformers-for-document-understanding) etc

### Extracting tables from PDF

``` python
pdf_filename = "alphabet_10K_2022.pdf"

from unstructured.partition.pdf import partition_pdf

elements = partition_pdf(pdf_filename, strategy="hi_res", hi_res_model_name=”yolox”,
                         infer_table_structure=True,)

elements[0].metadata
```

### Extracting images from PDF

``` python
from unstructured.partition.pdf import partition_pdf

partition_pdf(
    filename="path/to/your/pdf_file.pdf",                  # mandatory
    strategy="hi_res",                                     # mandatory to use ``hi_res`` strategy
    extract_images_in_pdf=True,                            # mandatory to set as ``True``
    extract_image_block_types=["Image", "Table"],          # optional
    extract_image_block_to_payload=False,                  # optional
    extract_image_block_output_dir="path/to/save/images",  # optional - only works when ``extract_image_block_to_payload=False``
    )
```

## Improving Search Relevance with Hybrid Search

Hybrid Search combines semantic and keyword searches using sparse-dense vectors, offering more comprehensive and contextually relevant search results.

* **Semantic Search**: Hybrid Search employs dense vectors to enable semantic search, which focuses on finding semantically similar results within the documents. This approach ensures that search results are not solely reliant on keyword matches but also consider the context and meaning of the content.

* **Keyword Search**: Additionally, sparse vectors are utilized for keyword searches, emphasizing the importance of specific words in the search query. By incorporating both semantic and keyword search capabilities, Hybrid Search ensures that search results are precisely matched to the query keywords while also maintaining contextual relevance.

By leveraging Hybrid Search, we anticipate improving the search functionality of this application, providing users with more accurate and meaningful search results, ultimately enhancing the overall user experience.
