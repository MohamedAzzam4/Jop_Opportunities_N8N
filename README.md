# n8n LinkedIn Job Scraper & AI Parser

An automated n8n workflow that scrapes job listings from LinkedIn, cleans the data using a hybrid Regex/AI approach, and structures it for analysis and storage in Google Sheets.

## 🚀 Introduction & Motivation

This project began as a personal journey to master **n8n**, a powerful low-code automation tool in high demand for freelance and professional projects. The goal was to build a real-world, scalable solution to a common problem: gathering and structuring job market data.

Along the way, this project became an exploration of the "No-Code/Low-Code" paradigm, revealing key insights into what it truly takes to build robust and reliable automations.

## ✨ Key Learnings: The "No-Code" Myths vs. Reality

My initial assumptions about no-code tools were quickly challenged. This project is a practical demonstration of two key realities:

1.  **The Myth of "Easy" No-Code:** While building a simple workflow is straightforward, creating a production-ready system that handles inconsistent data and unexpected errors is a significant engineering challenge. A $500 price tag for a robust workflow is not surprising when you account for the logic, error handling, and testing required.
2.  **"No-Code" is Actually "Low-Code":** To unlock the true power of n8n and handle complex data transformations, a foundational understanding of programming concepts and direct use of code (in this case, JavaScript) is essential. A developer's mindset for problem-solving and debugging is a massive advantage.

Ultimately, the most powerful solution was not a pure no-code or pure-code approach, but a **hybrid strategy**, which this workflow is built upon.

## 📋 Features

-   **Automated Scraping:** Kicks off with a LinkedIn search URL and scrapes the initial list of jobs.
-   **Deep Scraping:** Loops through each job link found to scrape its detailed description page.
-   **Intelligent Data Cleaning:** A multi-stage process to clean and structure the raw scraped data.
-   **Hybrid Extraction Engine:**
    -   **Regex Pre-processing:** A JavaScript-based cleaner first removes boilerplate "noise" from the raw text, significantly reducing the data size and API costs.
    -   **LLM Structuring:** The cleaned text is then passed to the Google Gemini API with a detailed prompt to accurately extract and structure the job details into a clean JSON format.
-   **Status Monitoring:** The final output includes a boolean flag (`extraction_successful`) to easily identify and handle jobs that failed to parse correctly.
-   **Data Storage:** Appends the final, structured job data as a new row in a Google Sheets spreadsheet.

## 🛠️ Tech Stack & n8n Nodes

-   **Core Automation:** n8n (self-hosted)
-   **Web Scraping:** `HTTP Request` Node or a dedicated scraper node.
-   **Data Processing & Cleaning:** `Code` Node (JavaScript with Regular Expressions).
-   **AI-Powered Structuring:** `Google Gemini` Node (can be swapped for any LLM).
-   **Looping:** `Split in Batches` Node.
-   **Conditional Logic:** `IF` Node.
-   **Data Storage:** `Google Sheets` Node.

## 🌊 Workflow Overview

The automation pipeline is designed to be robust and logical:

1.  **Get Job List:** The workflow starts, scraping a LinkedIn search results page to get a list of jobs with their basic info and individual URLs.
2.  **Loop Over Jobs:** The `Split in Batches` node iterates through the list, processing one job at a time.
3.  **Get Job Details:** An `HTTP Request` node visits the URL for the current job and scrapes its full page content.
4.  **Clean & Extract (The Core Function):**
    -   A `Code` node receives the raw text.
    -   It first extracts consistently formatted metadata (applicants count).
    -   It then uses start and end markers (e.g., "Report this job" and "Seniority level") to isolate the core job description, removing headers and footers.
    -   This cleaned `details` blob, the metadata, and a success flag are returned as a JSON object.
5.  **Structure with AI (Optional but Recommended):** A `Gemini` node receives the cleaned `details` and, using a sophisticated prompt, transforms the text into a structured JSON object with keys like `responsibilities`, `qual
