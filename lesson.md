# Web Scraping and Sentiment Analysis

We are going to build a basic web scraper that will scrape authors and quotes from a popular site used to learn web scraping called quotes.toscrape.com. We will store the data in a json file. We will then use Gemini API to create a sentiment analysis of the quotes which we will store in a text file. Today we will use Python but the same can be done in other languages including JavaScript using Cheerio or Puppeteer.

## Objectives

Learn how to:
- Use Python to scrape a website
- Use Gemini API to analyze text

## Setup

**Reminder:** Remember to `setup` and `activate` your virtual environment, choose your python interpreter and install the ipykernel. If you do not remember how please refer to the [Python Workspace Setup Instructions](https://github.com/jdrichards-pursuit/python-virtual-environment-setup).

**Note:** Because this is `not a Jupyter Notebook`, you will not need to select the Python Interpreter or install the ipykernel.

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

## 4. Before coding

For any web scraping project, the second thing you should do is take a look at the website you will be scraping. This will give you a good idea of what you are dealing with and what you need to scrape. (The first thing you should do is read the [Terms of Service](https://quotes.toscrape.com/robots.txt) of the website to make sure you are allowed to scrape it.)

 If you go to [quotes.toscrape.com](https://quotes.toscrape.com) in your browser, you will see a list of quotes. Now open your browser's developer tools by pressing `Cmd+Option+I` (Mac).

Take a look at the elements that make up the page. We are interested in the quotes and the authors.

In the **Elements** tab, look for the elements that contain the quotes and the authors in the **HTML** section.

### Questions to answer

- What are the tags of the elements that contain the quotes?
- What are the classes of the elements that contain the quotes?
- What are the tags of the elements that contain the authors?
- What are the classes of the elements that contain the authors?
- Are the quotes nested inside other elements? If so, what are the tags and classes of those elements?

Keep these tags in mind as we will be using them to scrape the data.

### 5. Importing Required Libraries

**Note:** All of the code for this lesson will be copied and pasted by you into the `main.py` file.

Now that you have your API key, you've installed the required libraries and have activated your virtual environment, you can import the required libraries into your `main.py` file.

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

### 6. Declaring `scrape_quotes()` Function

The first thing we need to do is create a driver instance. This is the main object that we will use to interact with the browser.

Copy and paste the following code into your `main.py` file:

```python
def scrape_quotes():
    base_url = "https://quotes.toscrape.com"
    
    # configure Chrome options and create a driver instance
    options = webdriver.ChromeOptions()
    options.add_argument('--headless')
    options.add_argument('--disable-gpu')
    options.add_argument('--no-sandbox')
    options.add_argument('--disable-dev-shm-usage')
    driver = webdriver.Chrome(options=options)

    driver.get(base_url)
```

#### `scrape_quotes()` function explanation

```python
base_url = "https://quotes.toscrape.com"
```
- Defines the target URL we want to scrape
- This is a practice website specifically designed for learning web scraping

```python
options = webdriver.ChromeOptions()
```
- Creates a new ChromeOptions object
- This object allows us to customize how Chrome will run
- Think of it as configuring your browser settings before launching

```python
options.add_argument('--headless')
```
- Enables headless mode
- Runs Chrome without opening a visible window
- Saves system resources and is better for automation
- Essential for server environments without displays

```python
options.add_argument('--disable-gpu')
```
- Disables GPU hardware acceleration
- Reduces resource usage
- Important when running in headless mode
- Particularly useful on Windows systems

```python
options.add_argument('--no-sandbox')
```
- Disables the Chrome sandbox
- The sandbox is a security feature that isolates browser processes
- Often necessary when running as root or in Docker containers
- **Security Note**: Only use in controlled environments

```python
options.add_argument('--disable-dev-shm-usage')
```
- Prevents issues with limited shared memory in some Linux environments
- dev/shm is a temporary file storage system in Linux
- Chrome uses this for storing temporary files
- This flag forces Chrome to use /tmp instead

```python
driver = webdriver.Chrome(options=options)
```
- Creates a new instance of Chrome WebDriver
- Applies all the options we configured above
- This is our main interface for controlling the browser
- The driver will use these settings for all subsequent operations

```python
driver.get(url)
```
- Tells the browser to navigate to our target URL
- Waits for the page to load before proceeding
- Similar to typing a URL in your browser and hitting Enter
- Returns control only after the page has loaded

#### Testing the `scrape_quotes()` function

We can already test this function and see the page source by adding the following code to the scrape_quotes function:

```python
print(driver.page_source)
```
and adding the following code to the bottom of our `main.py` file under the def main() function:
```python
scrape_quotes()
```

Now run the program by typing `python main.py` in your terminal.

Take a look in your terminal to see the page source. The page source is the HTML of the page you are scraping.

#### Add BeautifulSoup to the mix

Now we can use BeautifulSoup to parse the page source and extract the quotes and authors.

Copy and paste the following code into your `main.py` file at the bottom of the `scrape_quotes()` function:
```python
soup = BeautifulSoup(driver.page_source, 'html.parser')
```

# Understanding the BeautifulSoup Line of Code

```python
soup = BeautifulSoup(driver.page_source, 'html.parser')
```

This single line of code does three important things:

## 1. Gets the HTML Content: `driver.page_source`
- `driver` is your Selenium browser
- `page_source` captures all the HTML on the current webpage
- Think of it like viewing "View Page Source" in your web browser
- Includes any content that was loaded or changed by JavaScript

## 2. Specifies the Parser: `'html.parser'`
- Tells BeautifulSoup how to read the HTML
- `'html.parser'` is Python's built-in HTML parser
- It's like giving BeautifulSoup a set of instructions on how to interpret the HTML code
- Works well for most websites and comes pre-installed with Python

## 3. Creates the Soup Object: `BeautifulSoup()`
- Takes the raw HTML and turns it into an organized structure
- Makes the HTML easy to search through and navigate
- Creates something like a map of the webpage's content
- Allows you to easily find and extract specific information later

## Real-World Analogy
Think of it like this:
- The webpage is like a book
- `driver.page_source` takes a photo of every page
- `'html.parser'` is like knowing how to read the language the book is written in
- `BeautifulSoup()` organizes everything into a detailed table of contents
- The final `soup` object is your organized, searchable version of the webpage

The end result is a `soup` object that lets you easily find and extract any information you want from the webpage, like finding specific chapters in a well-organized book.



## 6. Declaring `setup_api()` Function

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
You've done this before so we don't need to go into as much detail. But you can read this here later if you want. This will setup the API key and model configuration for use after we build our web scraper.

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





<!-- ### Common Additional Options

You might also see these useful options in other scripts:
```python
# Set a custom user agent
options.add_argument('--user-agent="Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36"')

# Set window size (even in headless mode)
options.add_argument('--window-size=1920,1080')

# Disable images for faster loading
options.add_argument('--blink-settings=imagesEnabled=false')

# Disable JavaScript (use carefully)
options.add_argument('--disable-javascript')
```

### Error Handling Best Practices

It's good practice to wrap the driver creation and usage in try-except blocks:
```python
try:
    driver = webdriver.Chrome(options=options)
    driver.get(url)
except WebDriverException as e:
    print(f"Failed to initialize browser: {e}")
finally:
    if 'driver' in locals():
        driver.quit()
```

### Resource Management

Always remember to close the driver when you're done:
```python
# At the end of your script
driver.quit()
``` -->