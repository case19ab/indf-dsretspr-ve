# GPT-4.1 Multiple-Choice Question Answering System

This Python script leverages the OpenAI GPT-4.1 model to answer multiple-choice questions based on a provided text corpus. It is designed to analyze questions related to the corpus content, select the best option, and provide evidence and an explanation for its choice.

The primary corpus currently embedded in the script is "LÆREMATERIALE TIL INDFØDSRETSPRØVEN" (Learning Material for the Citizenship Test), focusing on Danish society, culture, and history.

## Features

-   **Corpus-Based Q&A**: Answers questions strictly based on the provided text corpus.
-   **Multiple-Choice Processing**: Handles questions with multiple options (A, B, C, etc.).
-   **Evidence and Explanation**: Aims to provide direct quotes from the corpus as evidence and a textual explanation for the chosen answer.
-   **Two Modes of Operation**:
    1.  **File Processing Mode**: Batch process questions from a text file.
    2.  **Interactive Mode**: Enter questions and options directly in the console.
-   **OpenAI API Integration**: Uses the `openai` Python library to communicate with the GPT-4.1 model.

## Prerequisites

-   Python 3.7+
-   OpenAI Python library: `pip install openai`
-   An active OpenAI API key with access to GPT-4.1 (or a similar model).

## Setup

1.  **Clone the repository or download the script (`Test/test_gpt.4.1.py`).**
2.  **Install the OpenAI library:**
    ```bash
    pip install openai
    ```
3.  **Set up your OpenAI API Key:**
    The script will look for an environment variable `OPENAI_API_KEY`. You can set it in your system:
    -   Linux/macOS: `export OPENAI_API_KEY='your_api_key_here'` (add to your `.bashrc` or `.zshrc` for persistence)
    -   Windows (PowerShell): `$env:OPENAI_API_KEY='your_api_key_here'` (add to your PowerShell profile for persistence)
    -   Windows (CMD): `set OPENAI_API_KEY=your_api_key_here`
    If the environment variable is not found, the script will prompt you to enter the API key when it runs.

4.  **Corpus Configuration (Optional):**
    -   The script uses an embedded corpus (`CORPUS_CONTENT` variable in `Test/test_gpt.4.1.py`). You can modify this variable directly in the script if you wish to use a different default corpus.
    -   Alternatively, you can set the `CORPUS_PATH` environment variable to point to a `.txt` file containing your corpus. If this variable is set and the file exists, it will be used instead of the embedded `CORPUS_CONTENT`.
        ```python
        # Example: CORPUS_CONTENT in the script
        CORPUS_CONTENT = """
        Your text corpus goes here...
        """
        ```
        If `CORPUS_PATH` is set, the script attempts to load it:
        ```python
        # CORPUS_PATH = os.environ.get("CORPUS_PATH", "corpus.txt")
        # ... logic to load from this path ...
        ```
        Currently, the script initializes `CORPUS_CONTENT` with a large string and does **not** automatically load from `CORPUS_PATH` unless you modify the script to prioritize `CORPUS_PATH` if `CORPUS_CONTENT` is also defined. The existing logic for `CORPUS_PATH` is:
        `CORPUS_PATH = os.environ.get("CORPUS_PATH", "corpus.txt")`
        Followed by the definition of `CORPUS_CONTENT`. To make `CORPUS_PATH` override the embedded content, you'd need to add logic to read the file specified by `CORPUS_PATH` into `CORPUS_CONTENT` if the file exists.

## Usage

The script can be run from the command line.

### 1. File Processing Mode

You can provide a path to a text file containing questions as a command-line argument.

**Input File Format (`questions.txt`):**

Each question and its options form a block. Blocks are separated by **two or more newlines**.
Within each block:
-   The **first line** is the question.
-   Subsequent lines are the options.
-   Options must start with `A:`, `B:`, `C:`, etc., followed by the option text.

**Example `questions.txt`:**

```
What is the capital of Denmark?
A: Oslo
B: Copenhagen
C: Stockholm

Which color is not typically found on the Danish flag?
A: Red
B: White
C: Blue
D: Green
```

**Running the script:**

```bash
python Test/test_gpt.4.1.py your_questions_file.txt
```

The script will process each question, display the model's selected answer, evidence, and explanation, and finally provide a summary of the results.

### 2. Interactive Mode

If you run the script without any command-line arguments, it will present a menu:

```
Danish History Multiple-Choice Question Answering System (GPT-4.1)
Options:
1. Process questions from file
2. Interactive question mode
3. Exit
Enter your choice (1-3):
```

-   **Choice 1**: Prompts you to enter the path to a questions file (same format as above).
-   **Choice 2**: Allows you to enter a question and its options directly in the console.
    -   First, enter the question.
    -   Then, enter each option one by one. Press Enter on an empty line to signify you've finished adding options for that question.
    -   The script will then process the question and display the result.
    -   Enter 'quit' as the question to exit interactive mode.
-   **Choice 3**: Exits the script.

## Script Structure Overview

(Located in `Test/test_gpt.4.1.py`)

-   **Global Variables**:
    -   `OPENAI_API_KEY`: Stores the API key.
    -   `CORPUS_PATH`: Path to an external corpus file (optional, see Setup).
    -   `CORPUS_CONTENT`: Holds the actual text corpus.
    -   `openai_client`: The OpenAI API client instance.
-   **`parse_questions_from_file(file_path)`**: Reads and parses the question file.
-   **`answer_multiple_choice(question, options)`**:
    -   Constructs the prompt by combining the `CORPUS_CONTENT`, the given `question`, and its `options`.
    -   Sends the prompt to the GPT-4.1 model via the OpenAI API.
    -   Parses the model's response to extract the selected option, evidence, and explanation.
    -   Returns a dictionary with the results.
-   **`process_questions_from_file(file_path)`**:
    -   Uses `parse_questions_from_file` to get questions.
    -   Iterates through each question, calling `answer_multiple_choice`.
    -   Prints results for each question and a final summary.
-   **`main()`**:
    -   Handles command-line arguments.
    -   Manages the interactive menu and user input.

## How it Works

1.  **Initialization**: The script initializes the OpenAI client. The `CORPUS_CONTENT` is defined globally (or loaded if `CORPUS_PATH` logic is fully implemented and prioritized).
2.  **Question Input**: Questions are either parsed from a file or entered interactively.
3.  **Prompt Construction**: For each question, a detailed prompt is created. This prompt includes:
    -   Instructions to the model.
    -   The entire `CORPUS_CONTENT`.
    -   The specific question.
    -   The formatted multiple-choice options.
    -   A required output format (SELECTED OPTION, EVIDENCE, EXPLANATION).
4.  **API Call**: The constructed prompt is sent to the specified OpenAI model (`gpt-4.1` by default).
5.  **Response Parsing**: The script parses the text response from the API to identify the selected option, the supporting evidence, and the model's explanation.
6.  **Output**: The results are displayed to the user. For file processing, a summary of successful answers is also shown.

## Customization

-   **Model**: You can change the OpenAI model by modifying the `model` parameter in the `openai_client.chat.completions.create` call within the `answer_multiple_choice` function (e.g., to `gpt-4-turbo` or another compatible model).
-   **Corpus**: Modify the `CORPUS_CONTENT` global variable or implement robust loading from `CORPUS_PATH` to use your own data.
-   **Prompt Engineering**: The prompt within `answer_multiple_choice` can be adjusted to change how the model is instructed, potentially improving accuracy or the format of the output.

## Troubleshooting

-   **API Key Issues**: Ensure your `OPENAI_API_KEY` is correctly set and has the necessary permissions and available quota.
-   **File Not Found**: Double-check the path to your questions file in file processing mode.
-   **Incorrect File Format**: Ensure your questions file adheres to the specified format (questions and options separated by blank lines, options prefixed with `A:`, `B:`, etc.). The script includes some debug prints during parsing that might help identify issues.
-   **API Errors**: Network issues or errors from the OpenAI API will be printed to the console. These might include token limit errors if the corpus and question combine to be too large for the model's context window.
-   **Low Success Rate**:
    -   The quality of answers depends heavily on the clarity of the questions, the relevance of the corpus content, and the capabilities of the LLM.
    -   Consider refining the prompt in `answer_multiple_choice` or ensuring the corpus contains the necessary information.
    -   The corpus might require cleaning or preprocessing for better results.
```
