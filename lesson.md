# Web Scraping and Sentiment Analysis

We are going to build a basic web scraper that will scrape authors and quotes from a popular site used to learn web scraping called quotes.toscrape.com. We will store the data in a json file. We will then use Gemini API to create a sentiment analysis of the quotes which we will store in a text file. Today we will use Python but the same can be done in other languages including JavaScript using Cheerio or Puppeteer.

## Objectives

Learn how to:
- Use Python to scrape a website
- Use Gemini API to analyze text

## Setup

**Reminder:** Remember to `setup` and `activate` your virtual environment, choose your python interpreter and install the ipykernel. If you do not remember how please refer to the [Python Workspace Setup Instructions](https://github.com/jdrichards-pursuit/python-virtual-environment-setup).

### 1. Retrieving Your API Key

Before we begin, you will need to retrieve your Gemini API key.

If you do not have one, use the following set of instructions to sign up for an account and retrieve your API key.

[Gemini API Key](https://github.com/jdrichards-pursuit/gemini-api-key-acquire?tab=readme-ov-file)

### 2. Setting Up Your Environment Variables

Now that you have your API key, you can set up your environment variables.

Create a new file called `.env` and add the following line of code:

```bash
API_KEY=<your-api-key>
```
### 3. Installing Required Libraries

Another efficient way to install the required libraries is to create a `requirements.txt` file and place your libraries in it.

Create a `requirements.txt` file and add the following libraries (do not include the numbers):

1. selenium
2. beautifulsoup4
3. python-dotenv
4. google-generativeai
5. python-dotenv

Once you have added the libraries to your `requirements.txt` file, you can install them by running the following command:

```bash
pip install -r requirements.txt
```

The `-r` flag is used to read the requirements from the `requirements.txt` file and install the libraries.


### 4. Importing Required Libraries

**Note:** All of the code for this lesson will be copied and pasted by you into the `main.py` file.

Now that you have your API key, you've installed the required libraries and have activated your virtual environment, you can import the required libraries in your `main.py` file.

```python
import os
from dotenv import load_dotenv
import google.generativeai as genai
import json
from bs4 import BeautifulSoup
from selenium import webdriver
from selenium.webdriver.support.ui import WebDriverWait 
from selenium.webdriver.support import expected_conditions as EC
from selenium.webdriver.common.by import By
```
#### Library Walkthrough

- **`os`**
  - Built-in Python library for OS operations

- **`python-dotenv`**
  - Python library for loading environment variables from a `.env` file

- **`json`**
  - Built-in Python library for working with JSON data

- **`google.generativeai`**
  - Official Google Generative AI library
  - Provides access to Gemini's multimodal capabilities

- **`bs4`**
  - BeautifulSoup4
  - Python library for parsing HTML and XML documents

- **`selenium`**
  - Python library for automating web browser interactions
  - Sub-libraries and classes of selenium:
    - **`webdriver`**
      - Python library for automating web browser interactions
    - **`WebDriverWait`**
      - Python class for specifying the maximum amount of time to wait for a condition to be met before proceeding with the code
    - **`EC`**
      - Python class for specifying the expected conditions to wait for
    - **`By`**
      - Python class for specifying the type of condition to wait for

## Declaring `setup_api()` Function

This time we will create a `setup` function to load our API key from the `.env` file as well as initialize and configure our Gemini model which we will later invoke and store in the `model` variable.

```python
def setup_api():
    load_dotenv()
    api_key = os.getenv('API_KEY')
    genai.configure(api_key=api_key)
    
    model_config = {
        "temperature": 0.2,
        "top_p": 0.95,
        "top_k": 40,
        "max_output_tokens": 4096,
    }
    
    return genai.GenerativeModel('gemini-1.5-flash', generation_config=model_config)
```

### `setup_api()` Function Walkthrough

#### Environment Setup
- Loads environment variables from `.env` file
- Retrieves API key from environment variables
- Configures Gemini API with the retrieved key

#### Model Configuration
Sets up model parameters:
- `temperature`: 0.2 (lower value for more focused/deterministic outputs)
- `top_p`: 0.95 (controls diversity of outputs)
- `top_k`: 40 (disabled, letting top_p handle output selection)
- `max_output_tokens`: 4096 (maximum length of generated responses)

### Return Value

Returns a configured instance of `GenerativeModel` using:
- Model: 'gemini-1.5-flash'
- Custom configuration settings as defined above