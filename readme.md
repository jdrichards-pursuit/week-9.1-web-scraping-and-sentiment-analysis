# Introduction to Web Scraping and Sentiment Analysis

<img src="assets/web-scrape.webp" alt="Web Scraping" width="500">

## What is Web Scraping?

Web scraping is the automated process of extracting information from websites programmatically. Instead of manually copying and pasting data from web pages, we use specialized programs (scrapers) to automatically collect the data we need. These scrapers can be programmed to visit web pages, locate specific elements, and extract the desired information into a structured format.

## Why Use Web Scraping When APIs Exist?

While many websites offer APIs (Application Programming Interfaces) for data access, web scraping remains valuable for several reasons:

1. **Limited or No API Available**: Many websites don't provide APIs for their data
2. **API Restrictions**: When APIs exist, they might have:
   - Rate limiting
   - Cost barriers
   - Limited data access
   - Required authentication
3. **Historical Data**: APIs often limit access to recent data only
4. **Flexibility**: Scraping allows you to gather exactly the data you need, in the format you want

## Real-World Applications

### Business Intelligence
- Monitoring competitor pricing
- Tracking product availability
- Gathering customer reviews and sentiment
- Market trend analysis

### Research and Academia
- Collecting social media data for studies
- Gathering scientific publication information
- Tracking changes in online content over time
- Building datasets for machine learning

### Financial Analysis
- Real-time stock price monitoring
- Economic indicators tracking
- Financial news aggregation
- Investment research

### Content Aggregation
- News compilation
- Job listing aggregation
- Real estate listings
- Price comparison services

## Popular Web Scraping Packages

Web scraping tools can be categorized based on language and primary use case. Here's a comprehensive overview:

### Python Libraries

- **Requests**: Industry-standard HTTP library for making web requests
- **Beautiful Soup 4**: Efficient, user-friendly HTML/XML parser
- **Scrapy**: Enterprise-grade web crawling framework
- **Selenium**: Complete browser automation framework


### JavaScript Libraries

- **cheerio**: Fast, flexible implementation of core jQuery for parsing
- **Crawlee**: Modern web scraping and browser automation library
- **Puppeteer**: Headless Chrome Node.js API by Google


### Our Core Tools

For this course, we'll focus on two powerful and complementary libraries:

#### 1. Beautiful Soup 4 ([`bs4`](https://www.crummy.com/software/BeautifulSoup/bs4/doc/))
- Efficient HTML/XML parsing library
- Low resource usage compared to alternatives
- Simple and intuitive API
- Great for static content
- Key features:
  - Easy navigation of HTML trees
  - Powerful searching and filtering
  - Robust parsing of malformed HTML
  - Multiple parser support

Example usage:
```python
from bs4 import BeautifulSoup
# Parse HTML content
soup = BeautifulSoup(html_content, 'html.parser')
# Find elements
results = soup.find_all('div', class_='product-item')
```

#### 2. Selenium ([`selenium`](https://www.selenium.dev/documentation/))
- Web browser automation framework
- Essential for dynamic content
- Handles JavaScript-rendered pages
- Key features:
  - Browser automation (clicking, typing, scrolling)
  - Wait for elements to load
  - Handle dynamic content
  - Handle pagination


Example usage:
```python
from selenium import webdriver
from selenium.webdriver.common.by import By

# Initialize browser
driver = webdriver.Chrome()
# Navigate to page
driver.get("https://example.com")
# Click next button
next_button = driver.find_element(By.CLASS_NAME, "pagination-next")
next_button.click()
```

### Why This Combination?

1. **Complementary Strengths**
   - Beautiful Soup: Fast and efficient for parsing HTML
   - Selenium: Handles dynamic content and user interactions

2. **Resource Optimization**
   - Use Beautiful Soup when possible (static content)
   - Switch to Selenium only when needed (dynamic content)

3. **Learning Curve**
   - Both have excellent documentation
   - Large community support
   - Plenty of examples and tutorials

## \*\*Important\*\*: Ethical Considerations and Best Practices

Laws around web scraping are complex and vary by country. In the United States, the [CAN-SPAM Act](https://www.ftc.gov/business-guidance/resources/can-spam-act-compliance-guide-business) and the [Computer Fraud and Abuse Act (CFAA)](https://en.wikipedia.org/wiki/Computer_Fraud_and_Abuse_Act) are relevant. The CAN-SPAM Act generally allows scraping for legitimate business purposes, provided it complies with the Act's requirements. The CFAA prohibits unauthorized access to a protected computer, but exceptions exist for authorized scraping activities.

### Before You Scrape
1. **Check robots.txt**: Always examine the website's robots.txt file first
   - Located at `website.com/robots.txt` (Take a look at `https://www.techcrunch.com/robots.txt`)
   - Specifies which parts of the site can be scraped
   - Indicates crawl rate limits

2. **Review Terms of Service**
   - Look for specific mentions of data collection
   - Check if scraping is explicitly prohibited
   - Understand usage restrictions on collected data

3. **Respect the Website**
   - Implement reasonable delays between requests
   - Don't overload the server
   - Only collect publicly available data

### Legal Considerations
- Scraping publicly available data is generally legal
- Avoid collecting personal or private information
- Consider copyright and data ownership
- Be aware of regional data protection laws (GDPR, CCPA)

## Technical Best Practices

1. **Rate Limiting**
   - Implement delays between requests
   - Use exponential backoff when encountering errors
   - Respect the website's crawl limits

2. **Error Handling**
   - Handle network timeouts gracefully
   - Account for missing or changed data
   - Log errors for debugging

3. **Data Quality**
   - Validate extracted data
   - Handle edge cases and exceptions
   - Clean and normalize data appropriately


## Common Challenges

1. **Dynamic Content**
   - JavaScript-rendered content
   - AJAX requests
   - Infinite scrolling
   - Pagination

2. **Anti-Scraping Measures**
   - CAPTCHA systems
   - IP blocking
   - Rate limiting
   - Dynamic HTML structures

3. **Site Changes**
   - HTML structure modifications
   - Class and ID changes
   - Regular layout updates
  
## Sentiment Analysis in Web Scraping Projects

### What is Sentiment Analysis?

Sentiment analysis, often called opinion mining, is a technique that determines the emotional tone and attitude expressed in text. Think of it as teaching computers to understand human emotions in written words. At its core, sentiment analysis works by breaking down text into smaller pieces (like words or phrases) and evaluating them against pre-existing databases of words with known emotional weights. For example, words like "excellent," "happy," and "wonderful" carry positive sentiment scores, while words like "terrible," "angry," and "disappointed" carry negative ones. However, modern sentiment analysis goes far beyond simple word matching. Advanced systems, especially those powered by large language models like GPT or Gemini, can understand context, sarcasm, and complex emotional nuances. They recognize that a phrase like "this movie was so bad it was good" is actually positive despite containing negative words, or that "the customer service was as helpful as a chocolate teapot" is sarcastic criticism. Sentiment can be calculated on different scales: some systems use simple positive/negative/neutral classifications, while others might score on a continuous scale (like -1 to +1) or detect specific emotions (joy, anger, sadness, etc.). The real power of sentiment analysis comes from its ability to process massive amounts of text data - like thousands of customer reviews or social media posts - and provide actionable insights about how people feel about products, services, or topics.

### Common Applications with Scraped Data
- Product reviews analysis
- Social media monitoring
- Customer feedback processing
- Competition analysis
- Customer support improvement

### Traditional Sentiment Analysis Packages

Before having LLMs, we used popular Python packages for sentiment analysis including:
- **NLTK**: Natural Language Toolkit
- **spaCy**: Industrial-strength NLP
- **TextBlob**: Simple interface for common NLP tasks

### Why Use Gemini API Instead of Packages?

#### Advantages of Gemini API

1. **Superior Understanding**
   - Better grasp of context and nuance
   - Understands sarcasm and implied meaning
   - Handles informal language and slang effectively
   - More accurate with mixed sentiments

2. **Language Capabilities**
   - Excellent multilingual support
   - Understands cultural context
   - Processes various dialects and regional expressions
   - Handles code-switching naturally

3. **Advanced Features**
   - Multi-label sentiment classification
   - Aspect-based sentiment analysis
   - Emotion detection beyond positive/negative
   - Confidence scores for predictions

4. **Practical Benefits**
   - No model training required
   - Regular updates and improvements
   - Scalable processing
   - Consistent results

#### Limitations of Traditional Packages

1. **Limited Context Understanding**
   - Often miss subtle nuances
   - Struggle with sarcasm
   - May misinterpret complex sentences
   - Limited understanding of context

2. **Binary/Basic Classification**
   - Usually limited to positive/negative/neutral
   - Miss nuanced emotional states
   - Struggle with mixed sentiments
   - Limited granularity in analysis

3. **Training Requirements**
   - Need large datasets for custom domains
   - Require regular model updates
   - Domain-specific training needed
   - Resource-intensive

4. **Language Limitations**
   - Often English-centric
   - Limited multilingual support
   - Struggle with informal language
   - Poor handling of regional variations


### Best Practices for Sentiment Analysis

1. **Data Preparation**
   - Clean and preprocess text data
   - Remove irrelevant information
   - Handle special characters appropriately
   - Normalize text format

2. **API Usage**
   - Implement rate limiting - use as few API calls as possible to avoid costs and rate limits
   - Handle API errors gracefully
   - Cache results when appropriate

3. **Analysis Approach**
   - Consider context in prompts
   - Use consistent evaluation criteria
   - Handle edge cases
   - Validate results



