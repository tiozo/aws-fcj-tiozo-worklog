---
title: "Week 5 worklog"
weight: 5
chapter: false
pre: "<b> 1.5 </b>"
---

## Week 5 Objectives

- Finish Markdown to Qdrant
- Finish the phase 1 of the project. (Extended due to lacks of technical skill to implement)
- Figure out how to combine the splited PDF, with correct order and figures for each statement is linked correctly in Qdrant.
- Prepare for exam, continue to study AWS security.

## Tasks to be carried out this week

| Day | Tasks | Start Dat | End Date | References |
|---|---|---|---|---|
| 1 | - Implemented a method in Python for figure/skectches/illustrations extractions from PDF<br>- Processed more cases of figure for each problem statements<br>- Changed LLM prompt to deal with tables within PDF to Markdown<br>- Continue to study Qdrant and drafting on what field is needed, which will be embeded for the input. | 6/10/2025 | 6/10/2025 |  |
| 2 | - Implemented markdown to database (MySQL & Qdrant).<br>- Init models for review (Reviewed and Accepted by Quang).<br>- Study on how to prevent loop/Relations between models in Java. | 7/10/2025 | 7/10/2025 | - https://vladmihalcea.com/the-best-way-to-use-the-manytomany-annotation-with-jpa-and-hibernate/<br>- https://vladmihalcea.com/tag/many-to-many/<br>- https://vladmihalcea.com/the-best-way-to-map-a-onetomany-association-with-jpa-and-hibernate/ |
| 3 | - Study for exam.  | 8/10/2025 | 8/10/2025 |  |
| 4 | - Implemented accepted models to database (Without figures).<br>- Faced and solved issue in models that's prevent saving (One to One relation between Statement and Figure).    | 9/10/2025 | 9/10/2025 |  |
| 5 | - Implemented figures saving into databases. (Full connection between statement and figure)<br>- Tested on saving on S3, and fixed where it saving partially, makes it comply with Atomicity in ACID practices.   | 10/10/2025 | 10/10/2025 |  |