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

## Data preprocessing

The input PDF document seems to have a lot of images and tables along with regular text. Some of these images has text inside them that might be useful for answering questions. Therefore its vital to design an PDF parsing mechanism that deals with Image and table parsing from the PDF. This can be done using a library called [Unstructured.io](https://docs.unstructured.io/open-source/core-functionality/partitioning#partition). The library does this using Advanced NLP and computer vision models like [OCR, LayoutLMV3, DONUT](https://unstructured.io/blog/an-introduction-to-vision-transformers-for-document-understanding) etc

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

## Architecture Diagram for RAG Pipeline

![Architecture](./Architecture_Diagram.png)

# References

* [Optimizing Unstructured Data Retrieval](https://unstructured.io/blog/optimizing-unstructured-data-retrieval)
