# OpinioBank

OpinioBank—a large-scale, high-quality dataset designed to support user-centric opinion summarization from extensive long-form reviews (e.g., OpinioRAG). Unlike existing datasets focused on short-form reviews or synthetic review-summary pairs, OpinioBank comprises entities with over a thousand user reviews each, meticulously paired with unbiased expert reviews and manually annotated queries. This dataset serves as the first benchmark of its kind, aimed at advancing model development and evaluation for user-centric opinion summarization over large-scale, noisy, repetitive, and stylistically diverse inputs. 


For more details, please refer to our paper, titled ["OpinioRAG: Towards Generating User-Centric Opinion Highlights from Large-scale Online Reviews"](https://arxiv.org/abs/2509.00285), published at **COLM 2025**. The dataset is available here: 🤗 [OpinioBank](https://huggingface.co/datasets/tafseer-nayeem/OpinioBank).


### Languages

English

### Loading the Dataset

```python
from datasets import load_dataset

ds = load_dataset("tafseer-nayeem/OpinioBank")

test_dataset = ds["test"]

print(f'Number of samples: {len(test_dataset)}')

```
---

## Dataset Structure


**OpinioBank** is organized in a standardized JSON format designed to ensure consistency and facilitate systematic evaluation of user-centric opinion summarization models. Each entity is represented by the following components:

**Entity Metadata**
| Field         | Type   | Description                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| `entity_id`   | Number | A unique identifier assigned to each entity (e.g., hotels, restaurants).    |
| `entity_name` | String | The name of the entity (e.g., “Fortune Hotel & Suites”).                    |


**Expert Review (Object):** Structured expert reviews categorizing *positive* and *negative* aspects, organized according to annotated user-centric queries.

**Pros (Object):** Positive attributes associated with user queries.

```json
"pros": {
  "shuttle service": "Free, 24-hour shuttle to the airport, Strip, and the Las Vegas Convention Center.",
  "air conditioning": "Effective air conditioning ensuring a comfortable indoor experience.",
  "laundry and dry cleaning": "Valet dry cleaning and laundry services available for guest convenience."
}
```

**Cons (Object):** Limitations or drawbacks associated with user queries.

```json
"cons": {
  "room decor": "Dated room decor that may not appeal to guests seeking modern aesthetics.",
  "wi-fi": "Wi-Fi requires a daily fee, which may be inconvenient for guests expecting complimentary access.",
  "pool": "No on-site pool available for guest relaxation."
}
```

**User Reviews (Array of Objects):** Collection of user-generated reviews. Each review is represented by the following attributes:

| Field | Type | Description |
|-------|------|-------------|
| `review_id` | Number | Unique identifier for each user review. |
| `text` | String | The textual content of the user review, reflecting the user’s experiences and opinions about the entity. |
| `rating` | Number | User’s numerical rating of the entity (e.g., 1–5), indicating overall satisfaction. |
| `helpful_votes` | Number | Number of helpful votes received by the review, serving as a proxy for perceived usefulness. |
| `publication_date` | String | Date of publication (`YYYY-MM-DD`), enabling temporal analysis. |
| `user_review_posted` | Number | Total number of reviews submitted by the user. |
| `user_cities_visited` | Number | Number of cities visited by the user, reflecting travel experience. |
| `user_helpful_votes` | Number | Total number of helpful votes across all of the user’s reviews, indicating their overall credibility. |


### Sample Example

Below is an example of a full entity entry illustrating the dataset structure:

```json
{
  "entity_id": 1,
  "entity_name": "The Evelyn",
  "expert_review": {
    "pros": {
      "shuttle service": "Free, 24-hour shuttle to the airport, Strip, and the Las Vegas Convention Center.",
      "room amenities": "Rooms feature Frette linens, C.O. Bigelow toiletries, and flat-screen TVs",
    },
    "cons": {
      "wi-fi": "Wi-Fi requires a daily fee, which may be inconvenient for guests expecting complimentary access.",
      "tourist attraction": "Limited tourist attractions within walking distance (but subways nearby)"
    }
  },
  "user_reviews": [
    {
      "review_id": 1,
      "text": "We travel frequently for business and this was the best hotel stay we've ever had...",
      "rating": 5,
      "helpful_votes": 1,
      "publication_date": "2022-03-24",
      "user_review_posted": 10,
      "user_cities_visited": 9,
      "user_helpful_votes": 2
    },
    {
      "review_id": 2,
      "text": "This is a good hotel. The staff are friendly and helpful, but ...",
      "rating": 3,
      "helpful_votes": 0,
      "publication_date": "2020-03-16",
      "user_review_posted": 112,
      "user_cities_visited": 71,
      "user_helpful_votes": 67
    }
  ]
}
```
---

### Other Supported Tasks

In addition to supporting **user-centric opinion summarization** from extensive, large-scale, long-form reviews, **OpinioBank** enables a wide range of complementary research tasks. These include:

- **Query-based retrieval** – retrieving relevant opinion segments based on user-specified information needs.  
- **Sentiment analysis** – identifying and classifying sentiments expressed in user reviews.  
- **Temporal opinion analysis** – examining how opinions and sentiments evolve over time.  
- **Aspect-specific summarization** – generating focused summaries centered on specific aspects or user queries.

This comprehensive structure provides a **versatile benchmark** for developing and evaluating advanced models that address real-world challenges in opinion understanding and summarization.

---

## OpinioBank Data Collection Pipeline

### Data Sources

**Source (User Reviews)**  
User reviews were collected from **TripAdvisor**, which provides substantially longer reviews than other travel platforms—on average, three times longer. This makes it an ideal source for studying **long-form summarization** with book-length inputs (exceeding 100K tokens).

**Target (Expert Reviews)**  
High-quality annotated datasets pairing summaries with long-form reviews are rare due to the cost of manual creation. We used **Oyster**, a platform providing **professional hotel reviews** based on firsthand, in-depth expert evaluations. These reviews are the result of a rigorous, multi-source assessment process involving on-site inspections, user review analysis, and online research.

---

### Data Preparation

**Entity Pairing and Crawling**  
We identified entities on Oyster and linked them to their TripAdvisor counterparts using unique identifiers such as **addresses** and **postal codes**. Once matched, we crawled both **user reviews** and **expert reviews** for each entity.

**Manual Query Annotation**  
Using a predefined list of gold query terms (e.g., *room*, *location*, *ocean views*), we manually annotated sentences to ensure **query diversity** and **granularity**, refining or generalizing terms as needed.

**Review Alignment Verification**  
For each query, we located user review sentences containing exact matches of the query terms. Queries without matches were marked as non-applicable and removed, ensuring alignment quality.

**Metadata Integration**  
We enriched the dataset with metadata from both the **review text** and the **reviewer**:

- *Review text*: star rating, helpful votes, publication date  
- *Reviewer*: number of reviews posted, cities visited, cumulative helpful votes

These metadata fields provide valuable contextual signals for analyzing **credibility**, **temporal trends**, and **review quality**.

---

## Dataset Statistics

| **Source User Reviews**         | **Target Expert Reviews** | **Annotated Queries**        |
|:-------------------------------:|:-------------------------------------:|:----------------------------:|
| #Entities: 500                  | #Entities: 500                        | Total #Queries: 5975         |
| Avg. #Reviews: 1.5K            | Avg. #Sents: 11.95                    | Unique #Queries: 1456       |
| Avg. #Sents: 10.5K            | Avg. #PROS: 8.30                     | #Queries (Unigram): 1721   |
| Avg. #Words: 196K            | Avg. #CONS: 3.65                     | #Queries (Bigram): 3441    |
| Avg. #Tokens: 207K          | Avg. #Tokens: 103.53                 | #Queries (Trigram): 640    |
| Max #Tokens: 975K          | Max #Tokens: 190                     | #Queries (>3 words): 173  |

### Key Points

- **Source User Reviews**
  - 500 entities, averaging **1.5K reviews**, **10.5K sentences**, and **207K tokens** per entity.
  - Max token length per entity reaches **975K tokens**, illustrating the **long-form** nature of the data.

- **Target Summaries (Expert Reviews)**
  - Each entity has expert summaries averaging **12 sentences**, with ~8 positive and ~4 negative aspects.
  - Average summary length is **~104 tokens**, max 190.

- **Annotated Queries**
  - Total of **5,975 queries**, with **1,456 unique** queries.
  - Distribution: **1,721 unigrams**, **3,441 bigrams**, **640 trigrams**, and **173 longer (>3 words)**.

- Statistics highlight the **scale**, **linguistic richness**, and **structured query diversity** of the dataset — making it suitable for **long-form summarization**, **retrieval**, and **query-based evaluation** tasks.

---

## Considerations for Using the Data

### Ethical Intent
The data collection process strictly follows established ethical guidelines. All content was sourced from **publicly accessible** TripAdvisor and Oyster pages. No authentication, paywall circumvention, or unauthorized access was involved. As such, the data collection does **not** violate the Computer Fraud and Abuse Act (CFAA) or similar legislation.

### Privacy and Content Integrity
To protect user privacy, we remove all **Personally Identifiable Information (PII)**, including reviewer IDs, usernames, real names, and location data. This ensures that the dataset remains anonymized and compliant with data protection regulations such as the **GDPR** and **CCPA**.

Because the dataset consists of **real-world user-generated content**, some reviews may contain personal details, subjective opinions, or inappropriate language. We intentionally do **not** manually filter or edit the review text to preserve its **authenticity** and **linguistic diversity**, which are valuable for downstream research on user-generated content.

---

## Licensing and Responsible Distribution

All collected data are made available exclusively for **non-commercial research purposes** under the [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International License (CC BY-NC-SA 4.0)](https://creativecommons.org/licenses/by-nc-sa/4.0/).

The **OpinioBank** dataset contains **500 expert-paired samples** and is intended solely for **evaluation and benchmarking**. This limited scale helps minimize both the overall volume of collected content and any potential commercial relevance, supporting responsible data sharing and academic use.

---

## Citation Information

If you use any of the resources or it's relevant to your work, please cite our [COLM 2025 paper](https://arxiv.org/abs/2509.00285). 

```
@inproceedings{nayeem2025opiniorag,
title={Opinio{RAG}: Towards Generating User-Centric Opinion Highlights from Large-scale Online Reviews},
author={Mir Tafseer Nayeem and Davood Rafiei},
booktitle={Second Conference on Language Modeling},
year={2025},
url={https://openreview.net/forum?id=R94bCTckhV}
}
```

