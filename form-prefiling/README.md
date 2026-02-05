# 🧑‍💼 Digital Me Form Prefilling Lab

## Table of Contents

- [Use case description](#use-case-description)

## Use Case Description

This lab will guide you through the steps required to implement the first use case of the API Onboarding Proof of Concept, namely form prefilling. The form prefilling use case aims at providing a way to improve user experience of API requesters through an agentic solution that can rely on design gate documents, APIC team documentation, and some other available documents to prefill a significant number of the fields that are part of the Digital Me API request form.

![Digital Me Form overview](images/digital_me.png)

Due to time constraints, we will build an agent able to prefill a couple of fields within a web mockup of the Digital Me API request form by extracting the values from a [PDF export of a previously filled form](assets/sample_form.pdf).

## Disclaimer

watsonx Orchestrate is a constantly evolving platform and some of the content in this lab may not faithfully reflect the current interface. Please refer to a bootcamp facilitator in case of issue.

## 🥇 Form Prefilling Agent
We will now walk you through the steps required to create a form prefilling agent.

### Create a new agent

Open the Agent Builder in watsonx Orchestrate, if you aren't there already -- click on **Build->Agent Builder** in the main hamburger menu.

![watsonx Orchestrate navigation menu with Build and Agent Builder options highlighted](./hands-on-lab-assets/open_agent_builder.png)

Create a new agent:

![Agent Builder interface showing the Create new agent button](./hands-on-lab-assets/create_new_agent.png)

Select **Create from scratch**, name it **Form Prefilling Agent**, and give it a short description. Descriptions are used to route a user query to the right agent. You can use the description below:

```
This agent helps prefilling the Digital Me API request form by extracting fields values from documents uploaded by user
```

![step-1](images/step-1.png)

After clicking **Create**, you will be taken to this screen:

![step-2](images/step-2.png)

For this agent, we will use the **llama-3-2-90b-vision-instruct** model. You can select it in the **Model** drop-down:

![step-3](images/step-3.png)

Feel free to experiment with the other model too.

We will leave all the other settings at default values for now. Scroll down to the **Toolset** section. This is where we will be adding our agent tools: a workflow to handle extraction of useful information from documents and a tool to prefill the Digital Me API request form. Click on **Add Tool**:

![step-4](images/step-4.png)

### Definition of a OpenAPI tool
We will start by defining a tool that will allow agent to trigger prefilling of the Digital Me API request form by invoking a REST api.

In the **Add a tool** modal, select **OpenAPI**:

![step-5](images/step-5.png)

Download this [OpenAPI definition of the REST api](assets/digital_me_api_wxo_openapi.json).

Then click on **Drag and drop an OpenAPI file here or click to upload** to upload the OpenAPI definition:

![step-6](images/step-6.png)

Then click on **Next**:

![step-7](images/step-7.png)

Select the **Initialize Form** operation, then click on **Done** to define the tool:

![step-8](images/step-8.png)

Once the tool has been defined, click on **Add tool** to define the workflow that will handle extraction of useful information from documents in order to trigger form prefilling.

![step-9](images/step-9.png)

### Definition of the form prefilling workflow

Select **Create an agentic workflow**:

![step-10](images/step-10.png)

Name your agentic workflow **Form Field Values Extraction Workflow**, then click on **Start building**:

![step-11](images/step-11.png)

Click on **Edit details** to set workflow properties:

![step-12](images/step-12.png)

In the Overview tab, set a description for the workflow:

```
Extracts significant information from documents uploaded by user in order to prefill the Digital Me API request form
```

![step-13](images/step-13.png)

In the Parameters tab, click on **Add output** to define an output for the workflow. The output will be the initialized form url, of type **string**:

![step-14](images/step-14.png)

```
Name: initialized_form_url
Description: url of the prefilled form
```

Click on **Done** to save the settings:

![step-15](images/step-15.png)

The flow has two nodes only for now - the start node with 0 inputs and 0 variables configured, and the end node with 1 variable configured. 

We will start by handling file upload. Click on **Add +**, then **User activity**:

![step-16](images/step-16.png)