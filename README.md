<div align="center">
  <br />
      <img alt="FactFusion Logo" src="./assets/logo.jpg" alt="FactFusion Logo" width="85%" height="auto">
  <br />
</div>


# Overview
FactFusion is designed to automate the process of verifying factual information. It provides a comprehensive pipeline that breaks down long texts into individual claims, assesses their verification worthiness, generates evidence search queries, crawls for evidence, and ultimately verifies the claims. This tool is especially useful for everyday people, journalists, researchers, and anyone interested in ensuring the accuracy of information. 


# Quick Start

- [Quick Start](#Quick-Start)
  - [Installation](#installation)
    - [Clone the repository and navigate to the project directory](#clone-the-repository-and-navigate-to-the-project-directory)
    - [Installation with pip (option 1)](#installation-with-pip-option-1)
    - [Installation with poetry (option 2)](#installation-with-poetry-option-2)
  - [Configure API Keys](#configure-api-keys)
    - [Environment Variables](#environment-variables)
    - [Configuration Files](#configuration-files-required-if-web-enivronment-is-used)
    - [Additional API Configurations](#additional-api-configurations)
  - [Basic Usage](#basic-usage)
    - [Used as a Web App](#used-as-a-web-app)
    - [Used in Command Line](#used-in-command-line)
    - [Used as a Library](#used-as-a-library)
  - [Advanced Features](#advanced-features)
    - [Multimodality](#multimodality)
    - [Customized Prompts](#customized-prompts)
    - [Switch Between Models](#switch-between-models)
  - [Highly Recommended](#highly-recommended-groq-to-openai)

## Installation

### Clone the repository and navigate to the project directory
```bash
git clone  https://github.com/AADI-234/FactFusion.git
cd OpenFactVerification
```

### Installation with pip (option 1)
1. Create a Python environment at version 3.9 or newer and activate it.
2. Navigate to the project directory and install the required packages:
```cmd
pip install -r requirements.txt
```

### Installation with poetry (option 2)
1. Install Poetry by following it [installation guideline](https://python-poetry.org/docs/).
2. Install all dependencies by running:
```cmd
poetry install
```

## Configure API Keys

API keys can be configured from both **Environment Variables** and **Configuration Files**.
Specifically, the tool initialization loads API keys from environment variables or config file, config file take precedence. Related implementations can be seen from `factcheck/utils/api_config.py`.

### Environment Variables
Example: Export essential api key to the environment
```cmd
set SERPER_API_KEY=... # this is required for all the task
set GROQ_API_KEY=... # this is required for all the task 
set OPENAI_API_KEY=... # this is required only if you want to replace groq with openai
set ANTHROPIC_API_KEY=... # this is required only if you want to replace groq with anthropic
set LOCAL_API_KEY=... # this is required only if you want to use local LLM
set LOCAL_API_URL=... # this is required only if you want to use local LLM
```

### Configuration Files (Required if Web enivronment is used)

Alternatively, we can store the api information in a YAML file with the same key names as the environment variables and pass the path to the yaml file as an argument to the `check_response` method.

Example: 
Go to FactFusion->factcheck->config->api_config.yaml then set the API keys OR
Pass the path to the api configuration file  
```YAML
GROQ_API_KEY:null
SERPER_API_KEY: null
OPENAI_API_KEY: null
ANTHROPIC_API_KEY: null
LOCAL_API_KEY: null
LOCAL_API_URL: null
```

To load api configurations from a YAML file, please specify the path to the YAML file with the argument `--api_config`

```bash
python -m factcheck --modal string --input "MBZUAI is the first AI university in the world" --api_config PATH_TO_CONFIG_FILE/api_config.yaml
```

### Additional API Configurations

The supported api configuration variables are pre-defined in `factcheck/utils/api_config.py`.
```python
# Define all keys for the API configuration
keys = [
    "SERPER_API_KEY",
    "OPENAI_API_KEY",
    "ANTHROPIC_API_KEY",
    "GROQ_API_KEY",
    "LOCAL_API_KEY",
    "LOCAL_API_URL",
]
```

Only these variables can be loaded from **Environment Variables**. If additional variables are required, you are recommended to define these variable in a YAML files. All variables in the api configuration file will be loaded automatically.

## Basic Usage

### Used in Command Line

```python
python -m factcheck --input "MBZUAI is the first AI university in the world"
```

### Used as a Library

```python

from factcheck import FactCheck
factcheck_instance = FactCheck()

# Example text
text = "Your text here"

# Run the fact-check pipeline
results = factcheck_instance.check_response(text)
print(results)
```

### Used as a Web App


```bash
python webapp.py --api_config demo_data/api_config.yaml
```

<p align="center"><img src="../assets/online_screenshot.png"/></p>
<p align="center"><img src="../assets/2ndpage.PNG"/></p>


## Advanced Features

```bash
# String	(This Always works)
python -m factcheck --modal string --input "MBZUAI is the first AI university in the world"
```

### Multimodality (NOT YET IMPLEMENTED, WORKKING ON IT)

Different modalities (text, speech, image, and video) are unified in this tool by converting them into text, and then verified by the standard text fact verification pipeline.

```bash
# Text
python -m factcheck --modal text --input demo_data/text.txt
# Speech
python -m factcheck --modal speech --input demo_data/speech.mp3
# Image
python -m factcheck --modal image --input demo_data/image.webp
# Video
python -m factcheck --modal video --input demo_data/video.m4v
```

### Customized Prompts
Prompts for each step can be specified in a YAML/JSON file. Please see `factcheck/config/sample_prompt.yaml` as an example.

For now, there are four prompts in each file with respect to claim decomposition, claim checkworthy, query generation, and claim verification, respectively.

When using your own prompts, please use `--prompt` to specify the prompt file path.


```bash
python -m factcheck --input "MBZUAI is the first AI university in the world" --prompt PATH_TO_PROMPT/sample_prompt.yaml
```


### Switch Between Models

Currently, FactFusion supports models from OpenAI, Anthropic, and local-hosted models. To specify the model version used for fact checking, there are two arguments `--model` and `--client`.
Please see `factcheck/utils/llmclient/__init__.py` for details.

| Model     | --model        | --client     |
|-----------|----------------|--------------|
| Groq      | llama3-8b-8192 | None         |
| OpenAI    | gpt-VERSION    | None         |
| Anthropic | claude-VERSION | None         |
| Local     | MODEL_NAME     | local_openai |


```bash

# Groq
python -m factcheck --modal string --input "MBZUAI is the first AI university in the world" --model llama3-8b-8192

# OpenAI
python -m factcheck --modal string --input "MBZUAI is the first AI university in the world" --model gpt-4-turbo

# Anthropic
python -m factcheck --modal string --input "MBZUAI is the first AI university in the world" --model claude-3-opus-20240229

# Local
python -m factcheck --modal string --input "MBZUAI is the first AI university in the world"  --client local_openai --model wizardlm2
```

The prompt can be model-specific, especially if we ask the model to output a JSON format response. We have yet to have a chance to support all models, so you will have to adopt your own prompts when using models other than openai.

Besides, when using local_openai models, please make sure to specify `LOCAL_API_KEY` and `LOCAL_API_URL`.

You can get a serper key from https://serper.dev/
You can get a serper key from https://console.groq.com/keys

## Highly Recommended (GROQ TO OPENAI)


Use gpt api for the best results. The results are way better than any other models
- For configuration
- Open folder factcheck in VS code
- press ctrl+shift+F
- search llama3-8b-8192
- Replace this with gpt-4o only in 2 file webapp.py and at __init__.py
