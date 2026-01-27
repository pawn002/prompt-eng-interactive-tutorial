# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

This is Anthropic's **Prompt Engineering Interactive Tutorial** - a hands-on course teaching optimal prompt engineering techniques for Claude through Jupyter notebooks. The tutorial consists of 9 chapters progressing from beginner to advanced topics, plus an appendix with advanced methods.

## Repository Structure

The repository contains **three parallel implementations** of the same tutorial content:

### 1. **Anthropic 1P/** - Direct API Access
- Uses `anthropic` Python SDK directly
- Requires Anthropic API key from console.anthropic.com
- Install: `pip install anthropic`

### 2. **AmazonBedrock/anthropic/** - Bedrock via Anthropic SDK
- Uses Anthropic SDK through AWS Bedrock
- Requires AWS credentials and Bedrock access
- Install: `pip install -r AmazonBedrock/requirements.txt`

### 3. **AmazonBedrock/boto3/** - Bedrock via boto3
- Uses boto3 for AWS Bedrock API calls
- Requires AWS credentials and Bedrock access
- Install: `pip install -r AmazonBedrock/requirements.txt`

**All three implementations teach the same content** - users choose based on their API access method.

## Running the Tutorial

### Starting Jupyter Notebook

Navigate to your chosen implementation directory and start Jupyter:

```bash
# For Anthropic 1P (direct API)
cd "Anthropic 1P"
python -m notebook

# For Bedrock implementations
cd AmazonBedrock/anthropic
python -m notebook
# or
cd AmazonBedrock/boto3
python -m notebook
```

Jupyter will open in your browser with an access token URL.

### Initial Setup in Notebooks

1. Open `00_Tutorial_How-To.ipynb` first
2. Set your API credentials:
   - **Anthropic 1P**: Set `API_KEY` with Anthropic API key
   - **Bedrock**: Configure AWS credentials (varies by method)
3. Set `MODEL_NAME = "claude-3-haiku-20240307"` (or other Claude model)
4. Run cells with `Shift + Enter` to execute and move to next cell
5. The `%store` magic command persists variables across notebooks

### Standard Workflow

Each notebook follows this pattern:
1. Import dependencies and load stored variables (`%store -r`)
2. Define helper functions (e.g., `get_completion()`)
3. Lesson content in markdown cells
4. Interactive exercises with grading functions
5. Example playground at the bottom for experimentation

## Tutorial Content Structure

**Chapters (work through in order):**

**Beginner:**
- Chapter 1: Basic Prompt Structure
- Chapter 2: Being Clear and Direct
- Chapter 3: Assigning Roles

**Intermediate:**
- Chapter 4: Separating Data from Instructions
- Chapter 5: Formatting Output & Speaking for Claude
- Chapter 6: Precognition (Thinking Step by Step)
- Chapter 7: Using Examples

**Advanced:**
- Chapter 8: Avoiding Hallucinations
- Chapter 9: Complex Prompts (Industry Use Cases)

**Appendix:**
- Chaining Prompts
- Tool Use
- Search & Retrieval
- Empirical Performance Evaluations (Bedrock only)

## Key Files

- **hints.py**: Contains hints for all exercises (both `Anthropic 1P/hints.py` and `AmazonBedrock/utils/hints.py`)
- **PROGRESS_LOG.md**: User-maintained progress tracking (not part of original repo)
- **requirements.txt**: Bedrock dependencies only (in `AmazonBedrock/` directory)

## Helper Functions Pattern

All notebooks use a consistent pattern with helper functions:

```python
# Anthropic 1P version
def get_completion(prompt: str):
    message = client.messages.create(
        model=MODEL_NAME,
        max_tokens=2000,
        temperature=0.0,
        messages=[{"role": "user", "content": prompt}]
    )
    return message.content[0].text
```

Bedrock implementations have similar functions but use different client initialization methods.

## Exercise System

Exercises include **automated grading functions** that check:
- Exact string matches
- Pattern matching (regex)
- Word counts
- Presence of specific keywords

Users iterate on prompts until passing the grading criteria.

## Important Notes

- **Model**: Tutorial designed for Claude 3 Haiku (cheapest/fastest), but techniques apply to all Claude models
- **Temperature**: Examples use `temperature=0.0` for deterministic outputs
- **Answer Key**: Static answer key available at https://docs.google.com/spreadsheets/d/1jIxjzUWG-6xBVIa2ay6yDpLyeuOh_hR_ZB75a47KX_E/edit
- **Alternative**: Google Sheets version exists using Claude for Sheets extension
- **Jupyter Syntax**: `!command` runs shell commands within notebooks (e.g., `!pip install anthropic`)

## API Requirements

### Anthropic 1P
- API key from https://console.anthropic.com/
- Requires credits/billing setup
- Messages API via Anthropic Python SDK

### Amazon Bedrock
- AWS account with Bedrock access enabled
- AWS credentials configured (via AWS CLI, environment variables, or IAM roles)
- Claude models enabled in Bedrock console for your region
- See AmazonBedrock documentation for setup details

## Personal Learning Environment

This is a **personal learning project** using git for version control and experimentation:

- **Branching**: Create branches to experiment with different approaches to exercises
- **Baselines**: Tag or commit clean states to easily revert if confused
- **Progress Tracking**: Use `PROGRESS_LOG.md` to track learning progress
- **No PR Intent**: Modifications are for personal learning, not for contributing back to the original repository

### Suggested Git Workflow

```bash
# Create a branch for each chapter or experiment
git checkout -b chapter-3-experiments

# Commit after completing exercises or reaching milestones
git add "Anthropic 1P/03_Assigning_Roles_Role_Prompting.ipynb"
git commit -m "Completed Chapter 3 exercises"

# Tag clean baselines for easy reversion
git tag chapter-3-complete

# Return to a baseline if needed
git checkout chapter-3-complete
```
