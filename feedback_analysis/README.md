# Amazon Customer Feedback Analysis and Automated Response

## Project Overview

This project analyzes customer reviews from an Amazon product review dataset and prepares personalized customer-support email drafts for critical reviews. It was developed as part of the **Imarticus Data Science Internship Assessment — Python Foundations & Generative AI**.

The workflow includes:
1. Loading and inspecting the Amazon reviews dataset.
2. Cleaning missing values and normalizing review text.
3. Identifying critical reviews using a rating threshold.
4. Counting common complaint keywords.
5. Selecting a small set of detailed negative reviews.
6. Generating apology email drafts with the Groq API.
7. Using a fallback email template if the API request fails.
8. Exporting analysis results to CSV files.

> **Important:** The Groq API has usage limits. If a request fails because of quota, network, model availability, or another API error, the project can use template-based fallback emails. These fallback drafts are not AI-generated.

## Files

Place these files in the same project folder:

```text
Amazon-Customer-Feedback-Project/
├── groq Ai.ipynb
├── amazon.csv
├── gem.env
└── README.md
```

The notebook creates these output files when run:

```text
critical_reviews.csv
complaint_keyword_summary.csv
generated_response_emails.csv
```

## Requirements

- Python 3.9 or later (a recent supported Python version is recommended)
- Jupyter Notebook or JupyterLab
- An internet connection for Groq API requests
- A Groq API key

## Installation

Run the following in a notebook cell:

```python
%pip install pandas groq python-dotenv
```

If your notebook also uses IPython display functions, they are normally available in Jupyter.

## Dataset

The notebook expects a file named `amazon.csv` in the current notebook folder. It uses fields such as:

- `product_name`
- `rating`
- `review_title`
- `review_content`
- `user_name`
- `product_id`
- `review_id`

Make sure the dataset includes the columns required by the notebook. The rating column is converted to numeric form, and rows with missing ratings or review content are removed.

## Configure the Groq API Key

1. Create or sign in to your account at [Groq Console](https://console.groq.com/).
2. Create an API key.
3. In the same folder as the notebook, create a file named `gem.env`.
4. Add the following line, replacing the placeholder with your own key:

```text
GROQ_API_KEY=your_groq_api_key_here
```

Keep your API key private. Do not paste it into the notebook, commit it to GitHub, or share `gem.env`.

The Groq setup in the notebook should load the key like this:

```python
import os
from dotenv import load_dotenv
from groq import Groq

load_dotenv("gem.env", override=True)
api_key = os.getenv("GROQ_API_KEY")

if not api_key:
    raise ValueError("GROQ_API_KEY was not found in gem.env.")

client = Groq(api_key=api_key)
```

The notebook's email-generation function should use a model currently available to your Groq account. Check [Groq's model documentation](https://console.groq.com/docs/models) if you get a model-not-found error.

## How to Run

1. Put `amazon.csv` and `gem.env` in the same folder as the notebook.
2. Open `groq Ai.ipynb` in Jupyter Notebook or JupyterLab.
3. Run the cells from top to bottom.
4. Review the data-cleaning output and critical-review summary.
5. Review the complaint keyword counts.
6. Check the generated email drafts and their source labels.
7. Open the exported CSV files for further analysis.

## Project Methodology

### 1. Data Cleaning
- Checks that required columns exist.
- Reports missing values.
- Removes rows with missing ratings or review content.
- Converts ratings to numeric values.
- Keeps ratings within the 1–5 range.
- Combines review titles and review content into cleaned text for keyword analysis.

### 2. Critical Review Filtering
Reviews rated **3 or lower** are treated as critical reviews. This is a rule-based threshold, not a machine-learning classification model.

### 3. Complaint Keyword Analysis
The notebook counts predefined terms associated with complaints, such as `broken`, `damaged`, `late`, `refund`, `defective`, `poor`, and `replacement`. Each listed keyword is counted at most once per review.

### 4. Review Selection
The notebook prioritizes the lowest ratings and then longer review text to select up to three detailed negative reviews for email drafting.

### 5. Automated Response Drafting
The Groq API is used to draft a concise, empathetic customer-support email. The prompt instructs the model not to invent facts or promise an unapproved refund or replacement. If the API fails, the notebook can create a template-based fallback email.

### 6. Export Results
The notebook saves the results to CSV files:

| Output file | Description |
|---|---|
| `critical_reviews.csv` | Reviews rated 3 or lower |
| `complaint_keyword_summary.csv` | Counts of predefined complaint keywords |
| `generated_response_emails.csv` | Email drafts, review identifiers, and generation source |

## Limitations

- Critical reviews are identified using a fixed rating threshold.
- Complaint analysis only checks a predefined keyword list; it may miss complaints expressed with other words.
- Selecting longer reviews is a simple heuristic, not a semantic ranking method.
- API calls are subject to the provider's model availability, rate limits, and account quota.
- Fallback emails are template-generated and should not be described as AI-generated.
- Generated drafts should be checked by a human before being sent to customers.

## Security Notes

- Keep API keys in `gem.env` or another environment-variable store.
- Do not upload API keys to public repositories.
- If a key is accidentally exposed, revoke it and create a new one.
- Consider adding `gem.env` to `.gitignore`:

```text
gem.env
```

## Conclusion

This project demonstrates a basic customer-feedback workflow combining data cleaning, rule-based critical-review identification, keyword analysis, and automated customer-support email drafting. It also provides a fallback mechanism so the analysis can still produce output files when the API is unavailable.
