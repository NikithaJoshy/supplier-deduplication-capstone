# Supplier Database Optimization: Entity Resolution & Deduplication

An industry-sponsored senior capstone project. We built a pipeline that cleans, standardizes, and deduplicates a large supplier master database so sourcing teams can trust their supplier data.

*Texas A&M University, DAEN 460 Senior Capstone. Four-person team project with an energy-technology industry sponsor.*

## Problem
Large supplier databases accumulate duplicate records, inconsistent naming across systems, and poor visibility into supplier relationships. This leads to manual validation work and low confidence in sourcing decisions.

## Pipeline
1. **Data cleaning & standardization:** ingested raw supplier data from Excel sources, normalized text fields, and unified the schema into one supplier master dataset (Python).
2. **Feature creation:** normalized names and cleaned tax/VAT IDs for consistent comparison.
3. **Blocking & filtering:** composite blocking keys (normalized name + geography) that avoid O(n²) brute-force comparison.
4. **Tiered matching:**
   - Tier 1: exact identifier match (tax ID / VAT)
   - Tier 2: normalized name + geographic match
   - Tier 3: weighted fuzzy match as a fallback, scored as `w1·NameSim + w2·AddressSim + w3·GeoSim`
5. **Clustering & record selection:** **Union-Find** clustering of duplicate pairs, plus a survivorship scoring model to pick the master record.
6. **Validation:** benchmarked against a reference duplicate dataset.

## Results
- **11,096** supplier records processed
- **6,759** duplicates identified
- **>90%** accuracy, **85%** group purity, and **100%** coverage of the reference duplicates
- 99.8% of matches came from the deterministic Tier 1 and 2 rules. Only 14 needed fuzzy scoring, which kept the design conservative.
- Design tradeoff: favored recall over strict precision, accepting slight over-merging in multi-location supplier groups to avoid missed duplicates.

## Tech Stack
Python, pandas, fuzzy string matching, Union-Find clustering, Excel

> The sponsor's data and code are confidential and are not included. This repo documents the approach and outcomes only.
