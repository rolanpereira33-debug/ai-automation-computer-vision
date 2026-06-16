---
description: Roboflow, Python, Discussion
---

# What Makes Good Sports Data?

### Learning Objectives

* Explain why data quality determines model quality - 'garbage in, garbage out'
* Identify the 7 core data quality issues specific to sports CV datasets
* Distinguish between class imbalance, label noise, and distribution bias
* Explain how motion blur, occlusion, and lighting variation affect detection accuracy
* Evaluate a real dataset in Roboflow and flag specific quality problems with evidence
* Recommend concrete fixes for each quality issue they identify
* Articulate the quality vs quantity trade-off in practical terms



All who train a model will eventually ask: 'Why is my model bad?' The answer is almost always the data. Not the model architecture. Not the hyperparameters. The data. This session installs that instinct before you touch a single model.

| A model cannot learn what the data does not show it. A model will learn every mistake in the data not just the correct patterns. More data only helps if that data is representative, correctly labelled, and diverse. In sports CV specifically: lighting, motion, occlusion, and kit colour variation |
| ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |

\
<br>
