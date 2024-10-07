# Analysis of Wikipedia Traffic for Rare Diseases

## Project Goal
Rare diseases, affecting a small percentage of the population, often receive less public and media attention compared to more common diseases.

The goal of this project is to analyze the monthly article traffic for Wikipedia pages related to rare diseases from July 1, 2015, to September 30, 2024. We aim to identify trends in engagement with these articles across different access types (desktop, mobile web, and mobile app) and provide insights into how traffic patterns may correlate with events such as health crises or public interest shifts.

### Key Objectives
- **Data Acquisition**: Collect monthly pageview data for specific rare disease articles by querying the Wikimedia Pageviews API and organizing the results into structured JSON files.
- **Data Analysis:** Create visualizations of the collected data to identify trends, peak interest periods, and engagement levels, with a focus on understanding user behavior on both desktop and mobile platforms.

This project follows to the best practices for open scientific research as mentioned in chapters "Assessing Reproducibility" and "The Basic Reproducible Workflow Template" of "The Practice of Reproducible Research: Case Studies and Lessons from the Data-Intensive Sciences" publication, ensuring transparency and reproducibility throughout the process.

## Licenses

### Input Data (rare-disease_cleaned.AUG.2024.csv)

In this repository, we collect counts of pageviews using a specified subset of Wikipedia article pages. This is a [subset of the English Wikipedia](https://drive.google.com/file/d/15_FiKhBgXB2Ch9c0gAGYzKjF0DBhEPlY/view) that represents a large number of articles related to rare diseases. This list of pages was collected by using a database of rare diseases maintained by the [National Organization for Rare Diseases (NORD)](https://rarediseases.org/). Please make sure to follow the [Terms & Conditions](https://rarediseases.org/terms-conditions/) before making contributions to this repository.

### Data Generated for Analysis

We have used Wikimedia's [Pageviews API](https://labtestwikitech.wikimedia.org/wiki/Analytics/PageviewAPI) to collect monthly pageview data for selected rare disease articles.
- Link to the official documentation: [API Documentation](https://wikimedia.org/api/rest_v1/)

The Wikimedia Foundation's [Terms of Use](https://foundation.wikimedia.org/wiki/Policy:Terms_of_Use) allows users to freely read, print, share, and reuse educational content while contributing to various projects under free licenses. Contributers looking to further explore the generated dataset must take responsibility for their edits, maintain a civil environment, adhere to copyright laws, and avoid harming the technological infrastructure. Contributions must generally be licensed under free and open terms, and content is intended for informational purposes only, not as professional advice.

### Repository

The repository is licensed under the [MIT License](https://docs.github.com/en/repositories/managing-your-repositorys-settings-and-features/customizing-your-repository/licensing-a-repository), and it permits users to freely use, modify, and distribute the code, provided that the original copyright notice and permission notice are included in all copies or substantial portions of the software.

A few snippets used in the scripts are developed by Dr. David W. McDonald - from the [example notebook provided with this assigment, revision dated: August 16, 2024](https://drive.google.com/file/d/1fYTIX79t9jk-Jske8IwysV-rbRkD4_dc/view?usp=drive_link) (licensed [CC-BY](https://www.google.com/url?q=https%3A%2F%2Fcreativecommons.org%2Flicenses%2Fby%2F4.0%2F))

## Workflow

The code was executed on a Windows 11 laptop. Before running this project, ensure that the [required libraries](https://github.com/parvatijay2901/data-512-homework_1/blob/main/requirements.txt) are installed. Additionally, please create the necessary folders and adjust the file paths as required for your environment.

To run this project, execute the notebooks mentioned in the sections below.
### 1. Data Acquisition
**Notebook:** [`step1_data-acquisition.ipynb`](https://github.com/parvatijay2901/data-512-homework_1/blob/main/scripts/step1_data-acquisition.ipynb)

This is the first step in this project where we ensure that all the data is prepared for analysis.

The script generates the following files in the location `data/generated_data/`:
 - **rare-disease_monthly_mobile_<`startYYYYMM`>-<`endYYYYMM`>.json:** Contains monthly pageview data for mobile access, combining both mobile app and mobile web views.

 ***Data Schema:***
```yaml
string:  # Page title
  - article: string       # Page title
    timestamp: string     # Timestamp in YYYYMMDDHH format
    views: integer        # Number of page views

```
 - **rare-disease_monthly_desktop_<`startYYYYMM`>-<`endYYYYMM`>.json:** Includes monthly pageview data specifically for desktop access.

  ***Data Schema:***
```yaml
string:  # Page title
  - project: string       # Wikimedia domain and subdomain
    article: string       # Page title
    granularity: string   # Time interval between datapoints
    timestamp: string     # Timestamp in YYYYMMDDHH format
    access: string        # Device used to access
    agent: string         # Type of user agent
    views: integer        # Number of page views
```
 - **rare-disease_monthly_cumulative_<startYYYYMM>-<endYYYYMM>.json:** Contains cumulative pageview data, summing all mobile and desktop traffic for each article.

  ***Data Schema:***
```yaml
string:  # Page title
  - article: string       # Page title
    timestamp: string     # Timestamp in YYYYMMDDHH format
    views: integer        # Number of page views

```

### 2. Analysis
**Notebook:** [`step2_data-analysis.ipynb`](https://github.com/parvatijay2901/data-512-homework_1/blob/main/scripts/step2_data-analysis.ipynb)

This is the second and final step in this assignment. Here we have a visual data analysis of pageview data collected in the previous step.

Particularly, we will create these three visualizations:
- **Maximum and Minimum Average:** A time series graph displaying the highest and lowest average page requests for both desktop and mobile access.
- **Top 10 Peak Page Views:** A time series graph showcasing the top 10 articles with the highest peak page views for desktop and mobile access.
- **Fewest Months of Data:** A time series graph illustrating the 10 articles with the fewest months of available data for both desktop and mobile access.

We have saved a logarithmic version of the same linear scale plots for better clarity in the folder `generated_visuals/`.

## Known Issues and Special Considerations
- In the example script provided with the assignment, the `safe` parameter was not included when encoding article titles for API requests. I have modified the script to include the `safe` parameter for handling special characters and spaces correctly:
```python
article_title_encoded = urllib.parse.quote(request_template['article'].replace(' ', '_'), safe=' ')
```
- The dataset may contain missing months for certain articles, especially for less popular topics. This could affect analyses that rely on complete time series data (For example, as seen in [Maximum and Minimum Average plot](https://github.com/parvatijay2901/data-512-homework_1/blob/main/generated_visuals/plot1_logscale_maximum_average_and_minimum_average.png)). The articles also vary in views and special care should be taken while analyzing less popular articles with more popular articles over time.
- Due to a skewed distribution of page views across articles, I have used logarithmic scales for the visualizations. Care should be taken while interpreting the results as this can mask differences between articles with lower view counts.
- Trends in page views may correlate with external events (e.g., the COVID-19 pandemic as seen in [Fewest Months of Data plot](https://github.com/parvatijay2901/data-512-homework_1/blob/main/generated_visuals/plot3_logscale_fewest_months_of_data.png.png)), and hence should be accounted. A good domain knowledge is required for the same.
- The data is sourced from Wikipedia’s pageview API, which may have limitations in terms of accuracy and completeness. The API may also undergo changes over time. I have not delved deep into the data accuracy and hence would recommend to be mindful of the limitations when conducting further analyses.


## Conclusion

We have successfully analyzed Wikipedia traffic patterns for rare disease articles, highlighting key trends across various access methods (desktop, mobile web, and mobile app) from July 2015 to September 2024. By querying and visualizing data from the Wikimedia Pageviews API, we can now analyze and track how public interest fluctuates over time and in response to specific events.

### Key Observations:
- There were noticeable spikes in page views for certain articles during pandemic crises or during special events, such as the COVID-19 (We saw more engagements to rare disease articles ever since).
- Mobile access (both mobile web and app) surpassed desktop traffic for most articles. This shows the growing trend of mobile usage, especially in the last few years.
- Data gaps were observed in certain months where page views were missing, and the distribution of page views was skewed, with a small number of articles receiving the majority of the traffic in specific cases.


### Research Practices
The project followed the best practices outlined in “The Practice of Reproducible Research” to ensure transparency and ease of replication:
- The project maintained a hierarchical structure, separating scripts, data, and visualizations into distinct folders for better organization. Output data and visuals are stored in dedicated folders (`data/generated_data/` and `generated_visuals/`, respectively).
- Dependencies were clearly listed in the requirements.txt file. This helps in easy replication of the environment.
- The workflow was automated using Jupyter notebooks. This helps in seamless re-execution of the analysis.
- We avoided hard coding paths. This made the project more simple and better adaptable for different environments.
- The scripts and functions are clearly separated to provide anyone looking into the project to have better clarity.
- Finally, the README provided clear instructions for running the project and understanding its objectives.

The project could be improved by incorporating best practices such as using logging, maintaining a `.gitignore file`, and applying additional workflow improvements. However, it serves its purpose for the current scope of analysis to a good extent.

## Additional References
- Blancas, Eduardo (2021) [The Data Science Checklist: Best Practices for Maintainable Data Science Projects](https://ploomber.io/blog/checklist/), Ploomber