---
title: FAQ for dialog with data (preview)
description: This FAQ provides information about the AI technology used in dialog with data (preview). It provides key considerations and details about how AI is used, how it was tested and evaluated, and any specific limitations.
ms.date: 09/01/2023
ms.custom: 
  - responsible-ai-faqs
ms.topic: article
author: wmelewong
ms.author: wameng
ms.reviewer: m-hartmann
---

# FAQ for dialog with data (preview)

[!INCLUDE [public-preview-banner](includes/public-preview-banner.md)]

These frequently asked questions (FAQ) describe the AI impact of Dynamics 365 Customer Insights - Data dialog with data (preview) feature.

[!INCLUDE [public-preview-note](includes/public-preview-note.md)]

## What is dialog with data?

This AI-powered feature helps generate insights about your customer data estate using natural language, which empowers you to design personalized and high-ROI business actions.

## What are the feature’s capabilities?

Dialog with data allows you to enter your question about customers in natural language and generate answer within seconds. The feature generates extensive information related to your original question that provides further insights and suggested questions to continue discovering new insights about your customers.  

## What is the feature’s intended use?

The intention is to democratize the power of generating customers insights embedded in your data estate. It aims to let you do these tasks faster and without the need of technical data query skills.

## How was dialog with data evaluated? What metrics are used to measure performance?

This feature has only been evaluated in English at this time.  

The feature is evaluated for accuracy of understanding user prompts, the generated results, and the performance of generating results.

In addition, it's evaluated with embedded monitoring for Responsible AI. Specifically, this feature has been tested with an array of scenarios to ensure it captures and mitigates issues like inappropriate language usage, malicious intention of jail break, and data fabrication.  

## What are the limitations of dialog with data? How can users minimize the impact of the limitations when using the system?

The feature transforms your prompts in natural language into a SQL query to run by Synapse Serverless Server, and generates result that gets translated back into natural language response.

- Only questions related to your customers are supported. We suggest you don't enter any non-business-related questions.

- Only questions with corresponding data existing in Customer Insights - Data are supported.

- ‘Did you know’ section and ‘Explore further’ result generation is dependent on Azure Open AI service model.  

The feature is available in the United States and supports English only.

## What operational factors and settings allow for effective and responsible use of the feature?

- When you enter a prompt, you can use a toggle to select your data sharing preference.

- There's reminder that the result is AI-generated and can be inaccurate. We suggest you always review and validate the AI-generated result before applying it for business actions.

- In general, you should use the feature in a mindful way to ensure responsible and appropriate inputs and outputs to minimize bias and unfairness.

## See also

- [Have a dialog with data using Copilot in Customer Insights (preview)](dialog-with-data.md)

[!INCLUDE[footer-include](includes/footer-banner.md)]
