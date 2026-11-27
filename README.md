# AI-Powered Document Intelligence and Automated Table Extraction using Databricks

## 1. Project Overview

This project implements an AI-powered document processing pipeline using **Databricks**. It automatically processes Word documents (`.docx`), identifies tables, dynamically generates extraction schemas, extracts structured data, classifies individual tables, and stores the results as separate CSV files.

The pipeline also maintains tracking information to prevent unnecessary reprocessing of documents. Using **Databricks Lakeflow Jobs and File Arrival Triggers**, the entire workflow can execute automatically whenever a new document is uploaded to the designated input directory.

The primary objective is to demonstrate how AI capabilities can be integrated into a data engineering workflow to transform unstructured documents into structured, categorized datasets with minimal manual intervention.

## 2. Key Features

- **Automated Document Parsing:** Identifies document elements and extracts table content from Word documents.
- **Dynamic Table Identification:** Detects multiple tables within a document without relying on predefined table positions or structures.
- **AI-Based Schema Generation:** Generates extraction schemas dynamically based on the headers and contents of individual tables.
- **Structured Data Extraction:** Extracts table contents into structured datasets while inferring appropriate data types.
- **AI-Powered Table Classification:** Classifies each extracted table into one of five predefined categories.
- **Individual CSV Generation:** Saves every extracted table as a separate CSV file.
- **Document Tracking:** Maintains records of processed documents and table classifications.
- **Automated Execution:** Uses Lakeflow Jobs with File Arrival Triggers to initiate processing when new documents arrive.

## 3. Technology Stack

| Technology | Purpose |
|---|---|
| Databricks | Data processing and execution environment |
| Python | Workflow development and processing logic |
| PySpark | Distributed data processing and DataFrame operations |
| Spark SQL | Executing Databricks AI functions |
| Unity Catalog | Data organization and managed storage |
| Databricks AI Functions | Document parsing, schema generation, extraction and classification |
| GPT-OSS-120B | AI model used for dynamic schema generation |
| Lakeflow Jobs | Workflow orchestration |
| File Arrival Trigger | Event-based workflow automation |
| CSV | Structured output format |

## 4. Project Architecture

The project follows a document-to-data processing architecture.

### Step 1: Document Upload

Users upload Word documents into the designated input directory within a Unity Catalog Volume.

### Step 2: Document Parsing

The `ai_parse_document()` function processes the uploaded document and identifies its elements, including tables.

### Step 3: Dynamic Table Identification

The parsed document is examined to identify all available tables. Each table is extracted independently along with its position and HTML representation.

### Step 4: Dynamic Schema Generation

The `ai_query()` function, using the GPT-OSS-120B model, analyzes each table and generates a suitable JSON extraction schema based on its headers and sample values.

This eliminates the need to manually define column names and data types for every document.

### Step 5: Table Extraction

The `ai_extract()` function uses the generated schema to extract structured data from each table.

The extracted records are converted into Spark DataFrames, with cleaned column names retained in the output.

### Step 6: AI-Based Classification

Each table is independently classified using `ai_classify()`.

The classification process uses five predefined categories:

1. Academic_Report
2. Employee_Information
3. Sales_and_Finance
4. Technical_Documentation
5. Other

### Step 7: CSV Generation

Every successfully extracted table is saved as an individual CSV file in the output directory.

The generated filenames include the original document name and the corresponding table position.

### Step 8: Tracking and Duplicate Prevention

Two tracking files are maintained:

- **category_tracking.txt:** Stores the generated CSV filename and its corresponding classification category.
- **processed_files.txt:** Maintains document processing records to prevent previously processed files from being processed again.

### Step 9: Automated Orchestration

Databricks Lakeflow Jobs executes the complete processing notebook.

A File Arrival Trigger monitors the input directory and initiates the job when new files arrive.

---

## 5. Unity Catalog Storage Structure

The project uses the following Unity Catalog structure:

- **Catalog:** `document_ai_catalog`
- **Schema:** `document_processing`
- **Volume:** `document_files`

The Volume contains three primary directories:

| Directory | Purpose |
|---|---|
| `input/` | Stores uploaded Word documents |
| `output/` | Stores extracted CSV files |
| `tracking/` | Stores processing and classification records |

### Example Directory Structure

    document_ai_catalog/
    └── document_processing/
        └── document_files/
            ├── input/
            │   ├── student_academic_report.docx
            │   └── employee_information.docx
            │
            ├── output/
            │   ├── student_academic_report_table_3.csv
            │   ├── student_academic_report_table_5.csv
            │   └── employee_information_table_2.csv
            │
            └── tracking/
                ├── category_tracking.txt
                └── processed_files.txt

The example filenames illustrate the expected naming convention. Actual table positions and output files depend on the uploaded documents.

## 6. Databricks Notebook Organization

The project is organized into three notebooks, each serving a distinct purpose.

| Notebook | Responsibility |
|---|---|
| `00_setup_storage` | Initial setup of the Unity Catalog storage structure and required directories. |
| `01_document_parsing` | Exploration and testing of document parsing, table identification, schema generation, extraction and classification. |
| `02_complete_document_pipeline` | Implements the complete end-to-end processing workflow, including CSV generation and tracking. |

For a detailed understanding of the implementation, refer to the notebooks in the repository.

## 7. Workflow Automation Using Lakeflow Jobs

The complete processing workflow is orchestrated using Databricks Lakeflow Jobs.

**Job Name:** `Document_AI_Processing`

**Task Name:** `process_documents`

**Execution Notebook:** `02_complete_document_pipeline`

**Compute:** Serverless

**Trigger Type:** File Arrival

The File Arrival Trigger monitors the following directory:

`/Volumes/document_ai_catalog/document_processing/document_files/input/`

When a new document arrives, Databricks evaluates the monitored location and automatically initiates the notebook workflow.

The trigger is configured with:

- Minimum time between triggers: 1 minute.
- Wait after last change: 1 minute.

These settings help control trigger frequency and allow files to finish uploading before processing.

## 8. End-to-End Workflow

The overall workflow can be summarized as follows:

**Word Document Upload → Document Parsing → Table Identification → Dynamic Schema Generation → Data Extraction → Table Classification → CSV Generation → Tracking Updates**

The entire workflow is executed automatically through Lakeflow Jobs once the File Arrival Trigger detects a qualifying new file.

## 9. Expected Outputs

For every successfully processed document:

- Each identified table is extracted independently.
- A separate CSV file is generated for each table.
- Each table receives one classification from the predefined categories.
- The classification tracking file is updated.
- The processed document is recorded to prevent duplicate processing.

The resulting CSV files can subsequently be used for reporting, analytics, visualization and further data engineering workflows.

## 10. Key Learning Outcomes

Through this project, the following concepts and technologies were explored:

- Working with Unity Catalog catalogs, schemas and Volumes.
- Reading and processing binary documents using PySpark.
- Extracting structured information from unstructured documents.
- Integrating Databricks AI Functions into data processing workflows.
- Generating dynamic schemas using LLMs.
- Working with Spark SQL and PySpark together.
- Handling semi-structured AI function outputs.
- Performing AI-based classification.
- Managing CSV outputs and processing metadata.
- Building automated workflows using Lakeflow Jobs.
- Configuring File Arrival Triggers for automatic execution.

## 11. Conclusion

This project demonstrates an end-to-end approach to automating document intelligence using Databricks and its built-in AI capabilities.

By combining document parsing, LLM-based schema generation, structured extraction, AI classification and workflow orchestration, the solution reduces the need for manually processing documents with varying table structures.

The project also demonstrates how AI-powered processing can be incorporated into a practical data engineering pipeline, transforming document-based information into structured datasets ready for downstream analytical use.
