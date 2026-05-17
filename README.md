# Tools-Review-Usability-Evaluation
LLM-based (GPT) usability evaluation of Power BI, Tableau, and Looker Studio using GPT-4o sentiment analysis on 880 Capterra reviews.
This project came out of a practical problem: I needed to evaluate the usability of three BI platforms, Power BI, Tableau, and Looker Studio, for my master's thesis, and I didn't want to run questionnaires or spend weeks manually coding hundreds of reviews. So I built a pipeline that does it automatically.

The idea is simple. Collect real user reviews from Capterra, use GPT-4o to figure out which usability dimension each review is actually talking about, then score each review specifically through that lens. The result is a multidimensional usability profile per platform grounded in ISO 9241-11 — not a single score, but five separate ones.
How it works
The pipeline runs in three stages, one notebook per stage.

While this project was built to compare BI tools specifically, the pipeline itself is not tied to that domain at all. The same approach — scrape reviews, discover usability themes inductively, score each review through its specific theme. It can be applied to evaluate any software product that has a body of user reviews online. CRM platforms, project management tools, IDE plugins, and mobile apps. If there are enough reviews on a platform like Capterra, G2, or the App Store, this pipeline can turn them into a structured usability profile. The only thing that would change is the input URLs.

# Stage 0 — Scraping (Capterra_scraper.ipynb)
Reviews are collected using Selenium and BeautifulSoup from Capterra's review pages. The scraper iterates page by page and pulls five fields per review: date, title, full text, reviewer role, and reviewer industry.
One thing worth noting: Capterra sometimes shows a bot verification screen on the first page load. When that happens, verify manually in the browser window before the loop starts; the rest runs automatically after that.

# Stage 1 — Theme Discovery & Classification (theme_analysis.ipynb)
This is the part I found most interesting to build. Rather than deciding in advance what usability dimensions matter, I let the model figure that out from the data itself.

# Step 1a  
Discovery: A random sample of 80 reviews gets sent to GPT-4o with a prompt anchored to ISO 9241 usability concepts. The model identifies the 5 most recurring usability-relevant themes across the corpus.
The five themes it found:

# Theme: What it covers
Ease of Use: How intuitive the interface is for everyday tasks

Visualization Clarity: How clear and readable the charts and dashboards are

Learnability: How quickly new users can get up to speed; onboarding friction

Efficiency: How fast users can complete tasks once they know the tool

User Satisfaction: Overall positive experience, comfort, and confidence

# Step 1b — Classification: 
All 880 reviews are then classified in batches of 20, each review assigned to its single best-matching theme.

# Stage 2 — Theme-Aware Sentiment Scoring (sentiment_by_theme.ipynb)
Each review is scored by GPT-4o through the lens of its assigned theme. This is important — without anchoring the model to a specific dimension, it tends to score based on the overall tone of the review rather than what you actually care about.
The prompt tells the model exactly what dimension to focus on, gives it a 1–10 scoring scale, and asks it to extract a short evidence quote from the review. Every call uses temperature=0.0 so the results are fully reproducible.

# Results
Mean score by theme and platform
Theme	Power BI	Tableau	Looker Studio
User Satisfaction	7.78	8.35	7.60
Visualization Clarity	7.45	7.71	6.50
Ease of Use	7.35	7.43	7.55
Efficiency	7.11	7.11	6.89
Learnability	4.85	5.22	4.68

Tech stack
Python 3.x

Selenium + BeautifulSoup — dynamic scraping

OpenAI API (GPT-4o) — theme discovery, classification, sentiment scoring

Pandas — data processing

tqdm — batch progress tracking

openpyxl — Excel I/O

Matplotlib / Seaborn — visualizations
