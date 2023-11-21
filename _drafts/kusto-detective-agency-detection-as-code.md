---
layout: post
title: Kusto Detective Agency - Detection as code
categories: [CTF, Detection and Threat-Hunting]
tags: [CTF, Threat-Hunting, Detection, DFIR]
---

## Category

Threat-Hunting

## Question
Kusto Onboarding

Your first task is to create a free Kusto cluster which will be your primary investigation tool. Then you’ll have to answer a simple question to get started.
paste the following KQL script into the query editor, and then run it. Make sure you wait around 10 seconds for the script to complete. Don’t run it more than once! This script creates a new table and loads a file located in Azure blob storage. Now, to test that it’s working, sum up the values in the “Score” column 

```kql
.execute database script <|
// Create table for the data
.create-merge table Onboarding(Score:long)
//clear any previously ingested data if such exists
.clear table Onboarding data
// Import data
.ingest into table Onboarding ('https://kustodetectiveagency.blob.core.windows.net/onboarding/onboarding.csv.gz') with (ignoreFirstRecord=true)
```

<details>
 <summary>Click here to view the query</summary>

```kql
Onboarding
| project Score
| summarize TotalSum = sum(Score)
```

</details>

