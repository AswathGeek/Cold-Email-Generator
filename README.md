# AI-Powered Job Application Cold Email Generator

This project automates the process of identifying suitable job postings from company career pages, extracting key information, and generating personalized cold emails for job applications. It leverages Large Language Models (LLMs) for intelligent text extraction and email generation, combined with a vector store for portfolio matching.

## Table of Contents

- [Features](#features)
- [Architecture](#architecture)
- [How It Works](#how-it-works)
- [Setup and Installation](#setup-and-installation)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)

## Features

* **Automated Job Posting Extraction**: Scrapes career pages and extracts job details like role, experience, skills, and description in a structured JSON format.
* **Intelligent Portfolio Matching**: Uses a vector store to find relevant portfolio links based on the skills required for a job.
* **Personalized Cold Email Generation**: Crafts customized cold emails incorporating job details and relevant portfolio highlights.
* **LLM-Powered**: Utilizes powerful Large Language Models for robust text understanding and generation.

## Architecture

The system's architecture is designed as a pipeline to efficiently process job information and generate tailored outreach emails.

![Architecture Diagram](architecture.png)

### Architecture Breakdown:

1.  **Career's Page**: The starting point, where raw job posting data is collected from company career pages.
2.  **LLM (Extraction)**: A Large Language Model processes the raw text from the career page to extract structured job information.
3.  **Extracted Jobs in JSON Format**: The LLM outputs job details in a clean JSON format with specific fields: `job_title`, `skills`, `experience`, and `job_description`.
4.  **Vector Store**: This component stores and retrieves portfolio links based on associated tech stacks or skills, enabling intelligent matching.
5.  **LLM (Email Generation)**: Another Large Language Model takes the extracted job details and relevant portfolio links to generate a personalized cold email.
6.  **Cold Mails**: The final output, ready to be sent to potential employers.

## How It Works

The process flows as follows:

1.  **Web Scraping**: The `WebBaseLoader` is used to fetch the content of a given career page URL.
2.  **Information Extraction**: An LLM (specifically `llama3-70b-8192` from `ChatGroq`) is prompted to extract `job_title`, `experience`, `skills`, and `job_description` from the scraped page content and output it as a JSON object. A `JsonOutputParser` then processes this output into a usable Python dictionary.
3.  **Portfolio Retrieval**: The extracted `skills` from the job description are used to query a `chromadb` vector store. This vector store is pre-populated with your portfolio data, linking `Techstack` to `Links`. The query retrieves the most relevant portfolio links based on the job's skill requirements.
4.  **Email Generation**: Finally, the extracted job details and the retrieved portfolio links are fed into another LLM prompt. This prompt instructs the LLM to act as a Business Development Executive from TCS (or your chosen entity) and compose a personalized cold email, highlighting how your capabilities (and portfolio examples) align with the job's needs.

## Setup and Installation

To get this project up and running, follow these steps:

1.  **Clone the repository:**

    ```bash
    git clone [https://github.com/your-username/your-repo-name.git](https://github.com/your-username/your-repo-name.git)
    cd your-repo-name
    ```

2.  **Create a virtual environment (recommended):**

    ```bash
    python -m venv venv
    source venv/bin/activate  # On Windows: `venv\Scripts\activate`
    ```

3.  **Install the required dependencies:**

    The project uses `langchain_groq`, `langchain_community`, `langchain_core`, `chromadb`, and `pandas`.

    ```bash
    pip install -r requirements.txt
    ```

    *(Create a `requirements.txt` file with these libraries if you don't have one)*

4.  **Set up your Groq API Key:**

    You'll need a Groq API key to use the `ChatGroq` LLM. Replace `'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'` in `email_generator.ipynb` with your actual key, or set it as an environment variable.

    ```python
    llm = ChatGroq(
        temperature=0,
        groq_api_key='YOUR_GROQ_API_KEY', # Replace with your key
        model_name="llama3-70b-8192"
    )
    ```

5.  **Prepare your Portfolio Data:**

    Ensure you have a `my_portfolio.csv` file in the root directory of your project. This CSV should have at least two columns: `Techstack` (listing technologies used in a project) and `Links` (the URL to your portfolio item). An example is provided in the `email_generator.ipynb`.

    ```csv
    Techstack,Links
    React, Node.js, MongoDB,[https://example.com/react-portfolio](https://example.com/react-portfolio)
    Angular,.NET, SQL Server,[https://example.com/angular-portfolio](https://example.com/angular-portfolio)
    Python, Django, MySQL,[https://example.com/python-portfolio](https://example.com/python-portfolio)
    Machine Learning, Python, TensorFlow,[https://example.com/ml-python-portfolio](https://example.com/ml-python-portfolio)
    # ... more entries
    ```

    The `email_generator.ipynb` will automatically populate the ChromaDB vector store with this data if it's not already populated.

## Usage

To run the project and generate a cold email:

1.  **Open the Jupyter Notebook:**

    ```bash
    jupyter notebook email_generator.ipynb
    ```

2.  **Modify the `WebBaseLoader` URL:**

    In the notebook, change the URL in the `WebBaseLoader` to the career page you want to scrape:

    ```python
    loader = WebBaseLoader("YOUR_CAREER_PAGE_URL_HERE")
    ```

3.  **Run all cells:**

    Execute all the cells in the Jupyter Notebook sequentially. The final cell will print the generated cold email to the console.
