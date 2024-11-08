# Web Scraping and Sentiment Analysis

We are going to build a basic web scraper that will scrape authors and quotes from a popular site used to learn web scraping called quotes.toscrape.com. We will store the data in a json file. We will then use Gemini API to create a sentiment analysis of the quotes which we will store in a text file. Today we will use Python but the same can be done in other languages including JavaScript using Cheerio or Puppeteer.

## Objectives

Learn how to:
- Use Python to scrape a website
- Use Gemini API to analyze text
- Save data to a JSON file
- Save data to a text file

## Setup

**Reminder:** Remember to `setup` and `activate` your virtual environment. If you do not remember how please refer to the [Python Workspace Setup Instructions](https://github.com/jdrichards-pursuit/python-virtual-environment-setup).

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
6. unicodedata

Once you have added the libraries to your `requirements.txt` file, you can install them by running the following command:

```bash
pip install -r requirements.txt
```

The `-r` flag is used to read the requirements from the `requirements.txt` file and install the libraries.

### 4. Study the Website Before Coding

For any web scraping project, the first thing you should do is read the [Terms of Service](https://quotes.toscrape.com/robots.txt) of the website to make sure you are allowed to scrape it.

Next, you should study the website you plan to scrape. Study the tree, the elements, classes, attributes, and any other information that will give you a good idea of what you are dealing with and what you need to scrape. 

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
import unicodedata
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

- **`unicodedata`**
  - Built-in Python library for working with Unicode characters

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

#### `scrape_quotes()` function explanation (**DO NOT COPY**)

`
base_url = "https://quotes.toscrape.com"
`
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

### Load the Parent Element

Before we scrape the page for our quotes and authors, we are going to load a parent element that contains the quotes and authors using Selenium. This ensures that the data is loaded into the page before we try to scrape it. (this will always depend on the website you are scraping and how it is structured as well as what data you are trying to scrape)

What is the parent element that contains all of the quotes?

We will use selenium to load that element by class name.

Add the following code to the bottom of your `scrape_quotes()` function:

```python
 try:
        WebDriverWait(driver, 10).until(
            EC.presence_of_element_located((By.CLASS_NAME, "col-md-8"))
        )
    except Exception as e:
        print(f"Error waiting for content to load: {e}")
        driver.quit()
        return []
    finally:
        driver.quit()
```

This code tells Selenium to wait up to 10 seconds for the element with the class name "col-md-8" to be present in the page. If the element is found within 10 seconds, the code will continue to execute. If the element is not found within 10 seconds, a `Exception` will be raised.

The `finally` block ensures that the driver is quit even if an exception is raised. This is important to avoid leaving the browser open and wasting resources.


#### Add BeautifulSoup to the scrape_quotes() function

Now we can use [BeautifulSoup](https://www.crummy.com/software/BeautifulSoup/bs4/doc/) to parse the page source and extract the quotes and authors.

Inside the `try` block after the `WebDriverWait` statement, but before the `except` statement, copy and paste the following code into your `scrape_quotes()` function:

```python
soup = BeautifulSoup(driver.page_source, 'html.parser')

quoteDivs = soup.find_all('div', class_='quote')

print(quoteDivs)
```

#### Understanding the BeautifulSoup Lines of Code (**DO NOT COPY**)

```python
soup = BeautifulSoup(driver.page_source, 'html.parser')
```

This single line of code does three important things:

- Gets the HTML Content: `driver.page_source`
  - `driver` is your Selenium browser
  - `page_source` captures all the HTML on the current webpage
- Think of it like viewing "View Page Source" in your web browser
- Includes any content that was loaded or changed by JavaScript

- Specifies the Parser: `'html.parser'`
  - Tells BeautifulSoup how to read the HTML
  - `'html.parser'` is Python's built-in HTML parser
- It's like giving BeautifulSoup a set of instructions on how to interpret the HTML code
- Works well for most websites and comes pre-installed with Python

- Creates the Soup Object: `BeautifulSoup()`
  - Takes the raw HTML and turns it into an organized structure
  - Makes the HTML easy to search through and navigate
- Creates something like a map of the webpage's content
- Allows you to easily find and extract specific information later

#### Real-World Analogy

Think of it like this:
- The webpage is like a book
- `driver.page_source` takes a photo of every page
- `'html.parser'` is like knowing how to read the language the book is written in
- `BeautifulSoup()` organizes everything into a detailed table of contents
- The final `soup` object is your organized, searchable version of the webpage

The end result is a `soup` object that lets you easily find and extract any information you want from the webpage, like finding specific chapters in a well-organized book.

**Explanation of the next line of code:**

```python
quoteDivs = soup.find_all('div', class_='quote')
```

This line of code finds all of the `div` elements with the class name `quote` and stores them in the `quoteDivs` variable.

#### Testing

Run the program by typing `python main.py` in your terminal.

Notice in the terminal that we have a list of `div` elements with the class name `quote` and all elements nested inside of them.

### 7. Scraping the Quotes and Authors

Our next step is to loop through the `quoteDivs` list and extract the quotes and authors and store them in a list of dictionaries.

Add the following code to the bottom of your `scrape_quotes()` function:

```python
quotes_and_authors = []

 for quoteDiv in quoteDivs:
        text = quoteDiv.find('span', class_='text').text
        author = quoteDiv.find('small', class_='author').text
       
        quotes_and_authors.append({
            'text': text,
            'author': author,
        })

    print(quotes_and_authors)
    return quotes_and_authors
```

In this code we loop through each `quoteDiv` in the `quoteDivs` list, extract the text and author from each `span` and `small` element, and store them in a dictionary. We then append that dictionary to the `quotes_and_authors` list.

#### Testing
(make sure to remove other print statements except for the one preceding the return statement)

Run the program by typing `python main.py` in your terminal.

Notice in the terminal that we have a list of dictionaries with the `text` and `author` keys and the values are the quotes and authors we scraped from the page.

### 8. Dynamically Scraping Multiple Pages

Change your print statement to print the length of the `quotes_and_authors` list.

```python
print(len(quotes_and_authors))
```

Now run the program by typing `python main.py` in your terminal.

Notice that we only have 10 quotes and authors. This is because we are only scraping the first page. But we want to scrape all of the pages.

Next, we are going to loop through multiple pages and scrape the data from each page.

So as not to confuse you, we will remove the scrape_quotes function from the `main.py` file and replace it with a new version that will scrape multiple pages.

```python
def scrape_quotes():
    base_url = "https://quotes.toscrape.com"
    quotes_and_authors = []
    page = 1
    
    while True:  # Loop until we don't find a next page
        # Construct the URL for the current page
        url = base_url if page == 1 else f"{base_url}/page/{page}"
        
        # ... existing Chrome setup code ...
        options = webdriver.ChromeOptions()
        options.add_argument('--headless')
        options.add_argument('--disable-gpu')
        options.add_argument('--no-sandbox')
        options.add_argument('--disable-dev-shm-usage')
        driver = webdriver.Chrome(options=options)

        print(f"Scraping page {page}: {url}")
        driver.get(url)

        try:
            # Wait for content to load
            WebDriverWait(driver, 10).until(
                EC.presence_of_element_located((By.CLASS_NAME, "col-md-8"))
            )
            
            # Parse the page
            soup = BeautifulSoup(driver.page_source, 'html.parser')
            quoteDivs = soup.find_all('div', class_='quote')
            
            # If no quotes found, we've reached the end
            if not quoteDivs:
                driver.quit()
                break
                
            # Extract quotes from current page
            for quoteDiv in quoteDivs:
                text = quoteDiv.find('span', class_='text').text
                author = quoteDiv.find('small', class_='author').text
                quotes_and_authors.append({
                    'text': text,
                    'author': author,
                })
            
            page += 1

            print(len(quotes_and_authors))
            return quotes_and_authors
        except Exception as e:
            print(f"Error on page {page}: {e}")
            driver.quit()
            break
        finally:
            driver.quit()

    return quotes_and_authors
```

Let's take a look at all of the changes we made to the `scrape_quotes()` function.

a. We added a `page` variable that will keep track of the current page number.
b. We added a `while True` loop that will continue to scrape pages until we don't find a next page. Thank you Selenium!
c. We added a `print` statement to show us each page we are scraping.
d. Go to the browser and click the next page button. Notice that the URL changes. We updated the `url` variable to dynamically construct the URL for the current page. Previously we had a hardcoded URL.
e. We added an `if` statement to check if the `quoteDivs` list is empty. If it is, we know we have reached the last page and we can break out of the loop.
f. We added a `print` statement to show us the length of the `quotes_and_authors` list after each page.



#### Testing

Run the program by typing `python main.py` in your terminal.

Notice that we see each page number and the length of the `quotes_and_authors` list after each page. We are scraping 10 pages and the length of the `quotes_and_authors` list is 100.

### 9. Shaping the Data for our JSON File and the Gemini API call

Before we store our data in a JSON file and call the Gemini API, we need to shape the data into a list of dictionaries where each dictionary contains the author and all of the quotes by that author.

We can create a new function called `author_quotes_collection()` that will take the `quotes_and_authors` list and return a list of dictionaries where each dictionary contains the author and all of the quotes by that author.

This code will be plain Python code and doesn't need any particular libraries. However, this will be a good shape for our Gemini API call to connect a single author with all of their quotes.

Add the following function declaration to the your `main.py` file bellow the `scrape_quotes()` function declaration:

```python
def author_quotes_collection(quotes):
    author_quotes = {}

    for quote in quotes:
        author = quote['author']
        text = quote['text']
        
        
        if author in author_quotes:
            author_quotes[author].append(text)
        else:
            author_quotes[author] = [text]
            
    return author_quotes
```
This function will take the `quotes_and_authors` list and return a dictionary where the keys are the authors and the values are lists of quotes by that author.

Then, in your main function declaration replace the code you have with the following:

```python
quotes_and_authors = scrape_quotes()
collection_of_author_quotes = author_quotes_collection(quotes_and_authors)
print(collection_of_author_quotes['Albert Einstein'])
```

#### Testing

Run the program by typing `python main.py` in your terminal.

Notice that we see the quotes by Albert Einstein.

### 10. Saving the Data to a JSON File

While it is not necessary to save the data to a JSON file, it is a good idea to do so for a couple of reasons.

1. It is easier to work with the data later.
2. It is easier to load the data into a database.

Add the following function declaration to the function declaration space below the `author_quotes_collection()` function declaration:

```python
def save_quotes_to_file(quotes):
    with open('quotes.json', 'w') as file:
        json.dump(quotes, file, indent=4)
```
then replace the print statement in the main function with the following:

```python
save_quotes_to_file(collection_of_author_quotes)
```

#### save_quotes_to_file() function explanation (**DO NOT COPY**)

This function will take the `collection_of_author_quotes` dictionary and save it to a file called `quotes.json` in the same directory as your `main.py` file.


```python
with open('quotes.json', 'w') as file:
```
- `with` is a context manager that automatically closes the file when done
- `open()` creates or opens a file named 'quotes.json'
- `'w'` means write mode (will overwrite if file exists)
- `as file` creates a variable name `file` to reference the opened file

```python
    json.dump(quotes, file, indent=4)
```
- `json.dump()` writes Python data to a JSON file using the python dictionary json that we imported.
- `quotes` is the data to write (your list of dictionaries)
- `file` is where to write it (the opened JSON file)
- `indent=4` makes the JSON file human-readable with nice formatting

#### What the Output Looks Like
```json
{
    "author": "Albert Einstein",
    "quotes": [
        "Life is like riding a bicycle. To keep your balance, you must keep moving.",
        "Imagination is more important than knowledge."
    ]
}
```

#### Why Each Part Matters
1. The `with` statement:
   - Safely handles file operations
   - Automatically closes file even if errors occur
   - Prevents memory leaks

2. The `'w'` mode:
   - Creates a new file if it doesn't exist
   - Overwrites the file if it does exist
   - Ensures fresh data each time

3. The `indent=4`:
   - Makes the JSON file easy to read
   - Each level is indented by 4 spaces
   - Helpful for debugging and verification


#### Testing

Run the program by typing `python main.py` in your terminal.

Notice that we see the quotes.json file in the same directory as your `main.py` file.

### 11. Cleaning the Text

Notice that the text contains some unicode characters that we need to clean up. We can do this by creating a `clean_text()` function.

Add the following function declaration to the function declaration space below the `save_quotes_to_file()` function declaration:

```python
def clean_text(text):
    if not text:
        return ""
    
    normalized = unicodedata.normalize('NFKD', text)
    ascii_text = normalized.encode('ascii', 'ignore').decode('ascii')
    cleaned_text = ' '.join(ascii_text.split())
    return cleaned_text
```

#### clean_text() function explanation (**DO NOT COPY**)

```python
import unicodedata
```
- Imports Python's built-in module for Unicode operations
- Provides tools for working with Unicode characters
- No installation needed as it's part of Python's standard library

```python
    if not text:
        return ""
```
- Safety check for empty or None input
- If text is empty, None, or False, returns an empty string
- Prevents errors when processing empty text

```python
    normalized = unicodedata.normalize('NFKD', text)
```
- Takes the input text and applies [NFKD normalization](https://docs.python.org/3/library/unicodedata.html#unicodedata.normalize)
- 'NFKD' tells Python to:
  1. Break down special characters into their base form (K - compatibility)
  2. Separate base characters from their accent marks (D - decomposed)
- For example: 'é' becomes 'e' + '´' (base letter + accent mark)

```python
    ascii_text = normalized.encode('ascii', 'ignore').decode('ascii')
```
This complex line does three things:
1. `encode('ascii', 'ignore')`
   - Converts the string to ASCII bytes
   - 'ignore' tells Python to drop any non-ASCII characters
   - Any character that can't be converted to ASCII is removed

2. `decode('ascii')`
   - Converts the ASCII bytes back into a string
   - Now we have a clean ASCII string

```python
    cleaned_text = ' '.join(ascii_text.split())
```
- `split()` breaks the text into words (splits on any whitespace)
- `join()` puts it back together with single spaces
- Removes extra whitespace, tabs, and newlines
- Normalizes all spacing between words

```python
    return cleaned_text
```
- Returns the final cleaned text
- All special characters have been normalized
- All extra whitespace has been removed

#### Adjust the scrape_quotes() function for loop

Replace the `for quoteDiv in quoteDivs:` loop with the following:

```python
        text = clean_text(quoteDiv.find('span', class_='text').text)
        author = clean_text(quoteDiv.find('small', class_='author').text)
```

We are now cleaning the text before we append it to the `quotes_and_authors` list.


#### Testing

Run the program by typing `python main.py` in your terminal.

Notice that the text is now cleaned up.

### 12. Preparing for the Gemini API Call

#### Building the Gemini API Call

Declare the `setup_api()` function:

We will create a `setup` function to load our API key from the `.env` file as well as initialize and configure our Gemini model which we will later invoke and store in the `model` variable.

Add the following code to the function declaration space below the `clean_text()` function declaration:
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

Also, at the top of your main function call the `setup_api()` function and store the result in the `model` variable:

```python
model = setup_api()
```

#### `setup_api()` Function explanation (**DO NOT COPY**)

```python
load_dotenv()
```
- Loads environment variables from a `.env` file into the program
- This is a security best practice to keep sensitive data like API keys out of the code
- Looks for a file named `.env` in your project directory

```python
api_key = os.getenv('API_KEY')
```
- Gets the API key from environment variables
- `os.getenv()` looks for a variable named 'API_KEY' in the environment
- If not found, returns None instead of raising an error

```python
genai.configure(api_key=api_key)
```
- Configures the Google Generative AI library with your API key
- This is required before you can create any model instances
- Sets up the authentication for all future API calls

```python
model_config = {
    "temperature": 0.2,
    "top_p": 0.95,
    "top_k": 40,
    "max_output_tokens": 4096,
}
```
Creates a dictionary with model parameters:
- `temperature`: 0.2
  - Controls randomness in responses
  - Lower values (like 0.2) make output more focused and deterministic
  - Range is 0-1, where 0 is most deterministic

- `top_p`: 0.95
  - Controls diversity via nucleus sampling
  - Model considers tokens that add up to this probability
  - 0.95 means it considers the top 95% most likely tokens

- `top_k`: 40
  - Limits the number of tokens considered for each step
  - Model only looks at the top 40 most likely next tokens
  - Helps balance between diversity and quality

- `max_output_tokens`: 4096
  - Maximum length of the response
  - 4096 tokens is approximately 3000-4000 words
  - Prevents overly long responses

```python
return genai.GenerativeModel('gemini-1.5-flash', generation_config=model_config)
```
- Creates and returns a new Gemini model instance
- Uses the 'gemini-1.5-flash' model variant (optimized for speed)
- Applies all the configuration settings we defined
- This model instance can be used to generate responses

### 13. Analyzing the Author's Quotes using the Gemini API

We are going to create a function called `analyze_author_quotes()` that will take the `collection_of_author_quotes` dictionary and analyze the quotes for each author and return a list of dictionaries with the author and the analysis.

Add the following function declaration to the function declaration space below the `setup_api()` function declaration:

```python

```

Also, in your main function replace the print statement with the following:

```python
author_sentiments = analyze_author_sentiments(collection_of_author_quotes, model)
print(author_sentiments['Albert Einstein'])
```

#### analyze_author_sentiments() function explanation (**DO NOT COPY**)

# Understanding the Sentiment Analysis Function Structure

- The Prompt Engineering
```python
prompt = """Analyze the overall sentiment and style for each author based on their quotes.

    For each author provide:
    * Overall emotional tone of their quotes
    * Common themes or subjects
    * Brief explanation of the reasoning
    * Writing style characteristics
    * Notable patterns in word choice or structure

    Format your response as:
    
    AUTHOR NAME:
    - Sentiment: [sentiment]
    - Explanation: [explanation]
    - Examples: [examples]

    Here are the authors and their quotes:\n\n"""
```
This prompt uses several key techniques:
- **Clear Instructions**: Tells the model exactly what to analyze
- **Structured Output Format**: Specifies how to format the response
- **Bullet Points**: Makes the required information clear and organized
- **Template Markers**: Uses [placeholder] to show where specific info should go
- **Hierarchical Structure**: 
  1. First states what to do
  2. Then lists what to provide
  3. Then shows how to format it
  4. Finally indicates where the data will be

- Building One Large Prompt (Batch Processing)
```python
for author, quotes in author_quotes.items():
    prompt += f"{author}:\n"
    for quote in quotes:
        prompt += f"- {quote}\n"
    prompt += "\n"
```
This loop is crucial for efficiency because:
- **Batches All Authors**: Instead of making one API call per author, we combine everything into one prompt
- **Cost Effective**: API services often charge per call, so fewer calls = lower costs
- **Rate Limit Friendly**: Most APIs have rate limits (e.g., max calls per minute)
  - Example: If you have 100 authors and a rate limit of 60 calls/minute:
    - Individual calls: Would take 2+ minutes and might hit rate limits
    - Batched call: One instant call, no rate limit issues

- Getting and Returning the Raw Response
```python
    try:
        response = model.generate_content(prompt)
        return response.text
```
- Makes single API call with complete prompt
- Returns unprocessed text exactly as received
- Preserves formatting and structure from API response
- Simple and maintainable approach

- Error Handling
```python
    except Exception as e:
        print(f"Error in sentiment analysis: {e}")
        return ""
```
- Catches any API call issues
- Provides clear error message
- Returns empty string instead of crashing
- Allows calling code to handle failures gracefully

The key efficiency of this function comes from building one large, well-structured prompt instead of making multiple API calls. This approach is like shipping one full truck instead of making multiple trips with a partially filled truck - more efficient, cost-effective, and less likely to hit road (rate) limits.


#### Testing

Run the program by typing `python main.py` in your terminal.

Notice that we see the analysis for each author.

### 14. Saving the Analysis to a text File


We can now create a function called `save_sentiment_analysis_to_text()` that will take the `author_sentiments` string    and save it to a text file.

Add the following function declaration to the function declaration space below the `analyze_author_sentiments()` function declaration:

```python
def save_sentiment_analysis_to_text(sentiments):
    try:
        with open('sentiments.txt', 'w') as file:
            file.write(sentiments)  # Simply write the string directly
    except Exception as e:
        print(f"Error saving sentiments to text file: {e}")
```
This function has the same structure as the `save_quotes_to_file()` function.

#### Testing

Run the program by typing `python main.py` in your terminal.

Notice that we see the sentiments.txt file in the same directory as your `main.py` file.

#### Questions

- notice the formatting of the writing in the sentiments.txt file. What are some ways in which you might improve the formatting?
- are there other ways in which you might scrape the data to use in different ways?
- are there other ways in which you might use the Gemini API to analyze the data?

### 15. Conclusion

In this lesson, we learned how to scrape a website, clean the data, and use the Gemini API to analyze the data. We also learned how to save the data to a JSON file and a text file.

