# NYC Public Sentiment

## 1. Introduction

This is a large-scale natural language processing (NLP) project that analyzes public feedback from NYC’s 311 Service Request system to uncover **latent urban issues and citizen sentiment**.

Rather than relying solely on predefined complaint categories, this project uses **semantic embeddings** to represent how residents describe problems in their own words. These representations are later connected to **time, location, and service outcomes** and visualized through an interactive city dashboard.

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

### Objective

The goal of Phase 1 was to transform raw citizen complaint text into a **dense semantic representation** that captures meaning, similarity, and latent structure across millions of service requests.

Rather than relying on predefined categories or keyword counts, this phase focuses on **how residents describe problems in their own words**.

---

### Text Construction

A unified text field was created for each service request by combining the following complaint-related columns:

- `complaint_type`  
- `descriptor`  
- `descriptor_2`  

These fields were concatenated in a consistent format, with missing values handled gracefully. This ensured that each complaint was represented by a single coherent textual description while preserving as much semantic detail as possible.

---

### Semantic Embedding Approach

Each complaint’s unified text was converted into a numerical vector using a **pretrained sentence embedding model**. This model maps semantically similar complaints to nearby points in vector space, even when they use different wording.

Key characteristics of this approach:
- Context-aware (goes beyond keywords)
- Robust to spelling and phrasing variation
- Scalable to millions of records
- Produces fixed-length embeddings suitable for clustering and similarity search

Embeddings were generated in batches with progress tracking to safely handle the dataset’s scale.

---

### Phase 1 Outputs

Phase 1 produces a reusable semantic layer that can be applied across multiple analyses:

- A matrix of dense embeddings representing each complaint
- A metadata table aligned to the embeddings for joins and aggregation
- A stable mapping between complaint identifiers and embedding rows

These artifacts are intentionally decoupled from raw data ingestion, allowing downstream tasks to be performed without reprocessing text.

---

