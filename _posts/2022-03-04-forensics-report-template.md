---
layout: post
title: Forensics report template 🕵️ 📝
categories:
- General-tech
- Misc
tags:
- General-tech
- Misc
- Report-writing
- Forensics
date: 2022-03-04 18:18 +0800
---

## Introduction

The following is a quick blog entry on general guideline template of how a forensics investigation report should be structured. Based on the course [Writing forensics Report](https://academy.cyber5w.com/courses/take/writing-forensics-report/pdfs/24032338-forensics-report-sections).

It is recommended to write the report in Microsoft word or Google Doc as they have tracking mechanisms and version history of the changes. If you want to have a [copy of the template](https://docs.google.com/document/d/1OdZub2M0L8m3Ib5fDquMPZG7LlYfzdKqrrShCVNuT1I/edit?usp=sharing) you can get the google doc I made from here.

You can also use git for controlling version history and write in markdown.

### Reporting Standards

- When writing the forensic report, the investigators should follow a reporting standard and informs the client of these standards to help them understand the contents included in the report.
- Also, the report should be concentrate on analyzing the case and highlighting the main causes by answering the 5 W's questions.
- Some of the reporting standards are illustrated below:
  - Understandable: can be understood by multiple audiences
  - Depends on the facts: written based on the fact not opinions
  - Delivered on time: should be delivered at a specific time arranged previously.
  - Reproducible: can be repeated easily by another investigator.

### Reporting Guidelines

- When writing the forensic report, the investigators should take into consideration the report style and formatting.
- Below are some of the guidelines for writing a forensic report:
  - Don't use the passive voice while writing the report
  - Write it in the past tense
  - Write in clear, specific, concise statements, and avoid ambiguity
  - Write the exact actions that you did rather than those that couldn't achieve
- Furthermore:
  - Make use of the abbreviations and transitoin words
  - Consistently use a common term and avoid switching between words with the same meaning
  - Use formal language and provide a clear description of your point of view
  - When explaining an idea, use tables, figures, and screenshots, and don't forget to include captions and references
  
## Title page

This section contains the overview of the case, and should include:

- [ ] Casename
- [ ] CaseID
- [ ] Date
- [ ] Department or organization
- [ ] Investigators name
- [ ] Contact information

## Table of Contents

- Each document has a table of content to learn if the report has many sections.
- The table of content contains all sections,figures,tables,and indexes to help the decision-makers to peruse the report easily.

## Adminstrative Information

This section contains valuable information related to the case, such as:

- [ ] Case number(s) and Case name
- [ ] The department or organization that requests the report
- [ ] the participants of the investigation(the investigators, the victim(s), and the suspect, with information about all of them).
- [ ] Also, it contains the lists of the interrogated or reviewed people
- [ ] Besides that, any search warrants, or authorization for the examination or investigation of the evidence.

## The Executive Summary

- When the investigation contains a separation of duties, the law enforcement agencies will clarify the duties of the investigators in this section.
- The executive summary should be written in clear, short, and concise sentences/paragraphs with no more than 10 percent of the entire report to help the non-technical user to understand the investigation.

## Investigation Scope

- This section contains the scope of the investigation and documents what was analyzed with the objectives.
- It is very important for the investigators and the decision-makers when they define the success of the investigation and the admissibility of the evidence.

## Analysis Summary

- This section contains all the obtained artifacts and the importance of holding them, especially when delivering the evidence to the court.

- Besides that, it contains a technical summary of the whole case.

## Analysis

- In this section, the investigators document all the steps used during the examination/investigation of the case.
- Also, they should include all hardware and software specifications utilized in the examination/investigation, as well as the make, model, and serial number for hardware, and the manufacturer and version number for the software.
  - It is very important to document all investigation details which help other investigators to validate the findings.
- This section is considered the longest section in the report because it includes the whole investigation from A to Z.
- The table below is an example of the software specifications included in the analysis section.

| Tools Name            | Version | Company Name       | Description      |
|:----------------------|:--------|:-------------------|:-----------------|
| FTK Imager            | 4.5.0.3 | AccessData         | Data Imaging tool|
| 010 Editor            | 11.0.1  | SweetScapeSoftware | Data Editor Tool |
| Arsenal Image Mounter | 3.3.138 | Arsenal Recon      | Image Mounting Tool|

## Conclusion

- It contains the summary of the report with the most significant information : **"findings"** that could help the decision-makers to make their decisions based upon the findings and evidence.
- Furthermore, it contains security recommendations besides the next steps after the investigation to solve the case.

## Exhibits

- All the Chain of Custodies (COCs), and the leftover are added in this section.
- The investigators include the investigation's documents with pictures, screenshots, fingerprints, printed emails, or any other files of interest and any other evidence located in the cybercrime scene.
- Furthermore, the external reports generated by the tools also should be included in this section.

## Revision History

- This section documents any further details or changes that happened to the investigation that affected the investigation documents.
- It contains the date and time, the changes, the editors, and any other notes
- This section is necessary to follow up the investigation process and tracking the changes that happened to the investgation's documents by other investigators or decision-makers.
