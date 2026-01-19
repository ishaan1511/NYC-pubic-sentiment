# NYC Public Sentiment

## 1. Introduction

This is a large-scale natural language processing (NLP) project that analyzes public feedback from NYC’s 311 Service Request system to uncover **latent urban issues and citizen sentiment**.

Rather than relying solely on predefined complaint categories, this project uses **semantic embeddings** to represent how residents describe problems in their own words. These representations are later connected to **time, location, and service outcomes** and visualized through an interactive city dashboard.

The primary output of the project is an **interactive analytics dashboard** that enables exploration of complaint themes across boroughs and over time, highlighting emerging urban issues and geographic concentration patterns.

This project blends:
- NLP and representation learning
- Public-sector data analysis
- Time and location-based insights
- Visualization-driven storytelling

---

## 2. Data Collection — NYC 311 via Open Data API

### 2.1 Data Source
- **Dataset:** NYC 311 Service Requests (2020–Present)
- **Provider:** NYC Open Data (Socrata)
- **API Type:** SODA v3

The dataset contains millions of citizen-submitted service requests, including complaint descriptions, timestamps, agencies, and geospatial information.

---

### 2.2 Columns Selected from the API (20 Core Fields)

The following 20 columns were retained, balancing **semantic richness**, **temporal context**, and **geospatial relevance**:

**Identifiers & Status**
- `unique_key` — Unique identifier for each service request  
- `status` — Current status of the request  

**Time Information**
- `created_date` — Timestamp when the service request was created  
- `closed_date` — Timestamp when the request was closed  

**Agency Information**
- `agency` — Acronym of the responding city agency  
- `agency_name` — Full name of the responding agency  

**Complaint Description**
- `complaint_type` — High-level category describing the issue  
- `descriptor` — Additional detail associated with the complaint type  
- `descriptor_2` — Further optional detail when available  

**Location (Human-Readable)**
- `borough` — Borough of the incident location  
- `city` — City name of the incident location  
- `incident_zip` — Zip code of the incident  
- `street_name` — Street name provided by the submitter  
- `incident_address` — House number or address provided  

**Geospatial Coordinates**
- `latitude` — Latitude of the incident location  
- `longitude` — Longitude of the incident location  

**Administrative Geography**
- `community_board` — NYC community board  
- `council_district` — City council district  
- `police_precinct` — NYPD precinct  

**Resolution Context**
- `resolution_description` — Text description of the final or latest action taken  

These fields were chosen to preserve the **who, what, when, where, and outcome** of each complaint while excluding highly specialized or low-coverage columns (e.g., taxi-specific, bridge-only, or park-only fields).

---

## 3. Phase 1 — Semantic Representation of Complaints

### 3.1 Objective

Phase 1 focuses on transforming raw NYC 311 complaint text into **dense semantic embeddings** that capture meaning and similarity across millions of service requests. Rather than relying on predefined complaint categories or keyword-based methods, this phase emphasizes **how residents describe issues in natural language**.

---

### 3.2 Text Construction

A unified text field was created for each service request by combining the following complaint-related columns:

- `complaint_type`
- `descriptor`
- `descriptor_2`

These fields were concatenated in a consistent format, with missing values handled appropriately. This approach ensures that each complaint is represented by a single, coherent textual description while preserving relevant semantic detail.

---

### 3.3 Semantic Embeddings

The unified complaint text was converted into numerical vectors using a **pretrained sentence embedding model**. This method maps semantically similar complaints to nearby points in vector space, even when different wording is used.

Embeddings were generated in batches with progress tracking to safely scale to millions of records. The resulting vectors form a reusable semantic representation suitable for similarity search, clustering, and downstream analysis.

---

### 3.4 Phase 1 Outputs

Phase 1 produces the following reusable artifacts:

- A matrix of dense embeddings representing each service request  
- A metadata table aligned with the embeddings for aggregation and joins  
- A stable mapping between service request identifiers and embedding rows  

These outputs are decoupled from raw data ingestion, enabling subsequent phases to operate directly on the semantic representation without reprocessing text.

## 4. Phase 2 — Latent Theme Discovery

### 4.1 Objective

Phase 2 focuses on discovering **latent urban issue themes** within NYC 311 service requests by clustering semantically similar complaints.  
Rather than relying on predefined complaint categories, this phase groups complaints based on **how residents describe issues in natural language**.

---

### 4.2 Semantic Embedding Input

This phase operates directly on the dense semantic embeddings produced in Phase 1.  
Each embedding represents a single service request constructed from complaint-related text fields, enabling similarity-based grouping without manual labeling.

---

### 4.3 Dimensionality Reduction

To improve clustering efficiency and interpretability, high-dimensional complaint embeddings are projected into a lower-dimensional space.  
This step preserves local semantic relationships while reducing noise and computational complexity, and enables visualization of latent complaint structure.

---

### 4.4 Unsupervised Clustering

Density-based clustering is applied to the reduced embedding space to identify natural groupings of semantically similar complaints.  
This approach:
- Discovers clusters of varying size and density  
- Does not require specifying the number of themes in advance  
- Identifies rare or highly specific complaints as noise  

Each service request is assigned either a **cluster label** or a **noise label**, reflecting its membership in a latent theme.

---

### 4.5 Cluster-to-Theme Mapping

Clusters are translated into **human-interpretable themes** by examining:
- Representative complaint text within each cluster  
- Frequently occurring complaint types and descriptors  
- Distribution of clusters across agencies and locations  

This mapping process converts abstract clusters into meaningful urban issue themes suitable for aggregation and analysis.

---

### 4.6 Phase 2 Outputs

Phase 2 produces the following artifacts:
- A cluster label for each service request  
- A theme label derived from cluster interpretation  
- Cluster-level summaries describing dominant urban issues  

These outputs serve as structured inputs for downstream temporal and spatial analysis.

---

## 5. Phase 3 — Temporal and Spatial Analysis

### 5.1 Objective

Phase 3 integrates latent complaint themes with **time and location** to analyze how urban issues evolve across neighborhoods and over time.

---

### 5.2 Temporal Aggregation

Service requests are aggregated by month to examine:
- Theme-level complaint trends  
- Emerging or declining urban issues  
- Seasonality in complaint patterns  

Resolution-related metadata is also used to evaluate variation in service response across themes and agencies.

---

### 5.3 Spatial Aggregation

Complaints are analyzed across boroughs, council districts, and community boards to identify:
- Geographic concentration of specific themes  
- Persistent neighborhood-level issue patterns  
- Cross-borough differences in service request composition  

Spatial aggregation enables the identification of localized hotspots and regional disparities.

---

### 5.4 Phase 3 Outputs

Phase 3 produces:
- Time-series summaries of complaints by theme  
- Geographic distributions of latent themes  
- Inputs for interactive visualization and dashboarding  

These outputs support exploratory analysis and policy-relevant insight generation.


## 6. References

1. **Sentence-BERT: Sentence Embeddings using Siamese BERT-Networks.**  
   Proceedings of the 2019 Conference on Empirical Methods in Natural Language Processing.  
   [https://arxiv.org/abs/1908.10084](https://arxiv.org/abs/1908.10084)

2. *Sentence-Transformers.  
   **all-MiniLM-L6-v2: General-purpose sentence embedding model.**  
   Hugging Face Model Hub.  
   [https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2](https://huggingface.co/sentence-transformers/all-MiniLM-L6-v2)

3. **HDBSCAN: Hierarchical Density-Based Clustering.**  
   Journal of Open Source Software.  
   [https://joss.theoj.org/papers/10.21105/joss.00205](https://joss.theoj.org/papers/10.21105/joss.00205)

4. New York City Open Data.  
   **311 Service Requests from 2020 to Present.**  
   [https://data.cityofnewyork.us/Social-Services/311-Service-Requests-from-2020-to-Present/erm2-nwe9/about_data](https://data.cityofnewyork.us/Social-Services/311-Service-Requests-from-2020-to-Present/erm2-nwe9/about_data)
