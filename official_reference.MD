         

Quickstart #1: basic scraping that turns a webpage into markdown
↑ Back to Top

Simple scraping without extraction costs 1 API credit per API call

Basic scraping that ingests an URL and outputs markdown content and other essential information such as page metadata, multimedia content, links, etc.

fetch('https://www.crawl4ai-cloud.com/query', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({
        url: 'https://www.kidocode.com/degrees/technology',
        apikey: "013ed7cfa4723cfe38ea"
    })
})
.then(response => response.json())
.then(data => console.log(data));

Quickstart #2: scrape + extract with LLM-based extraction
↑ Back to Top

Scraping + extraction costs 2 API credits per API call

Scrape + extract with LLM-based extraction logic. 🦾Recommended for use cases that require reliable extraction results but the underlying webpage structure frequently and unpredictably changes


// Define schema for structured extraction
const llmSchema = {
    course_name: 'name of the course offered'
};

// Instruction for LLM extraction
const llmInstruction = `Extract the the course name of each item listed in the Explore our future-forward courses section. The extraction should look like:
{'course_name':'Coding with Python'}`;

// Make request to public API
fetch('https://www.crawl4ai-cloud.com/query', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({
        url: 'https://www.kidocode.com/degrees/technology',
        apikey: "013ed7cfa4723cfe38ea",
        llm_instruction: llmInstruction,
        llm_schema: llmSchema,
        cache_mode: 'bypass'
    })
})
.then(response => response.json())
.then(data => console.log(data.extractions));

Quickstart #3: scrape + extract with JSON CSS extraction & schema
↑ Back to Top

Scraping + extraction costs 2 API credits per API call

Extract structured data from web pages using CSS selectors and optional JavaScript pre-processing, with no LLM needed. ⚡Recommended for use cases that value fast extraction on mostly static webpage structures.

const url = 'https://www.kidocode.com/degrees/technology';

// JavaScript to execute before extraction
const js_code = `
(async () => {
    const tabs = document.querySelectorAll("section.charge-methodology .tabs-menu-3 > div");

    for(let tab of tabs) {
        // scroll to the tab
        tab.scrollIntoView();
        tab.click();
        // Wait for content to load and animations to complete
        await new Promise(r => setTimeout(r, 500));
    }
})();
`;

// Define extraction schema using CSS selectors
const json_css_schema = {
    name: "KidoCode Courses",
    baseSelector: "section.charge-methodology .div-block-214.p-extraxx",
    fields: [
        {
            name: "section_title",
            selector: "h3.heading-50",
            type: "text",
        },
        {
            name: "section_description",
            selector: ".charge-content",
            type: "text",
        },
        {
            name: "course_name",
            selector: ".text-block-93",
            type: "text",
        },
        {
            name: "course_description",
            selector: ".course-content-text",
            type: "text",
        },
        {
            name: "course_icon",
            selector: ".image-92",
            type: "attribute",
            attribute: "src"
        }
    ]
};

// Make API request
fetch('https://www.crawl4ai-cloud.com/query', {
    method: 'POST',
    headers: {
        'Content-Type': 'application/json'
    },
    body: JSON.stringify({
        url: url,
        apikey: "013ed7cfa4723cfe38ea",
        js_code: js_code,
        json_css_schema: json_css_schema,
        cache_mode: 'bypass'
    })
})
.then(response => response.json())
.then(data => console.log(data.extractions));

API Parameters: Details & Examples
Output Format Options
↑ Back to Top

Control the format of the crawled content using the output_format parameter. Review detailed documentation on the 4 output options: html, cleaned_html, markdown, fit_markdown.

import requests

url = 'https://www.kidocode.com/degrees/technology'

# Make API request with output format
response = requests.post(
    'https://www.crawl4ai-cloud.com/query',
    json={
        'url': url,
        'apikey': '013ed7cfa4723cfe38ea',
        'output_format': 'html'  # Options: html, cleaned_html, markdown, fit_markdown
    }
)
print(response.json())

Page Interaction & JS Code Execution
↑ Back to Top

Execute custom JavaScript code on the target page before extraction

import requests

# JavaScript code to execute on the page
js_code = """
window.scrollTo(0, document.body.scrollHeight);
document.querySelector('#fr-submit-btn').click();
"""

response = requests.post(
    'https://www.crawl4ai-cloud.com/query',
    json={
        'url': 'https://www.kidocode.com/franchise',
        'apikey': '013ed7cfa4723cfe38ea',
        'js_code': js_code,
        'cache_mode': 'bypass'
    }
)
print(response.json())

JavaScript Code Features:

    ✓ Execute custom JavaScript on target page
    ✓ Interact with page elements (click, scroll, etc.)
    ✓ Modify page content before extraction
    ✓ Wait for dynamic content to load

Perfect for interactive pages that require user actions before content is available
Magic Mode
↑ Back to Top

Enable comprehensive anti-bot protection bypass with the magic parameter. Review detailed documentation.

import requests

url = 'https://www.kidocode.com/degrees/technology'

response = requests.post(
    'https://www.crawl4ai-cloud.com/query',
    json={
        'url': url,
        'apikey': '013ed7cfa4723cfe38ea',
        'magic': True  # Enables all anti-detection features
    }
)
print(response.json())

Magic Mode Features:

    ✓ Masks browser automation signals
    ✓ Simulates human-like behavior
    ✓ Handles cookie consent popups
    ✓ Manages browser fingerprinting

Processing iFrames
↑ Back to Top

Enable crawling of content within iframes:

import requests

url = 'https://www.kidocode.com/degrees/technology'

response = requests.post(
    'https://www.crawl4ai-cloud.com/query',
    json={
        'url': url,
        'apikey': '013ed7cfa4723cfe38ea',
        'process_iframes': True  # Include content from iframes
    }
)
print(response.json())

iFrame Processing:

    ✓ Crawls content within embedded iframes
    ✓ Disabled by default for faster crawling
    ✓ Useful for sites with embedded content
    ✓ May increase total crawl time

Removing Overlay Elements
↑ Back to Top

Remove popups, ads, and other overlay elements during crawling:

import requests

url = 'https://www.kidocode.com/degrees/technology'

response = requests.post(
    'https://www.crawl4ai-cloud.com/query',
    json={
        'url': url,
        'apikey': '013ed7cfa4723cfe38ea',
        'remove_overlay_elements': True  # Remove popups and overlays
    }
)
print(response.json())

Overlay Removal Features:

    ✓ Removes advertisement overlays
    ✓ Clears cookie consent notices
    ✓ Eliminates modal popups
    ✓ Improves content extraction

Excluding HTML Tags
↑ Back to Top

Filter out specific HTML elements during content extraction:

import requests

url = 'https://www.kidocode.com/degrees/technology'

response = requests.post(
    'https://www.crawl4ai-cloud.com/query',
    json={
        'url': url,
        'apikey': '013ed7cfa4723cfe38ea',
        'excluded_tags': ['nav', 'form']  # Ignore navigation and forms
    }
)
print(response.json())

Tag Exclusion Features:

    ✓ Filter out irrelevant HTML elements
    ✓ Focus extraction on main content
    ✓ Customize content processing
    ✓ Default: process all tags (empty list)

Waiting for DOM Element to Load
↑ Back to Top

Specify elements to wait for before processing the page:

import requests

url = 'https://www.kidocode.com/degrees/technology'

response = requests.post(
    'https://www.crawl4ai-cloud.com/query',
    json={
        'url': url,
        'apikey': '013ed7cfa4723cfe38ea',
        'wait_for': 'css:.dynamic-content'  # Wait for element to appear
    }
)
print(response.json())

Wait For Features:

    ✓ Wait for AJAX-loaded content
    ✓ Support for CSS and XPath selectors
    ✓ Ensures complete page loading
    ✓ Perfect for dynamic web apps

Format: Use "css:.selector" for CSS selectors or "xpath://div" for XPath expressions
CSS Selector Targeting
↑ Back to Top

Focus content extraction on specific page elements:

import requests

url = 'https://www.kidocode.com/degrees/technology'

response = requests.post(
    'https://www.crawl4ai-cloud.com/query',
    json={
        'url': url,
        'apikey': '013ed7cfa4723cfe38ea',
        'css_selector': '.margin-bottom-24px'  # Only process matching elements
    }
)
print(response.json())

CSS Selector Features:

    ✓ Target specific page elements
    ✓ Extract only relevant content
    ✓ Reduce processing overhead
    ✓ Improve extraction accuracy

Use standard CSS selector syntax to identify target elements
Word Count Threshold
↑ Back to Top

Filter content blocks based on minimum word count:

import requests

url = 'https://www.kidocode.com/degrees/technology'

response = requests.post(
    'https://www.crawl4ai-cloud.com/query',
    json={
        'url': url,
        'apikey': '013ed7cfa4723cfe38ea',
        'word_count_threshold': 10  # Minimum words per content block
    }
)
print(response.json())

Word Count Features:

    ✓ Filter out short text snippets
    ✓ Focus on substantial content
    ✓ Improve extraction quality
    ✓ Reduce noise in results

Set threshold based on your content requirements
Screenshot Capture
↑ Back to Top

Take screenshots of web pages with optional delay:

import requests
import base64

url = 'https://www.kidocode.com/degrees/technology'

response = requests.post(
    'https://www.crawl4ai-cloud.com/query',
    json={
        'url': url,
        'apikey': '013ed7cfa4723cfe38ea',
        'screenshot': True,  # Take screenshot
        'screenshot_wait_for': 2.0  # Wait 2 seconds before capture
    }
)

# Save screenshot to file
data = response.json()
with open("screenshot.png", "wb") as f:
    f.write(base64.b64decode(data['screenshot']))

Screenshot Features:

    ✓ Capture full page screenshots
    ✓ Optional delay before capture
    ✓ Returns base64 encoded PNG
    ✓ Perfect for visual verification

Use screenshot_wait_for parameter to ensure dynamic content is loaded

Note: Due to payload size and latency considerations, LLM extraction and JSON-CSS extraction will be disabled when screenshot is enabled.
Cache Control
↑ Back to Top

Control caching behavior for content retrieval:

import requests

url = 'https://www.kidocode.com/degrees/technology'

response = requests.post(
    'https://www.crawl4ai-cloud.com/query',
    json={
        'url': url,
        'apikey': '013ed7cfa4723cfe38ea',
        'cache_mode': 'bypass'  # Always fetch fresh content
    }
)
print(response.json())

Cache Control Features:

    ✓ Default caching enabled for performance
    ✓ Optional bypass for fresh content
    ✓ Trade-off between speed and freshness
    ✓ Useful for frequently updated content

Note: Using cache bypass may increase response times
LLM Instruction and Schema
↑ Back to Top

Extract structured data using LLM instructions and optional schema definitions:

import requests

url = 'https://www.kidocode.com/degrees/technology'

# Define schema for structured extraction
llm_schema = {
    'course_name': 'name of the course offering',
    'course_description': 'description of the course offering',
}

# Instruction for LLM extraction
llm_instruction = '''Extract the course_name and course_description of each course.'''

# Make API request
response = requests.post(
    'https://www.crawl4ai-cloud.com/query',
    json={
        'url': url,
        'apikey': '013ed7cfa4723cfe38ea',
        'llm_instruction': llm_instruction,
        'llm_schema': llm_schema,
        'input_format': 'markdown', # valid values are markdown (default), fit_markdown, and html
        'cache_mode': 'bypass'
    }
)
print(response.json()['extractions'])

LLM Parameters:

    llm_instruction - Natural language prompt for extraction (max 200 tokens)
    llm_schema - Optional dictionary defining expected fields and their descriptions
    input_format - Optional parameter that specified which page content is fed to the LLM for extraction. By default, input_format is set to "markdown", meaning the page's markdown is fed to the LLM. You can also set the parameter to "fit_markdown" or "html". The "fit_markdown" setting in particular can drastically reduce the number of tokens sent to LLMs (if you trust the underlying markdown filtering logic).

Note: If no schema is provided, the LLM will infer the structure from the instruction.
JSON CSS based Extraction
↑ Back to Top

The JSON-CSS-based extraction is a powerful feature of Crawl4AI that allows you to extract structured data from web pages using CSS selectors. This method is particularly useful when you need to extract specific data points from a consistent HTML structure, such as tables or repeated elements. Here's how to use it with the AsyncWebCrawler. All you need is to define a schema that specifies: 1. A base CSS selector for the repeating elements 2. Fields to extract from each element, each with its own CSS selector. This strategy is fast and efficient, as it doesn't rely on external services like LLMs for extraction.

import requests
import json

url = 'https://www.kidocode.com/degrees/technology'


# Define extraction schema using CSS selectors
json_css_schema = {
    "name": "KidoCode Courses",
    "baseSelector": "section.charge-methodology .div-block-214.p-extraxx",
    "fields": [
        {
            "name": "section_title",
            "selector": "h3.heading-50",
            "type": "text",
        },
        {
            "name": "section_description",
            "selector": ".charge-content",
            "type": "text",
        },
        {
            "name": "course_name",
            "selector": ".text-block-93",
            "type": "text",
        },
        {
            "name": "course_description",
            "selector": ".course-content-text",
            "type": "text",
        },
        {
            "name": "course_icon",
            "selector": ".image-92",
            "type": "attribute",
            "attribute": "src"
        }
    ]
}

# Make API request
response = requests.post(
    'https://www.crawl4ai-cloud.com/query',
    json={
        'url': url,
        'apikey': '013ed7cfa4723cfe38ea',
        'json_css_schema': json_css_schema,
        'cache_mode': 'bypass'
    }
)
print(response.json()['extractions'])

Utiliy/Helper API: CSS Schema Generator
↑ Back to Top

Don't want to manually create JSON CSS schema? You can use this utility/helper API to turn raw HTML content into the corresponding JSON CSS schema.

import requests

html = """
<div class="product-card">
    <h2 class="title">Gaming Laptop</h2>
    <div class="price">$999.99</div>
    <div class="specs">
        <ul>
            <li>16GB RAM</li>
            <li>1TB SSD</li>
        </ul>
    </div>
</div>
"""

# Make API request
response = requests.post(
    'https://www.crawl4ai-cloud.com/query',
    json={
        'utility_mode': 'json_css_schema_generator',
        'html':html,
        'apikey': '013ed7cfa4723cfe38ea',
    }
)
print(response.json())