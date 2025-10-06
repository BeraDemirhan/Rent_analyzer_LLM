# NYC Rent Roll Query System

## Overview
This project uses [LlamaIndex](https://llamaindex.ai) to index and query rent roll data from text files (e.g., OCR-processed documents like the 1996 Registration Rent Roll Report for 127 Riverside Drive). The system leverages [Milvus](https://milvus.io) for vector storage and [Ollama](https://ollama.ai) for local language model processing with the `llama3.1:8b` model. Users can query details such as apartment rents, tenant names, and lease dates using natural language.

## Prerequisites
- Python 3.10 or higher
- [Docker](https://www.docker.com) (for running Milvus)
- [Ollama](https://ollama.ai) (for running the `llama3.1:8b` model)
- A directory containing text files with rent roll data (e.g., `NYC-rent-project/127Riverside-Drive-DHCR`)

## Setup Instructions

1. **Activate the Python Environment**:
   Ensure you're in the correct Python environment (e.g., `bizai`):
   ```bash
   source ~/opt/anaconda3/envs/bizai/bin/activate
   ```

2. **Install Dependencies**:
   Install the required Python packages:
   ```bash
   pip install llama-index-core llama-index-vector-stores-milvus llama-index-llms-ollama llama-index-embeddings-ollama
   ```

3. **Start Milvus**:
   Run a Milvus standalone server using Docker:
   ```bash
   docker run -d --name milvus_standalone -p 19530:19530 -p 9091:9091 milvusdb/milvus:latest
   ```
   Verify Milvus is running:
   ```bash
   docker ps
   ```

4. **Set Up Ollama**:
   Start the Ollama server:
   ```bash
   ollama serve
   ```
   Pull the `llama3.1:8b` model:
   ```bash
   ollama pull llama3.1:8b
   ```
   Confirm the model is installed:
   ```bash
   ollama list
   ```

5. **Prepare Data**:
   Place your rent roll text files (e.g., OCR output from `page_14.png`) in the data directory:
   ```
   NYC-rent-project/127Riverside-Drive-DHCR
   ```
   Ensure the files are plain text and contain structured data (e.g., apartment numbers, rents, tenant names).

## Usage
1. **Run the Script**:
   Execute the main script (`main.py`) to index the documents and start querying:
   ```bash
   python main.py
   ```

2. **Example Queries**:
   The script supports natural language queries on the indexed data. Examples include:
   - "What is the legal regulated rent for apartment 22?"
     - Expected: $1007.07
   - "Who is the tenant of apartment 3?"
     - Expected: Robert Townsend
   - "List all tenants with a legal regulated rent above $1000."
     - Expected: Lists tenants like William Corkhill ($1363.27), Julie Simons ($1575.90), etc.
   - "Which apartments have leases ending in 1996?"
     - Expected: Lists apartments like 32 (ends 04/14/96), 33 (ends 05/31/96), etc.
   - "Who are the tenants in the penthouse apartments?"
     - Expected: Marshall Clements (Penthouse 1), Cynthia Morgan (Penthouse 2)

   See the script for more example queries.

## Project Structure
- **main.py**: Main script to load documents, index them with LlamaIndex and Milvus, and run queries using Ollama.
- **data/**: Directory containing text files with rent roll data (e.g., `127Riverside-Drive-DHCR`).
- **README.md**: This file.

## Notes
- **Embedding Dimension**: The script uses `dim=4096` for `llama3.1:8b` embeddings in Milvus. Verify the dimension if you encounter errors:
  ```python
  from llama_index.embeddings.ollama import OllamaEmbedding
  embed_model = OllamaEmbedding(model_name="llama3.1:8b")
  embedding = embed_model.get_text_embedding("test text")
  print(len(embedding))  # should match dim in MilvusVectorStore
  ```
- **Alternative Embedding Model**: If `llama3.1:8b` is slow or unsuitable for embeddings, consider using `sentence-transformers/all-MiniLM-L6-v2` (requires `llama-index-embeddings-huggingface` and `dim=384`).
- **Troubleshooting**:
  - Ensure Milvus is running on `localhost:19530`.
  - Ensure Ollama is running and `llama3.1:8b` is available.
  - Check that text files in the data directory are readable and properly formatted.
  - If queries return unexpected results, simplify the phrasing or verify the indexed data.

## License
This project is licensed under the MIT License.
