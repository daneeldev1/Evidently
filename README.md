# Evidently

Evidently is an open-source Python framework for evaluating, testing, and monitoring machine-learning and LLM-powered systems.

It supports offline experiments, regression testing, CI/CD validation, production monitoring, data-drift detection, model-quality analysis, LLM evaluation, tracing, and self-hosted observability workflows.

## Overview

Evidently works with tabular and text data and provides more than 100 built-in evaluations for:

- Data quality
- Data drift
- Classification
- Regression
- Ranking
- Recommendation systems
- Text quality
- RAG evaluation
- LLM outputs
- LLM-as-a-judge workflows

You can use Evidently as a lightweight Python library for one-off evaluations or run its monitoring UI for continuous evaluation over time.

## Features

### Reports

Reports compute and summarize data, ML, and LLM quality metrics.

They can be used for:

- Exploratory analysis
- Experiment comparison
- Model debugging
- Data validation
- Regression testing
- Monitoring
- CI/CD checks

Results can be viewed interactively or exported as Python objects, dictionaries, JSON, or HTML.

### Tests

Reports can include pass/fail conditions.

Tests are useful for:

- CI/CD quality gates
- Regression checks
- Data-pipeline validation
- Model release validation
- Production health checks

Conditions can be configured manually or inferred from reference data.

### Presets

Presets provide ready-to-use groups of evaluations.

Common presets cover:

- Data drift
- Data summary
- Classification
- Regression
- Text evaluation
- Recommendation quality

### Data Drift

Evidently includes multiple statistical tests and distance measures for detecting changes in feature distributions.

Drift analysis can cover:

- Numerical features
- Categorical features
- Dataset-level drift
- Feature-level drift
- Reference-versus-current comparisons

### Data Quality

Built-in checks can identify:

- Missing values
- Duplicate rows
- Unexpected categories
- Range violations
- Correlation changes
- Distribution anomalies

### Classification

Classification evaluation can include:

- Accuracy
- Precision
- Recall
- F1 score
- ROC AUC
- Confusion matrix
- Probability quality
- Error analysis

### Regression

Regression evaluation can include:

- MAE
- Mean error
- RMSE
- Error distribution
- Error bias
- Residual behavior

### Ranking and RAG

Ranking metrics include:

- NDCG
- MAP
- MRR
- Hit Rate
- Retrieval relevance
- Ranking effectiveness

These are useful for search and RAG retrieval evaluation.

### Recommendation Systems

Recommendation evaluation can cover:

- Diversity
- Novelty
- Serendipity
- Popularity bias
- Ranking quality

### Text and LLM Evaluation

Evidently supports row-level descriptors and aggregate metrics for text and generative AI.

Examples include:

- Text length
- Sentiment
- Language
- Toxicity
- Pattern matching
- Semantic similarity
- Retrieval relevance
- Summarization quality
- LLM-as-a-judge evaluation

### Tracing

Evidently can work with OpenTelemetry-based tracing data.

Tracing can capture:

- Inputs
- Outputs
- Session identifiers
- Model calls
- Tool calls
- Intermediate events
- Application attributes

Traced datasets can then be evaluated with the same Dataset, descriptor, Report, metric, and test APIs.

### Monitoring UI

The self-hosted UI can display:

- Projects
- Evaluation runs
- Metrics
- Test outcomes
- Historical changes
- Traces
- Monitoring dashboards

## Tech Stack

| Area | Technologies |
| --- | --- |
| Language | Python 3.10+ |
| Data processing | pandas, NumPy |
| Statistics | SciPy, statsmodels |
| Machine learning | scikit-learn |
| Visualization | Plotly |
| Service framework | Litestar |
| Application server | Uvicorn |
| Validation | Pydantic |
| Configuration | Dynaconf, YAML |
| CLI | Typer, Rich |
| Storage abstraction | fsspec |
| SQL support | SQLAlchemy, Alembic, psycopg2 |
| LLM integrations | OpenAI, LiteLLM |
| NLP | NLTK, Transformers |
| Embeddings | Sentence Transformers |
| Retrieval | FAISS, LlamaIndex |
| Spark support | PySpark |
| Tracing | OpenTelemetry, Tracely |
| Testing | Pytest |
| Type checking | mypy |
| Linting | Ruff |
| Packaging | Hatchling |

## Installation

### Requirements

```text
Python 3.10+
```

### Create a Virtual Environment

```bash
python -m venv .venv
```

Linux or macOS:

```bash
source .venv/bin/activate
```

Windows PowerShell:

```powershell
.venv\Scripts\Activate.ps1
```

### Install Evidently

```bash
pip install evidently
```

Verify installation:

```bash
evidently --help
```

### Optional LLM Dependencies

```bash
pip install "evidently[llm]"
```

### SQL Support

```bash
pip install "evidently[sql]"
```

### Spark Support

```bash
pip install "evidently[spark]"
```

### S3 Support

```bash
pip install "evidently[s3]"
```

### Google Cloud Storage Support

```bash
pip install "evidently[gcs]"
```

## Quick Start

### Data Drift Report

```python
from sklearn import datasets

from evidently import Report
from evidently.presets import DataDriftPreset


iris = datasets.load_iris(as_frame=True).frame

report = Report(
    [
        DataDriftPreset(method="psi"),
    ],
    include_tests="True",
)

result = report.run(
    iris.iloc[:60],
    iris.iloc[60:],
)

result
```

### Export Results

Save HTML:

```python
result.save_html("report.html")
```

Export JSON:

```python
result.json()
```

Export a dictionary:

```python
result.dict()
```

## LLM Evaluation

Create an evaluation dataset:

```python
import pandas as pd

from evidently import Dataset
from evidently import DataDefinition
from evidently.descriptors import Contains
from evidently.descriptors import Sentiment
from evidently.descriptors import TextLength


frame = pd.DataFrame(
    [
        {
            "question": "What is the capital of Japan?",
            "answer": "The capital of Japan is Tokyo.",
        },
        {
            "question": "Who painted the Mona Lisa?",
            "answer": "Leonardo da Vinci.",
        },
    ]
)

dataset = Dataset.from_pandas(
    frame,
    data_definition=DataDefinition(),
    descriptors=[
        Sentiment("answer", alias="Sentiment"),
        TextLength("answer", alias="Length"),
        Contains(
            "answer",
            items=["sorry", "apologize"],
            mode="any",
            alias="Denials",
        ),
    ],
)
```

Inspect enriched data:

```python
dataset.as_dataframe()
```

Create a text evaluation Report:

```python
from evidently import Report
from evidently.presets import TextEvals


report = Report(
    [
        TextEvals(),
    ]
)

result = report.run(dataset)
```

## Monitoring UI

Start the self-hosted demo UI:

```bash
evidently ui --demo-projects all
```

The UI runs on port `8000` by default.

With `uv`:

```bash
uv run --with evidently evidently ui --demo-projects all
```

## Monitoring Workflow

A typical monitoring workflow is:

```text
Production Data
      ↓
Evidently Report
      ↓
Metrics and Tests
      ↓
Workspace
      ↓
Monitoring UI
```

This pattern works for:

- Scheduled batch monitoring
- ML model validation
- Data-quality monitoring
- LLM quality tracking
- Retrieval evaluation
- Release regression checks

## Datasets

Evidently Dataset objects can:

- Wrap pandas data
- Define column semantics
- Add descriptors
- Add derived values
- Preserve evaluation context
- Support tabular and text workloads

A `DataDefinition` can describe important columns such as:

- Prediction
- Target
- Input
- Output
- Text
- Numerical features
- Categorical features

## Descriptors

Descriptors compute row-level properties.

Examples include:

- Text length
- Sentiment
- Pattern matches
- Semantic properties
- Custom evaluators

Descriptors are useful when raw model outputs need enrichment before aggregate metrics are calculated.

## Custom Metrics

Custom evaluation logic can be added when built-in metrics do not capture application-specific quality.

Typical custom use cases include:

- Business-rule compliance
- Domain-specific quality scores
- Safety checks
- Custom retrieval metrics
- Application-specific thresholds

## Self-Hosted Storage

Optional SQL dependencies include:

```text
SQLAlchemy
psycopg2
Alembic
```

SQL-backed storage is useful when:

- Multiple processes need shared state
- Monitoring data must survive restarts
- The UI is deployed remotely
- A production database is preferred over local storage

PostgreSQL is a practical choice for shared deployments.

## Tracing

AI application traces can be collected through OpenTelemetry-compatible workflows.

Typical trace attributes include:

```text
session_id
prompt
response
model
latency
tool
retrieval_context
```

Traced datasets can be evaluated using:

- Descriptors
- Reports
- Metrics
- Tests
- LLM-based evaluators

## Configuration

Evidently supports configuration through Python, CLI options, YAML, environment variables, and Dynaconf-based settings.

Important areas include:

| Area | Purpose |
| --- | --- |
| Workspace | Stores projects and evaluation runs |
| Service host | Network interface for the UI |
| Service port | Monitoring service port |
| SQL storage | Persistent database configuration |
| LLM provider | Model provider and credentials |
| Tracing | Telemetry destination and project metadata |
| Object storage | S3 or GCS integration |
| Telemetry | Framework telemetry controls |

Keep API keys and database credentials outside source control.

## CI/CD Integration

A typical quality gate can:

1. Load reference data.
2. Load candidate or production data.
3. Run a Report.
4. Evaluate pass/fail conditions.
5. Save the result artifact.
6. Fail the pipeline when critical checks fail.

Common validation targets include:

- Data drift
- Schema changes
- Data quality
- Model performance
- Retrieval quality
- LLM output quality

## Production Monitoring

For production environments:

- Store repeated evaluation runs
- Use stable reference datasets
- Version model and data metadata
- Track important metrics over time
- Add explicit test thresholds
- Use persistent storage
- Protect provider credentials
- Review privacy requirements for traces
- Monitor the UI service
- Monitor SQL storage health
- Run expensive evaluations outside latency-sensitive inference paths

## Development

Clone the repository:

```bash
git clone <repository>
cd evidently
```

Create an environment:

```bash
python -m venv .venv
source .venv/bin/activate
```

Install in editable mode:

```bash
pip install -e ".[dev]"
```

Install optional development features when needed:

```bash
pip install -e ".[dev,llm,sql]"
```

## Testing

Run tests:

```bash
pytest
```

Run a targeted test:

```bash
pytest tests/path/to/test_file.py
```

The repository defines markers including:

```text
slow
asyncio
```

## Code Quality

Run Ruff:

```bash
ruff check .
```

Format code:

```bash
ruff format .
```

Run type checking:

```bash
mypy src/evidently
```

Run pre-commit checks:

```bash
pre-commit run --all-files
```

## Contributing

Create a focused branch from the current development branch and follow existing testing, typing, and quality conventions.

Before submitting changes:

- Add tests for fixes and new behavior
- Run targeted Pytest suites
- Run Ruff checks
- Run formatting
- Run mypy when applicable
- Run pre-commit checks
- Preserve public APIs where possible
- Keep optional integrations behind extras
- Avoid committing credentials or sensitive datasets
- Update documentation for user-facing changes
- Keep changes focused and clearly documented



