# AI & NLP Powered PDF Question Generator

This project automatically generates diverse types of questions (with answers) directly from the content of PDF documents. It leverages Natural Language Processing (NLP) techniques and Optical Character Recognition (OCR) to understand the text, identify key concepts, and formulate relevant questions.

## Features

*   **PDF Text Extraction:** Extracts text directly from text-based PDFs and uses OCR (Tesseract via `pytesseract`) for image-based PDFs or scanned documents.
*   **Content-Aware Filtering:** Intelligently identifies and skips cover pages, tables of contents, references, etc.
*   **Noise Reduction:** Cleans extracted text by removing common noise like page numbers, headers/footers, URLs, and irrelevant short lines.
*   **Relevance Scoring:** Prioritizes pages with higher informational content for question generation.
*   **Key Phrase Extraction:** Uses NLTK for Part-of-Speech (POS) tagging to identify important nouns, verbs, and adjectives as potential question topics.
*   **Key Sentence Identification:** Detects sentences likely containing definitions or important statements based on keywords and structure.
*   **Diverse Question Generation:** Creates various question types based on Bloom's Taxonomy levels and common assessment formats:
    *   Factual (What, Who, When, Where, How)
    *   Conceptual (Why, Significance, Explain)
    *   Analytical (Compare/Contrast, Analyze, Evaluate)
    *   Application (How to apply, Use case)
    *   Multiple Choice Questions (MCQ)
    *   True/False
*   **Answer Retrieval:** Attempts to locate relevant sentences or context within the PDF to serve as answers for the generated questions.
*   **Source Tracking:** Provides the page number(s) where the information for the answer was likely found.
*   **Output:** Prints questions and answers to the console and saves them to a downloadable text file (`questions_and_answers.txt`).

## Technologies Used

*   **Python 3**
*   **PyPDF2:** For reading and extracting text from PDF files.
*   **pdf2image:** To convert PDF pages into images for OCR.
*   **Pytesseract (Tesseract OCR):** For extracting text from images (OCR).
*   **NLTK (Natural Language Toolkit):** For text processing tasks like tokenization, POS tagging, and stopword removal.
*   **Pillow (PIL Fork):** Image processing library (dependency for `pdf2image`/`pytesseract`).
*   **Google Colab:** The environment this script is designed to run in (uses `google.colab.files` for upload/download).
*   **System Dependencies:**
    *   `poppler-utils`: Required by `pdf2image`.
    *   `tesseract-ocr`: The OCR engine used by `pytesseract`.

## Setup and Installation (Google Colab)

This script is designed to be run in a Google Colab notebook. The necessary setup commands are included at the beginning of the script:

1.  **Install Python Packages:**
    ```bash
    !pip install PyPDF2 pdf2image pytesseract nltk Pillow
    ```
2.  **Install System Dependencies:**
    ```bash
    !apt-get update
    !apt-get install -y poppler-utils tesseract-ocr
    ```
3.  **Download NLTK Data:**
    ```python
    import nltk
    nltk.download('punkt')
    nltk.download('averaged_perceptron_tagger')
    nltk.download('stopwords')
    ```

## Usage

1.  **Prepare your Environment:** Open the script (`.ipynb` file) in Google Colab.
2.  **Run Setup Cells:** Execute the installation and NLTK download cells provided above (they should be at the top of your notebook).
3.  **Adjust `num_questions` (Optional):** Modify the `num_questions` variable near the end of the script to specify how many questions you want to generate.
    ```python
    # Set the number of questions you want to generate
    num_questions = 15 # Change this value as needed
    ```
4.  **Run the Main Script:** Execute the remaining cells of the notebook.
5.  **Upload PDF:** When prompted, use the "Choose Files" button to upload the PDF document you want to process.
6.  **Processing:** The script will extract text, analyze content, and generate questions. Progress messages will be printed in the output. This might take a few minutes depending on the PDF's size and complexity (especially if OCR is heavily used).
7.  **View Output:** The generated questions, along with their types, answers, and source page numbers, will be printed in the Colab output.
8.  **Download Results:** A file named `questions_and_answers.txt` containing the generated content will be automatically downloaded to your local machine.

## How It Works

1.  **PDF Loading:** The script takes an uploaded PDF file as input.
2.  **Text Extraction:** It first tries direct text extraction using `PyPDF2`. It then uses `pdf2image` and `pytesseract` (OCR) to extract text from pages that might be images or where direct extraction failed/yielded poor results.
3.  **Preprocessing & Filtering:**
    *   Identifies and skips potential cover pages, TOCs, etc., based on text patterns and content length.
    *   Removes noise like page numbers, headers/footers, and filters out pages with low relevance scores (calculated based on sentence count and information density).
    *   Cleans the remaining text.
4.  **NLP Analysis:**
    *   Uses `nltk` to tokenize the text into sentences and words.
    *   Performs Part-of-Speech (POS) tagging to identify nouns, verbs, adjectives, etc.
    *   Extracts potentially important phrases based on sequences of relevant POS tags (e.g., adjective-noun, noun-verb).
    *   Identifies key sentences containing indicators of importance or definitions.
5.  **Question Generation:**
    *   Selects important phrases and key sentences as subjects for questions.
    *   Uses predefined templates for different question types (Factual, Conceptual, MCQ, etc.).
    *   Fills the templates with the extracted phrases/concepts.
6.  **Answer Finding:**
    *   For a given question subject (phrase), it searches the extracted text for sentences containing definitions or context related to that phrase.
    *   It prioritizes definition patterns (e.g., "X is defined as...", "Y refers to...") and contextual sentences.
    *   The located sentence (or a combination) is used as the answer.
7.  **Output Formatting:** The generated question-answer pairs, along with metadata (type, page), are formatted and presented.

## Limitations and Future Work

*   **OCR Accuracy:** The quality of questions from image-based PDFs depends heavily on the OCR accuracy, which can vary based on image quality, layout, and font.
*   **Answer Quality:** Answer finding is based on heuristics and pattern matching. It might not always capture the most nuanced or comprehensive answer. It doesn't perform deep semantic understanding or summarization.
*   **Complex Layouts:** PDFs with complex multi-column layouts, tables, or figures might pose challenges for text extraction and ordering.
*   **Context Understanding:** The model primarily relies on sentence-level context and keyword matching; deeper contextual understanding across paragraphs or sections is limited.
*   **Colab Dependency:** Currently tied to the Google Colab environment for easy dependency management and file handling.

**Potential Improvements:**

*   Integrate more advanced QA models (like those based on Transformers) for better answer generation/selection.
*   Improve layout analysis for more accurate text ordering in complex PDFs.
*   Add summarization capabilities to generate more concise answers.
*   Develop a more sophisticated relevance scoring mechanism.
*   Refactor into a standalone Python application or web service.
*   Support other document formats (e.g., .docx, .txt).
