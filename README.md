# The_Protein_Project
A health project to analyze common/well-known protein supplements sold in India, data is taken from a study conducted by @theliverdoc(Cyriac Abby Philips) and Neogenlab

The following sections detail insightful questions derived from the findings and some sample SQL queries to answer them

## Findings and Questions

### 1. High Rate of Mislabeling
**Question:** What percentage of the analyzed protein supplements were found to be mislabeled regarding their protein content?
```sql
WITH ProteinDataCTE AS (
SELECT ,
CASE
WHEN (Labelled_Protein - Actual_Protein) > 0 THEN 'Yes'
ELSE 'No'
END AS Mislabeled
FROM ProteinData
)
SELECT
(COUNT() * 100.0 / (SELECT COUNT(*) FROM ProteinDataCTE)) AS Mislabeling_Percentage
FROM ProteinDataCTE
WHERE Mislabeled = 'Yes';
```

---

### 2. Brands with Less than 40% Actual Protein Compared to Labeled Protein
**Question:** Which brands have products that contain less than 40% of the actual protein compared to what is labeled?
```sql
WITH ProteinDataCTE AS (
SELECT *,
CASE
WHEN (Labelled_Protein - Actual_Protein) > 0 THEN 'Yes'
ELSE 'No'
END AS Mislabeled
FROM ProteinData
)
SELECT ManufacturerName, ProductName, Actual_Protein, Labelled_Protein
FROM ProteinDataCTE
WHERE (Actual_Protein / Labelled_Protein) < 0.4;
```

---

### 3. Severe Discrepancies in Protein Content and Toxicity
**Question:** What are the details of severely mislabelled products containing toxic heavy metals?
```sql
WITH ProteinDataCTE AS (
SELECT *,
CASE
WHEN (Labelled_Protein - Actual_Protein) > 0 THEN 'Yes'
WHEN (Labelled_Protein - Actual_Protein) > 10 THEN 'Yes Severe'
ELSE 'No'
END AS Mislabeled
FROM ProteinData
)
SELECT ManufacturerName, ProductName, Actual_Protein, Labelled_Protein
FROM ProteinDataCTE
WHERE Mislabelled LIKE '%Severe%' AND (Arsenic IS NOT NULL OR Lead IS NOT NULL);
```

---

### 4. Protein Spiking Indication
**Question:** Which products exhibit higher actual protein content than what is labeled, indicating potential protein spiking?
```sql
WITH ProteinDataCTE AS (
SELECT *,
CASE
WHEN (Labelled_Protein - Actual_Protein) > 0 THEN 'Yes'
ELSE 'No'
END AS Mislabeled
FROM ProteinData
)
SELECT ProductName, ManufacturerName, Actual_Protein, Labelled_Protein
FROM ProteinDataCTE
WHERE Actual_Protein > Labelled_Protein;
```

---

### 5. Brands with Multiple Mislabeled Products
**Question:** Which brands have the highest number of mislabeled products in the analysis?
```sql
WITH ProteinDataCTE AS (
SELECT ,
CASE
WHEN (Labelled_Protein - Actual_Protein) > 0 THEN 'Yes'
ELSE 'No'
END AS Mislabeled
FROM ProteinData
)
SELECT ManufacturerName, COUNT() AS Mislabeled_Count
FROM ProteinDataCTE
WHERE Mislabeled = 'Yes'
GROUP BY ManufacturerName
ORDER BY Mislabeled_Count DESC;
```
---

### 6. Worst Protein Content Products
**Question:** What are the 5 worst products in the market according to actual protein content?
```sql
SELECT ProductName, ManufacturerName, Actual_Protein
FROM ProteinData
ORDER BY Actual_Protein ASC
LIMIT 5;
```
---

### 7. Top 5 best Protein Content Products Free of Protein Spiking?
**Question:** What are top 5 products in the market according to actual protein content that are free of protein spiking?
```sql
WITH ProteinDataCTE AS (
    SELECT *,
        CASE 
            WHEN (Labelled_Protein - Actual_Protein) > 0 THEN 'No Protein Spiking' 
            ELSE 'Protein Spiking' 
        END AS ProteinSpiking
    FROM ProteinData
)
SELECT ProductName, ManufacturerName, Actual_Protein
FROM ProteinDataCTE
WHERE ProteinSpiking = 'No Protein Spiking'
ORDER BY Actual_Protein DESC
LIMIT 5;
```

---

## Key Findings 

• Analyzed 36 popular protein supplements used in India, revealing that approximately 70% were mislabeled regarding protein content

• Identified potential protein spiking in 10 products where actual protein content exceeded labeled amounts

• Discovered 14% of the supplements contained harmful toxins above acceptable limit posing serious health risks

• Highlighted Big Muscles and Amway as the worst whey protein and vegan protein brands respectively





