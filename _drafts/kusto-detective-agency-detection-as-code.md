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

<details>
 <summary>Click here to view the query</summary>

```kql
Onboarding
| project Score
| summarize TotalSum = sum(Score)
```
</details>

