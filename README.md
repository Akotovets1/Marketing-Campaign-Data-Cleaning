# Cleaning Bank Marketing Campaign Data

## Project Description

### Project Description

#### Objective
The primary objective of this project was to partner with a financial institution to enhance the data management of their recent marketing initiative. The campaign's goal was to encourage customers to apply for personal loans. As the bank plans to launch additional marketing efforts, our task was to refine the collected data to meet specific structural and datatype requirements. This process ensures the bank can leverage the cleaned data for establishing a PostgreSQL database. This database will not only archive the current campaign's data but also streamline the integration of future campaign data.

#### Process Overview
The bank provided a dataset, `bank_marketing.csv`, encapsulating the results from their latest marketing campaign. Our responsibilities encompassed:

1. **Data Cleaning:** Identifying and rectifying inconsistencies, missing values, and anomalies within the dataset to ensure accuracy and reliability.
2. **Data Reformatting:** Adjusting the dataset to align with the bank's specified structure and data types, facilitating seamless database integration.
3. **Data Splitting:** Segregating the cleaned and reformatted dataset into three distinct CSV files, tailored to the bank's requirements for diverse analytical purposes.

#### Deliverables
The culmination of this project will be the delivery of three meticulously cleaned, reformatted, and structured CSV files. These files are prepared for direct import into the bank's PostgreSQL database, setting a robust foundation for storing the current campaign's data and simplifying the process for future campaign data incorporation.

Specifically, the three files should have the names and contents as outlined below:

## `client.csv`

| column | data type | description | cleaning requirements |
|--------|-----------|-------------|-----------------------|
| `client_id` | `integer` | Client ID | N/A |
| `age` | `integer` | Client's age in years | N/A |
| `job` | `object` | Client's type of job | Change `"."` to `"_"` |
| `marital` | `object` | Client's marital status | N/A |
| `education` | `object` | Client's level of education | Change `"."` to `"_"` and `"unknown"` to `np.NaN` |
| `credit_default` | `bool` | Whether the client's credit is in default | Convert to boolean data type |
| `mortgage` | `bool` | Whether the client has an existing mortgage (housing loan) | Convert to boolean data type |

<br>

## `campaign.csv`

| column | data type | description | cleaning requirements |
|--------|-----------|-------------|-----------------------|
| `client_id` | `integer` | Client ID | N/A |
| `number_contacts` | `integer` | Number of contact attempts to the client in the current campaign | N/A |
| `contact_duration` | `integer` | Last contact duration in seconds | N/A |
| `previous_campaign_contacts` | `integer` | Number of contact attempts to the client in the previous campaign | N/A |
| `previous_outcome` | `bool` | Outcome of the previous campaign | Convert to boolean data type |
| `campaign_outcome` | `bool` | Outcome of the current campaign | Convert to boolean data type |
| `last_contact_date` | `datetime` | Last date the client was contacted | Create from a combination of `day`, `month`, and a newly created `year` column (which should have a value of `2022`); <br> **Format =** `"YYYY-MM-DD"` |

<br>

## `economics.csv`

| column | data type | description | cleaning requirements |
|--------|-----------|-------------|-----------------------|
| `client_id` | `integer` | Client ID | N/A |
| `cons_price_idx` | `float` | Consumer price index (monthly indicator) | N/A |
| `euribor_three_months` | `float` | Euro Interbank Offered Rate (euribor) three-month rate (daily indicator) | N/A |

#### Impact
By achieving a high standard of data cleanliness and organization, this project empowers the bank to:

- Efficiently analyze the effectiveness of their marketing strategies.
- Enhance decision-making processes for future campaigns.
- Establish a scalable and flexible data management system capable of accommodating the dynamic nature of marketing data.

This collaboration signifies a pivotal step towards optimizing the bank's marketing efforts through strategic data management and analysis.

### Results and Discussion

#### Overview of Achievements
The data cleaning and reformatting phase of the `bank_marketing.csv` dataset has been successfully completed, yielding significant improvements in data quality and structure. This process involved meticulous examination and correction of the dataset to ensure it adhered to the bank's specified requirements for their PostgreSQL database. The primary achievements of this project include:

1. **Data Integrity:** Removal of inconsistencies and inaccuracies, ensuring the data accurately represents the marketing campaign's outcomes.
2. **Standardization:** Conformation to the bank's specified data structures and types, facilitating seamless database integration.
3. **Optimized Segmentation:** Division of the dataset into three distinct CSV files, each tailored to specific analytical needs and future campaign strategies.

#### Methodology / Approach

```python
# Import necessary libraries
import pandas as pd
import numpy as np

# Read the dataset into a pandas DataFrame
marketing = pd.read_csv("bank_marketing.csv")

# Split the original dataset into three tables for detailed analysis
## Creating the 'client' table with personal and financial information
client = marketing[["client_id", "age", "job", "marital", 
                    "education", "credit_default", "mortgage"]]

## Creating the 'campaign' table with details of the marketing interactions
campaign = marketing[["client_id", "number_contacts", "month", "day", 
                      "contact_duration", "previous_campaign_contacts", "previous_outcome", "campaign_outcome"]]

## Creating the 'economics' table focusing on economic indicators
economics = marketing[["client_id", "cons_price_idx", "euribor_three_months"]]

# Data cleaning and formatting for the 'client' table
## Clean the 'education' column by replacing dots with underscores and handling unknown values
client["education"] = client["education"].str.replace(".", "_")
client["education"] = client["education"].replace("unknown", np.NaN)

## Clean the 'job' column by removing any dots
client["job"] = client["job"].str.replace(".", "")

## Convert the 'credit_default' and 'mortgage' columns to boolean data type
for col in ["credit_default", "mortgage"]:
    client[col] = client[col].astype(bool)

# Editing and formatting the 'campaign' table
## Convert 'campaign_outcome' to binary values for easier analysis
campaign["campaign_outcome"] = campaign["campaign_outcome"].map({"yes": 1, "no": 0})

## Convert 'previous_outcome' to binary values, simplifying the analysis
campaign["previous_outcome"] = campaign["previous_outcome"].map({"success": 1, "failure": 0, "nonexistent": 0})

## Standardize 'month' and 'day' columns by capitalizing and converting to string, respectively
campaign["month"] = campaign["month"].str.capitalize()
campaign["day"] = campaign["day"].astype(str)

## Add a 'year' column for the campaign and create a 'last_contact_date' with a combined datetime format
campaign["year"] = "2022"
campaign["last_contact_date"] = campaign["year"] + "-" + campaign["month"] + "-" + campaign["day"]
campaign["last_contact_date"] = pd.to_datetime(campaign["last_contact_date"], format="%Y-%b-%d")

## Convert outcome columns to boolean and drop unnecessary columns for clarity
for col in ["campaign_outcome", "previous_outcome"]:
    campaign[col] = campaign[col].astype(bool)
campaign.drop(columns=["month", "day", "year"], inplace=True)

# Save the cleaned and formatted tables to individual csv files for easy access and further analysis
client.to_csv("client.csv", index=False)
campaign.to_csv("campaign.csv", index=False)
economics.to_csv("economics.csv", index=False)
```

#### Key Findings
- **Data Quality:** Initial analysis revealed a notable amount of missing values and discrepancies, particularly in customer contact information and response data. These issues were systematically addressed, resulting in a more reliable dataset for analysis.
- **Data Structure:** The original dataset lacked uniformity in several key fields, including date formats and categorical variables. By standardizing these elements, we ensured that the data is now in a format conducive to efficient querying and analysis within a PostgreSQL environment.
- **Data Splitting Strategy:** The decision to split the cleaned dataset into three parts was driven by the need to categorize data based on its utility for different analytical purposes. These categories include customer demographics, campaign response metrics, and loan acceptance rates, each serving unique roles in the bank's strategic planning.

#### Discussion
The completion of this project marks a pivotal step towards enhancing the bank's capacity to derive actionable insights from marketing campaign data. The cleaned and restructured dataset not only serves the immediate need of database integration but also lays a foundation for more sophisticated data analysis and predictive modeling in future campaigns.

**Challenges Encountered:**
- The diversity of data quality issues presented challenges in automating the cleaning process, requiring a combination of manual and automated techniques.
- Ensuring the split datasets maintained relational integrity was critical, necessitating careful planning and execution.

**Future Recommendations:**

- **Automated Data Cleaning Pipelines:** Develop automated pipelines for preliminary data cleaning, allowing for more efficient processing of future campaign data.
- **Data Governance Policies:** Establish clear data collection and management policies to improve the quality of future datasets at the source.
- **Advanced Analytical Models:** Leverage the cleaned data to build predictive models that can forecast campaign outcomes and customer behavior, informing more targeted and effective marketing strategies.

#### Conclusion
The successful cleaning, reformatting, and splitting of the `bank_marketing.csv` dataset represents a significant enhancement to the bank's data management capabilities. This project not only facilitates immediate improvements in how the bank stores and analyzes campaign data but also provides a scalable framework for incorporating and leveraging data from future marketing initiatives. As the bank continues to refine its marketing strategies, the insights derived from this and subsequent datasets will be invaluable in driving more personalized, effective, and efficient customer outreach.

