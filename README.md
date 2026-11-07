# InsightFlow AI
## Multi-Agent System for Customer Feedback Analysis

InsightFlow AI is a Python project that helps analyse customer survey responses, understand satisfaction trends and find useful information from a FAQ knowledge base.

The idea behind this project is to bring different types of analysis together instead of handling everything separately. It uses multiple specialised agents, with each agent responsible for a particular task. The agents work together to analyse survey data, compare results across different periods and retrieve relevant information from FAQs.

I built this project to explore how multi-agent systems, data analysis and Retrieval-Augmented Generation can be combined to solve a practical business problem.

## 1. Key Features

- Survey Analysis: Calculates the total number of responses, average ratings and Customer Satisfaction Score (CSAT).
- Feedback Analysis: Identifies commonly discussed topics in customer comments, including food quality, waiting time, staff behaviour and pricing.
- Period Comparison: Compares survey results across different months and highlights changes in customer satisfaction.
- FAQ Retrieval: Uses semantic search to find relevant information from a local FAQ knowledge base.
- Multi-Agent Workflow: Assigns different tasks to specialised agents and brings their results together.
- Structured Outputs: Uses Pydantic to maintain consistent communication between agents.
- RAG Evaluation: Tests FAQ retrieval using sample business questions.
- Automated Testing: Includes tests for metric calculations and agent functionality.

The project also includes a synthetic dataset containing 50,000 survey responses covering May and June 2026.

## 2. How the System Works
                 Business Question
                         |
                         v
                  +--------------+
                  | Orchestrator |
                  +--------------+
                    /     |     \
                   /      |      \
                  v       v       v
             +--------+ +----------+ +---------+
             |  Data  | |Comparison| |   RAG   |
             | Agent  | |  Agent   | |  Agent  |
             +--------+ +----------+ +---------+
                  |          |           |
                  v          v           v
              Survey      Period      FAQ Search
              Analysis   Comparison   ChromaDB
                  |          |           |
                  +----------+-----------+
                             |
                             v
                       SummaryAgent
                             |
                             v
                      Final Response

The application follows a multi-agent architecture. Each agent handles a specific responsibility, while the Orchestrator manages the overall workflow.

When a business question is submitted, the Orchestrator identifies what needs to be done and forwards the task to the appropriate agent. The results are then collected and passed to the SummaryAgent.

### Orchestrator

The Orchestrator manages the workflow between the different agents. It receives business questions, identifies the required tasks and coordinates the execution of those tasks.

### DataAgent

The DataAgent works with the survey dataset. It calculates response counts, customer satisfaction scores and average ratings. It also identifies common topics in customer feedback.

### ComparisonAgent

The ComparisonAgent focuses on comparing survey results across different periods. It highlights changes in response counts, CSAT and average ratings, making it easier to understand how customer satisfaction has changed.

### RAGAgent

The RAGAgent handles questions that require information from the FAQ knowledge base. It generates text embeddings using Sentence Transformers and searches ChromaDB for relevant information.

### SummaryAgent

The SummaryAgent collects the outputs from the other agents and prepares the final response. It uses predefined rules to answer supported operational questions and summarises the available survey metrics.

## 3. Technologies Used

| Technology | Usage |
|---|---|
| Python | Application development |
| Pandas | Survey data processing |
| NumPy | Numerical calculations |
| Pydantic | Data validation and structured outputs |
| Sentence Transformers | Text embeddings |
| ChromaDB | Vector storage and semantic retrieval |
| uv | Dependency management |
| Pytest | Automated testing |

## 4. Project Structure

The project is organised into separate modules for agents, data processing, FAQ retrieval and testing.

```text
InsightFlow-AI/
|
|-- app/
|   |-- agents/
|   |   |-- orchestrator.py
|   |   |-- data_agent.py
|   |   |-- rag_agent.py
|   |   |-- comparison_agent.py
|   |   |-- summary_agent.py
|   |
|   |-- rag/
|   |   |-- ingestion.py
|   |   |-- retrieval.py
|   |
|   |-- tools/
|   |   |-- metrics.py
|   |
|   |-- schemas.py
|
|-- data/
|-- scripts/
|-- tests/
|-- main.py
|-- pyproject.toml
|-- requirements.txt
|-- uv.lock
|-- README.md
```

## 5. Getting Started

### Requirements

- Python 3.11 or later
- uv package manager
- Internet connection for the initial model download

### Clone the Repository

```bash
git clone https://github.com/LeelaVani238/InsightFlow-AI.git
cd InsightFlow-AI
```

### Install Dependencies

```bash
uv sync
```

### Generate Survey Data

```bash
uv run python -m scripts.generate_data
```

This generates the synthetic survey dataset used by the application.

### Prepare the FAQ Knowledge Base

```bash
uv run python -m scripts.ingest_faq
```

This reads the FAQ document, splits it into smaller text chunks, generates embeddings and stores them in ChromaDB.

### Run RAG Evaluation

```bash
uv run python -m scripts.evaluate_rag
```

The evaluation script runs sample questions against the FAQ knowledge base and saves the results in the data directory.

### Run Tests

```bash
uv run python -m pytest -v
```

### Start the Application

```bash
uv run python main.py
```

## 6. Survey Analysis

One of the main parts of the project is analysing customer feedback through numerical metrics.

The application calculates the following:

**Response Count**

The total number of valid survey responses for the selected period.

**Customer Satisfaction Score (CSAT)**

CSAT represents the percentage of customers who provided a positive rating. Ratings of 4 and 5 are considered positive on a five-point scale.

Formula:

CSAT = (Positive Responses / Total Valid Responses) x 100

**Average Rating**

The average of all valid customer ratings, providing an overall view of customer satisfaction.

**Feedback Themes**

The application also identifies commonly mentioned topics in customer comments, such as waiting time, food quality, cleanliness, pricing and staff behaviour.

## 7. Retrieval-Augmented Generation

The FAQ retrieval component uses semantic search to find information related to a user's question.

The embedding model used is:

`sentence-transformers/all-MiniLM-L6-v2`

The retrieval process works as follows:

1. The FAQ document is loaded into the application.
2. The content is divided into smaller overlapping chunks.
3. Sentence Transformers converts the chunks into embeddings.
4. The embeddings are stored in ChromaDB.
5. When a question is submitted, the same embedding model converts it into a vector.
6. ChromaDB searches for the most relevant content.
7. The retrieved information is passed to the response workflow.

This approach helps retrieve relevant information even when the wording of a question is different from the original FAQ content.

### Sample Questions

The evaluation script includes questions such as:

- How quickly should a customer complaint be escalated?
- What waiting time should customers expect during peak hours?
- What CSAT score triggers a root-cause review?

The retrieved content and corresponding responses are saved for further review.

## 8. Sample Survey Comparison

The project includes synthetic survey data for May and June 2026. The ComparisonAgent uses this data to demonstrate how customer satisfaction changes can be analysed.

One sample evaluation produced the following results:

| Metric | May 2026 | June 2026 |
|---|---:|---:|
| Survey Responses | 25,246 | 24,754 |
| CSAT | 51.48% | 50.32% |
| Average Rating | 3.34 | 3.30 |

The comparison shows a slight decrease in both CSAT and average ratings during June. The response count also decreased.

These results demonstrate how the system presents changes across different periods and helps identify areas that may need further investigation.

Note: The figures are generated from synthetic survey data and are intended for demonstration purposes.

## 9. Testing and Evaluation

Testing is an important part of the project because the different agents depend on one another to complete the workflow.

The project includes:

- Tests for survey metric calculations.
- Individual agent execution scripts.
- Tests for comparison functionality.
- Tests for FAQ retrieval.
- Orchestrator workflow testing.
- SummaryAgent tests.
- Sample RAG evaluation questions.

These tests help verify individual components and check that the main workflows operate as expected.

## 10. Future Enhancements

Some areas I would like to explore further are:

- Adding sentiment analysis for more detailed customer feedback classification.
- Supporting comparisons across custom date ranges.
- Improving FAQ retrieval and answer relevance.
- Exploring LLM-based orchestration for more flexible task routing.
- Adding more RAG evaluation questions and performance metrics.
- Introducing additional data visualisations.
- Building an API to make the analysis functionality accessible to other applications.

## Conclusion

InsightFlow AI brings together customer survey analysis, feedback insights, period comparisons and semantic FAQ retrieval through a multi-agent architecture.

Building this project gave me an opportunity to work with Python, data processing, embeddings, vector databases and agent coordination. It also helped me understand how different AI components can be organised to work together on a business problem.

The project is a starting point for exploring how AI-based systems can make customer feedback analysis and information retrieval more efficient.
