# n8n LinkedIn Job Scraping & AI-Powered Parsing Pipeline

This repository contains an n8n workflow designed to automate the process of scraping job opportunities from LinkedIn, intelligently cleaning and structuring the data, and storing it in Google Sheets.

## Project Goal & Design Philosophy

The primary objective was to build a robust pipeline to automate the following steps:
1.  **Gather Job Listings:** Scrape a list of jobs from a LinkedIn search.
2.  **Initial Cleaning:** Parse the initial list to extract individual job links and basic metadata.
3.  **Deep Dive:** Visit each job link to scrape its detailed description.
4.  **Detailed Cleaning & Structuring:** Clean the detailed description and structure it into a usable format.
5.  **Store:** Save the final, clean data in an organized Google Sheet.

A core focus of this project was **data quality and integrity**. The goal was to minimize data loss at every step and create a reliable, scalable system.

## The Core Challenge: The Evolution of a Data Parsing Strategy

The main technical challenge was clear: **How can I reliably and affordably clean and structure the messy, inconsistent data from various job postings into a clean JSON object?**

I explored three distinct methodologies to solve this problem.

### Solution v1: The LLM-Only Approach

The initial and most obvious solution was to leverage a Large Language Model (Gemini) for both cleaning and structuring the data.

-   **Result:** This method produced highly accurate and well-structured JSON data.
-   **Problem:** The cost was a major issue. Sending the entire raw HTML/Markdown of each job posting resulted in a very high token count, making the solution impractical and expensive to scale. The key takeaway was the need to reduce token consumption.

### Solution v2: The Regex-Only Approach

The second iteration aimed to completely eliminate API costs by relying solely on Regular Expressions.

-   **Result:** The financial cost became zero, and data extraction accuracy reached about 80%.
-   **Problem:** An 80% success rate is unacceptable for a production-grade system. While a higher accuracy (>95%) might be possible with more complex Regex, developing a truly generic script that could handle all the different job post layouts proved to be extremely time-consuming and difficult to maintain, even with AI assistance for writing the patterns.

### Solution v3: The Hybrid Approach (Regex + LLM) - The Final Solution

The final and most effective solution combines the strengths of the previous two approaches.

1.  **Regex for Pre-Processing:** A robust JavaScript script in an n8n `Code` node performs an initial cleaning pass. It uses reliable markers to isolate the core job description, stripping away the majority of the "noise." This step **dramatically reduces the number of tokens** sent to the AI.
2.  **LLM for Structuring:** The smaller, cleaner text is then passed to a Gemini node. With a highly specific prompt, the LLM's task is now much simpler and cheaper: structure the clean text into a perfect JSON object.

This hybrid model proved to be the optimal solution, achieving **high accuracy (>95%)** while keeping the **API costs low and manageable**.

## Tech Stack & Key Tools

-   **Automation Core:** n8n (self-hosted)
-   **Web Scraping:** The standard `HTTP Request` node was insufficient for scraping dynamic JavaScript-heavy pages like LinkedIn. To solve this, the workflow uses the **Jina AI Reader API**. By prepending `https://r.jina.ai/` to any URL, it acts as a powerful, simplified scraping layer that returns clean Markdown content.
-   **Data Cleaning (Pre-processing):** `Code` Node with custom JavaScript and Regular Expressions.
-   **Data Structuring:** `Google Gemini` Node.
-   **Data Storage:** `Google Sheets` Node.
-   **Workflow Logic:** `Split in Batches` for looping, `IF` node for error handling.

## Future Improvements

-   **Telegram Integration:** The next logical step is to connect the workflow to a Telegram bot. This would allow a user to trigger the entire process by simply sending a message with a job title (e.g., "Machine Learning Engineer in Cairo"), which would then be used to dynamically construct the search URL and start the scraping pipeline.
-   **Additional Data Destinations:** Add support for saving data to other platforms like Notion, Airtable, or a SQL database.
-   **Scheduled Triggers:** Configure the workflow to run automatically on a set schedule (e.g., daily) to continuously gather new job postings.
