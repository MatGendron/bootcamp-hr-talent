# 🧑‍💼 Agentic HR Management

`Chat with documents` `RAG` `Flows` `Intelligent Document Processing` `Multi-agent orchestration`
![Bootcamp Illustration](./images/bootcamp_illu.png)

## 🤔 The Problem

This is the story of **Luisa**. **Luisa** is an HR manager for a large corporation that's hiring 5,000 employees for their new division. Her struggle is two-fold:

1. **Recruiting candidates** for their open positions
2. **Handling reports** from employees for potential Business Conduct Guidelines violations.

For recruiting, Luisa gets many PDFs with candidate résumés. She has to:

- Check if candidates **fulfill the requirements** of a given position
- Fill in a **table** with the skills/experience of each candidate
- Select **candidates** to be interviewed
- Assign **interviewers** from the team
- Coordinate **interviews** with candidates and interviewers via email
- Schedule **interviews**
- Compile **feedback** from different reviewers
- **Report back** the results to the hiring manager

Luisa would like to make her hiring process more efficiently.

## 🎯 Objective

In this lab you will automate many of the tedious tasks related to recruiting talent in Luisa's organization, as well as providing an AI tool to help Luisa review reports for potential violations to the Business Conduct Guidelines of the company.

## 📈 Business value

Luisa and her team would be able to save hundreds of hours spent scanning résumés and job descriptions manually by leveraging Agentic AI. Also, Luisa will save some time matching which potential sections of the BCG might be infringed in each of the cases she's reviewing.

## 🏛️ The solution

![An Agentic Example](./images/agentic_example.png)

## 📄 Hands-on step-by-step lab

If you do not already have access to a watsonx Orchestrate instance, please refer to the [Instructions for watsonx Orchestrate Free Trial](./instructions-for-wxo-trial.md) guide to create one.

Please find the step-by-step instructions [in the Chat with Documents lab guide](/hr-talent/assets/hands-on-lab-hr-manager.md) on how you can implement this use case.

You can also directly move on the [the Flow Builder lab guide](/hr-talent/assets/hands-on-lab-hr-manager-flows.md) to learn how to build the the agentic workflow for this use case.

## 🏋 Practice on an API onboarding use case

To apply what has been learned to an API onboarding use case, we have prepared a [third lab](/form-prefiling/README.md) to guide you through the steps required to implement the first use case of the API Onboarding Proof of Concept, namely form prefilling. 

The form prefilling use case aims at providing a way to improve user experience of API requesters through an agentic solution that can rely on design gate documents, APIC team documentation, and some other available documents to prefill a significant number of the fields that are part of the Digital Me API request form.

![workflow overview](/form-prefiling/images/workflow.png)