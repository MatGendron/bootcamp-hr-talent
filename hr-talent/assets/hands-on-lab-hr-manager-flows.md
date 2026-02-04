# 🧑‍💼 Automating Talent Acquisition with Agentic Workflows

## Table of Contents

- [Use Case description](#use-case-description)
- [Pre-requisites](#pre-requisites)
- [Talent acquisition agent with agentic workflows](#-talent-acquisition-agent-with-agentic-workflows)
  - [Create a Talent Agent](#create-a-new-agent)
  - [Step 1: Create an agentic workflow and configure inputs and outputs](#step-1-create-an-agentic-workflow-and-configure-inputs-and-outputs)
  - [Step 2: User activity to collect number of candidates](#step-2-user-activity-to-collect-number-of-candidates)
  - [Step 3: Code block to store number of candidates](#step-3-code-block-to-store-number-of-candidates)
  - [Step 4: For each loop to upload candidate resumes](#step-4-for-each-loop-to-upload-candidates-resumes)
  - [Step 5: Display message to upload a resume](#step-5-display-message-to-upload-a-resume)
  - [Step 6: Resume file upload](#step-6-file-upload)
  - [Step 7: Document extractor for resumes](#step-7-document-extractor-for-resumes)
  - [Step 8: Store candidate's name and skills](#step-8-store-candidates-name-and-skills-for-later)
  - [Step 9: Display a message to upload a job description](#step-9-display-a-message-to-upload-a-job-description)
  - [Step 10: Upload the job description](#step-10-upload-the-job-description)
  - [Step 11: Document extractor for job skills](#step-11-document-extractor-for-job-skills)
  - [Step 12: Generative prompt - match candidates to job](#step-12-generative-prompt---match-candidates-skills-to-job-skills)
  - [Step 13: Display match summary](#step-13-display-match-summary---output-of-generative-prompt)
  - [Update the agent behavior](#update-agent-behavior)
  - [Test the agent](#test-the-agent)
- [Pulling it all together](#pulling-it-all-together)

## Use Case Description

In the [first part of the HR Talent lab](./hands-on-lab-hr-manager.md) you used the **Chat with documents** feature to upload several resumes and a job description. You then prompted the agent to generate a table comparing candidates' skills to job required skills. In this case the agent's internal LLM does all the work, all that is required from the user is providing the right prompt/query. However, sometimes it may not be obvious what the right prompt is as HR Managers are not prompt engineers. Additionally, we may want to program the agent to run some additional steps, e.g. automatically reach out to the selected candidate, ask them to select an interview time, automatically process the response and add it to the calendar. In this case we may want to create an **agentic workflow**.

An agentic workflow represents a sequence of steps that utilizes conditional controls and activities. Agentic workflows allow us to create sequences of tasks, as well as conditions, branches and loops. We can use a variety of nodes, including small code blocks, user input, document processing nodes to extract data from documents, and generative prompts to create and configure LLM prompts with inputs and outputs.

Rather than handling each step individually, agents can start an angetic workflow to manage the entire process from beginning to end. Agentic workflows are ideal for tasks that require coordination across systems or multiple decision points.

For example, an agentic workflow can be created to handle employee onboarding: collecting information, creating accounts, sending welcome emails, and notifying internal teams. Once built, this agentic workflow can be reused across departments, triggered by agents whenever a new employee joins —- no need to manually coordinate each step.

By using agentic workflows, business users gain:

- Confidence that tasks are completed correctly and consistently.
- Speed through automation of repetitive steps.
- Visibility into how processes run and where bottlenecks occur.
- Scalability to apply the same logic across teams, regions, or products.

## Disclaimer

watsonx Orchestrate is a constantly evolving platform and some of the content in this lab may not faithfully reflect the current interface. Please refer to a bootcamp facilitator in case of issue.

## Pre-requisites

If you haven't yet as part of the earlier steps of the HR Manager lab, download the following files:

[Candidate 1.pdf](../data/Candidate%201.pdf)

[Candidate 2.pdf](../data/Candidate%202.pdf)

[Candidate 3.pdf](../data/Candidate%203.pdf)

[Job Description.pdf](../data/Job%20Description.pdf)

## 🥇 Talent Acquisition Agent with agentic workflows

In this part of the lab we will implement the following workflow:

![Complete agentic workflow diagram showing the sequence of nodes from start to end, including user activities, code blocks, for-each loops, document extractors, and generative prompts](./hands-on-lab-assets/flow_to_build.jpeg)

We will now walk you through creating the above workflow step by step. We will first create a separate agent to experiment.

### Access watsonx Orchestrate

From the ibm cloud home page click on the **resource list** button in the navigation menu on the left-hand side.

![ibm cloud home page](./hands-on-lab-assets/ibm_home_page.png)

In the resource list, under AI/Machine Learning, click the link to your watsonx Orchestrate instance.

![watsonx orchestrate in resource list](./hands-on-lab-assets/resource_list.png)

Click on the "Launch watsonx Orchestrate" button on the next screen.

### Create a new agent

Open the Agent Builder in watsonx Orchestrate, if you aren't there already -- click on **Build->Agent Builder** in the main hamburger menu.

![watsonx Orchestrate navigation menu with Build and Agent Builder options highlighted](./hands-on-lab-assets/open_agent_builder.png)

Create a new agent:

![Agent Builder interface showing the Create new agent button](./hands-on-lab-assets/create_new_agent.png)

Select **Create from scratch**, name it **Talent Agent**, and give it a short description. Descriptions are used to route a user query to the right agent. You can use the description below:

```
This agent helps match candidates to a job based on their skills
```

![Create agent dialog with Talent Agent name and description fields filled in](./hands-on-lab-assets/agent_description.png)

After clicking **Create**, you will be taken to this screen:

![Talent Agent configuration screen showing model selection and settings options](./hands-on-lab-assets/talent_agent_intro.png)

For this agent, we will use the **llama-3-405b-instruct** model. You can select it in the **Model** drop-down:

![Model dropdown menu with llama-3-405b-instruct model selected](./hands-on-lab-assets/agent_change_model.png)

Feel free to experiment with the other (vision) model too, but this one worked better for our use case.

We will leave all the other settings at default values for now. Scroll down to the **Toolset** section. This is where we will be adding our flow (agentic workflow). Click on **Add Tool**:

![Toolset section with Add Tool button highlighted](./hands-on-lab-assets/add_tool.png)

Select **Create an agentic workflow**:

![create workflow button](./hands-on-lab-assets/create_workflow.png)

### Step 1: Create an agentic workflow and configure inputs and outputs

First, we will edit the flow description, input, and outputs. Click on the pencil next to the name of the flow in the top left corner:

![Flow editor with pencil icon next to flow name for editing](./hands-on-lab-assets/edit_flow_description.png)

Change the name to **Match candidates**, change description to:

```
Extracts skills from candidates' resumes, extracts skills from a job description, and generates a summary table showing which candidates have which skills required and preferred for the job.
```

then navigate to the **Parameters** tab.

![Flow configuration dialog showing name, description, and Add output button](./hands-on-lab-assets/flow_description.png)

Click on the **Add output** button to specify the output of the flow.

This is where we will configure the variable that will store the output of the whole flow, returned to the agent after the flow is done running. Select **String** for the type of variable:

![Variable type selection dropdown with String option highlighted](./hands-on-lab-assets/select_string_output.png)

Give it a name e.g. **match_summary** and click **Add**:

![Output variable configuration with match_summary name entered](./hands-on-lab-assets/match_summary_var.png)

After you click on **Save**, your flow show look similar to:

![Flow canvas showing start node connected to end node with 0 inputs and 1 output variable](./hands-on-lab-assets/flow_start.png)

The flow has two nodes only for now - the start node with 0 inputs and 0 variables configured, and the end node with 1 variable configured. You can validate that your output variable was added successfully by clicking on the end node:

![End node details panel showing match_summary output variable configuration](./hands-on-lab-assets/output_node.png)

We will next configure a couple flow variables that we can use througout our flow. We will need two:

- _num_candidates_ - a list that represents a range of integers _0_ to _n_ where _n_ is the number of candidates To upload and process multiple candidate resumes, we will use a **For each** node. In order to do this, we can iteratate over _num_candidates_
- _candidates_ - this is a string variable that will hold extracted candidates' names and corresponding skills. We will need it so we can use it in a generative prompt node

Click on the flow variable button then add a flow variable and select **Integer**:

![Start node context menu with Edit flow variables option](./hands-on-lab-assets/edit_flow_variables.png)

Enter the name of the variable, _num_candidates_ and a description e.g:

```
list of candidates, enum
```

Check the **List of Integer** option since we will have a list, and click on **Add** to add the variable.

Add another variable:

![Add flow variable button for creating additional variables](./hands-on-lab-assets/add_another_var.png)

This time make it a String. Give it the name _candidates_ and a simple description e.g.:

```
candidate names and skills
```

Specify the default (starting) value: "" and click **Add**:

![String variable configuration for candidates with default empty value](./hands-on-lab-assets/candidates_var.png)

Your flow show now look like this:

![Flow canvas with start node showing 2 flow variables configured](./hands-on-lab-assets/start_vars_defined.png)

### Step 2: User activity to collect number of candidates

We will now add our first user activity. The first activity we are going to create will be to ask the user how many candidates they would like to evaluate for the job. Hover over the arrow connecting the start node to the end node and click on the **+** sign:

![Flow canvas with plus icon on arrow between start and end nodes](./hands-on-lab-assets/add_user_activity1.png)

Click on **User activity**:

![Node type selection menu with User activity option highlighted](./hands-on-lab-assets/select_user_activity.png)

Hover over the arrow from Start to End **inside User activity 1**. Click on **+**, then on **Collect from user**, then on **Number**:

![User activity submenu showing Collect from user and Number options](./hands-on-lab-assets/collect_number.png)

Click on **Number 1**, then on the pencil icon to edit the question to display to the user:

```
How many candidates would you like to evaluate?
```

![Number input configuration with question text editor](./hands-on-lab-assets/collect_number2.png)

Your flow should now look like this:

![Flow showing User activity 1 node with number collection configured](./hands-on-lab-assets/number_collected.png)

### Step 3: Code block to store number of candidates

We will now define a node to update the _num_candidates_ variable:

Hover over the arrow connecting the user activity to the end node. Click on the **+** sign and then on **Code block**:

![Node type menu with Code block option highlighted](./hands-on-lab-assets/add_code_block.png)

Click on the new code block node, and open code editor:

![Code block node with Open code editor button](./hands-on-lab-assets/open_code_editor.png)

Enter the folowing code into the editor:

```
numc = flow["User activity 1"]["How many candidates would you like to evaluate?"].output.value
flow.private.num_candidates = list(range(0, numc))
```

And click on the **X** to close the editor:

![Code editor with Python code for storing candidate list](./hands-on-lab-assets/enter_code.png)

Click on the code block again and rename it using the Edit button (pencil):

![Code block node with pencil icon for editing name](./hands-on-lab-assets/edit_code_block_name.png)

Name it **store candidate list** and click **V** to save. Your flow should now look like the following:

![Flow showing User activity 1 connected to store candidate list code block](./hands-on-lab-assets/flow_with_code_block.png)

### Step 4: For each loop to upload candidates resumes

We will next create a **for-each loop** to upload each resume, extract the name of the candidate and their skills, and store all this info in the _candidates_ variable.

Hover over the arrow connecting the code block to the end node and click on the **+** sign, then select **For each**:

![Node type menu with For each loop option highlighted](./hands-on-lab-assets/create_for_each.png)

### Step 5: Display message to upload a resume

Inside the **For each** node, create a **User activity** by hovering over the arrow inside the **For each** node and clicking on the **+** sign. Next, hover over the inside of the **User activity**, click on **+**, select **Display to user**, then **Message**:

![User activity submenu showing Display to user and Message options](./hands-on-lab-assets/add_display_message.png)

Next click on **Message** and edit the **Output message**:

```
Please upload a candidate's resume
```

This is what will be displayed to the user to ask them to upload a resume.

At the same time change the node name to:

```
Prompt user to upload resume
```

![Message node configuration with prompt text for resume upload](./hands-on-lab-assets/upload_resume_message.png)

### Step 6: File upload

Add another **User Activity**:

![Plus icon for adding another user activity node in the flow](./hands-on-lab-assets/add_another_user_activity.png)

This time it will be a **File Upload** activity:

![User activity submenu with File upload option highlighted](./hands-on-lab-assets/create_file_upload.png)

Click on the new \*File upload** node and rename it to **Upload resume\*\*:

![File upload node with name field showing Upload resume](./hands-on-lab-assets/rename_file_upload.png)

### Step 7: Document extractor for resumes

Next we will create a document extraction node to extract the candidate's name and skills from their resume.

Still inside the **For all** loop, hover over the last arrow and click on **+** to create a new **Document extractor** node:

![Node type menu with Document extractor option highlighted](./hands-on-lab-assets/create_doc_extractor.png)

For this lab, select **Structured** when creating document extractors.

Click on the **Document extractor** node and then **Edit fields**:

![Document extractor node with Edit fields button](./hands-on-lab-assets/edit_doc_extractor_fields.png)

We will now upload one of the resumes as a sample to train the document extractor. Drag and drop the [Candidate2.pdf](../data/Candidate%202.pdf) file you downloaded earlier in the lab:

Once the document is done uploading, you will see the following screen. Click on **Add field** to start adding fields we want to extract and train the document extractor on:

![Document extractor interface showing uploaded resume with Add field button](./hands-on-lab-assets/doc_extractor_show.png)

Enter **Name** for the name of the field and hit Enter. The document extractor will try to extract the name from the resume and will display it once ready:

![Document extractor showing extracted Name field with candidate name](./hands-on-lab-assets/candidate_name.png)

Next we need to add another field **Skills**. Add one more field and name it **Skills**. Once you hit Enter, the document extractor will populate the field from the document:

![Document extractor showing Name and Skills fields extracted from resume](./hands-on-lab-assets/candidate_skills.png)

Rename the document extractor node to **Resume extractor** by clicking on it and editing it's name

Your **For each** loop should now look like this:

![For each loop containing message, file upload, and resume extractor nodes](./hands-on-lab-assets/for_each_after_extractor.png)

### Step 8: Store candidate's name and skills for later

The last activity we need to create in the **For each** loop is another code block that stores the candidate's name and skills after each iteration:

![Code block node added at end of for each loop](./hands-on-lab-assets/store_candidate_info.png)

Click on the code block and open the code editor. Enter the following in the code editor:

```
flow.private.candidates += "Name: " + str(flow["For each 1"]["Resume extractor"].output.name) + "\n\nSkills: " + str(flow["For each 1"]["Resume extractor"].output.skills) + "\n\n"
```

Rename the code block to **Update candidates**. The **For each** should now look like this:

![Complete for each loop with all nodes including Update candidates code block](./hands-on-lab-assets/for_each_final.png)

### Step 9: Display a message to upload a job description

Next we will ask the user to upload a job description. First we will display a message to the user, then we will add a file upload activity.

**Below** the **For each** loop, click on the arrow connecting to the end node and add a **User activity**:

![Plus icon on arrow after for each loop for adding user activity](./hands-on-lab-assets/add_user_activity_job.png)

Click inside the user activity and select **Display to user** then **Message**:

![User activity menu with Display to user and Message options](./hands-on-lab-assets/display_to_user_job_upload.png)

Update the **Output message** to:

```
Please upload the job description
```

And change the **Display message** (name of the node in the flow) to:

```
Prompt user to upload job description
```

![Message node configuration for job description upload prompt](./hands-on-lab-assets/configure_display_to_user_job_upload.png)

### Step 10: Upload the job description

Add another **User activity** right before the end node. This time make it of type **File upload**:

![File upload node type selection for job description](./hands-on-lab-assets/file_upload_job.png)

Click on the newly created file upload node and change its' name to:

```
Upload job description
```

![File upload node renamed to Upload job description](./hands-on-lab-assets/change_file_upload_job_node_name.png)

### Step 11: Document extractor for job skills

Next we will create another document extractor node to extract required and preferred skills from the job description.

Add a **Document extractor** node before the end of the flow and rename it as **Extract job skills**:

![Document extractor node renamed to Extract job skills](./hands-on-lab-assets/extract_job_skills.png)

Edit the fields of this document extractor node and drag and drop [the job description file](../data/Job%20Description.pdf) that you downloaded earlier:

![Document extractor interface with job description file being uploaded](./hands-on-lab-assets/job_file_drag_drop.png)

Once the document has been processed, you will see the following screen:

![Document extractor showing processed job description document](./hands-on-lab-assets/job_desc_doc_proc.png)

Add two fields, similar to what you did for the resume extractor. This time, however, add fields **required** and **preferred** to extract required and preferred skills:

Close the extractor node once done.

### Step 12: Generative prompt - match candidates' skills to job skills

We are finally almost at the end of the flow. We still need to implement a Generative prompt. This prompt will take as input the value of _candidates_ variable, which is a string that contains by now all candidate names and their skills. It will also take as input the required and preferred skills just extracted from the job description. The prompt will compare the skills of each candidate to the skills required by the job and generate a table which summarizes how well candidate skills map to the job description.

Add a **Generate prompt** node at the end of the flow (before the end node):

![Node type menu with Generate prompt option highlighted](./hands-on-lab-assets/generative_prompt.png)

Rename the node to **Match candidate skills to job skills** and edit **Prompt settings**:

![Generative prompt node with edit options and Prompt settings button](./hands-on-lab-assets/rename_prompt_node.png)

Select the **llama-3-2-90b-vision-instruct** model.

For system prompt enter the following:

```
You are a helpful assistant who can match candidates skills to job requirements.
```

For user prompt enter:

```
Make a table where each row is a candidate and each column is a skill in the job description. Do not invent any candidates. Have the check emoji if the candidate does have the corresponding skill. Mark columns for required job skills with *. Include the candidate's name in each row.

Candidate names and skills:
Required job skills:
Preferred job skills:
```

In order to work, our generative prompt will need to take **as input** a string that contains candidate names and skills extracted earlier in the flow. It will also need two strings for skills (required and preferred) from the job description itself. Therefore, we need to create three _String_ input varilables that will hold these values and that we can reference in the user prompt as variables.

We can also provide a sample test value for each variable so we can run the prompt directly in the generative prompt editor and double check that the output is as expected, without having to quit and run the whole flow.

Add the following _String_ input variables: _candidates_, _job_required_, and _job_preferred_ and assign some test values e.g.:

![Generative prompt input variables section with add variable button](./hands-on-lab-assets/create_new_var_gp.png)

Fill in the name of the var and add a simple description:

![Variable creation dialog with candidates name and description fields](./hands-on-lab-assets/create_candidates_var_gp.png)

Edit the var to add a test value:

![Variable editor with Add test value option](./hands-on-lab-assets/add_test_value.png)

Paste the following text to add the value:

```
Name: Jane Smith
Skills: Java, Javascript

Name: John Doe
Skills: Java, Python, Javascript, ML
```

Your _candidates_ variable show now look like this:

![Candidates variable with test values showing sample names and skills](./hands-on-lab-assets/prompt_candidates_var.png)

Follow the steps above to add two more _String_ variables, _job_required_ and _job_preferred_:

![Input variables showing candidates, job_required, and job_preferred with test values](./hands-on-lab-assets/job_reqs.png)

Finally reference these variables in your prompt by clicking the **X** sign in the user prompt area:

![User prompt editor with variable insertion icon highlighted](./hands-on-lab-assets/select_vars.png)

Your prompt should now look like this:

![Complete generative prompt configuration with system prompt, user prompt, and variables](./hands-on-lab-assets/generative_prompt_finished.png)

Click on **Generate response** to run the prompt on the test values you provided and observe the results returned:

![Generative prompt test results showing candidate skills comparison table](./hands-on-lab-assets/gen_prompt_test.png)

As you can see, the result is a table which compares each candidate's skills with the job requirements. This is exactly what we were looking for, so we have validated that our generative prompt works and can move on to the next step.

Close the prompt definition now, click on the Generative prompt node you just created and **Edit data mapping**:

![Generative prompt node with Edit data mapping option](./hands-on-lab-assets/edit_data_mapping.png)

We now need to map data collected earlier in the flow to the inputs of the generative prompt.

Click on the **variable** icon in the _candidates_ row:

![Data mapping interface showing candidates input with variable icon](./hands-on-lab-assets/prompt_edit_candidates_input.png)

In the editor select **Flow variables -> candidates**:

![Variable selector showing Flow variables with candidates option](./hands-on-lab-assets/select_candidates_fv.png)

For _job_preferred_, also select the **variable icon** and choose **Extract job skills -> preferred**

![Variable selector showing Extract job skills node with preferred field](./hands-on-lab-assets/select_job_preferred.png)

Similarly, for _job_required_ select the variable icon and chooose **Extract job skills -> required**

![Variable selector showing Extract job skills node with required field](./hands-on-lab-assets/select_job_required.png)

### Step 13: Display match summary - output of generative prompt

Finally, create one last **User activity** node to display the output of the generative prompt:

![User activity node added to display generative prompt output](./hands-on-lab-assets/display_output.png)

Update the node name to **Show summary** and click on **Select variable** to select the output message:

![Show summary node configuration with Select variable option](./hands-on-lab-assets/edit_output_node.png)

In the editor select the generative prompt node name, then select the corresponding output variable _value_:

![Variable selector showing generative prompt output value variable](./hands-on-lab-assets/select_prompt_output_var.png)

We are finally done defining the flow. Click on **Done** to close the flow.

### Update Agent Behavior

Before testing the agent, let's complete the **Behavior** section. Use the following instructions:

```
When asked to match a candidate to job or to recommend the best candidate for a job, call the 'Match candidates' tool.  All other questions should be answered based on the context in the chat.
```

![Agent behavior configuration with instructions for Match candidates tool](./hands-on-lab-assets/behavior.png)

### Test the agent

Test your agent by providing two candidate resumes. Enter the following query in chat:

```
recommend a candidate for a job
```

The agent will ask you how many candidates you would like to evaluate. Answer: 2

You will then be asked to upload a candidate's resume. You can upload any candidate's resume, for example [Candidate 3.pdf](../data/Candidate%203.pdf). Note you may be asked to review the extraction results - if the extractor's confidence is below 95%, human validation will be required. This behavior can be easily configured within the document extractor node. The same is true for any other uploaded documents.

You will then be asked to upload the second resume. You can upload another candidate's resume, for exmaple [Candidate 1.pdf](../data/Candidate%201.pdf).

You will finally be asked to upload a job description. You can use [Job Description.pdf](../data/📄%20Job%20Description.pdf).

The results should look similar to the following:

![Agent output showing skills comparison table with candidates and job requirements](./hands-on-lab-assets/table_output.png)

As you can see, the columns marked with \* are skills required by the job. Other skills are preferred.
Each candidate row shows which skills the candidate has.

The agent summarizes by telling us who the recommended candidate is:

![Agent response recommending the best candidate based on skills match](./hands-on-lab-assets/recommended_candidate.png)

## Troubleshooting

By default, watsonx Orchestrate uses AI to determine node inputs. In case of issue when executing the flow, you can try to specifiy node inputs.<br>
In the flow for this lab this can apply to the `Resume extractor`, `Extract job skills`and `Match candidate skills to job skills` nodes.

To specify node inputs, click on a node, then click on **Edit data mapping**:

![Edit data mapping button](./hands-on-lab-assets/troubleshoot_edit_data_mapping.png)

Click on the **variable** icon:

![Variable icon](./hands-on-lab-assets/troubleshoot_variable.png)

Then choose the relevant data:

![Choose data](./hands-on-lab-assets/troubleshoot_select_var.png)

## Pulling it all together

In this part of the lab we automated the process of extracting skills from resumes and the job description and summarizing how well the candidates' skills match the skills required and preferred for the job. We used **document processing** nodes to define the fields to be extracted from documents and to train the document processor. We then fed the output of the document processing nodes as input into the **generative prompt** node which composed the right prompt for the LLM to summarize how well candidate skills match the job requirements.
We could easily expand this workflow with additional nodes and branches, for example to send an email to the highest-ranked candidates, to ask them to pick an interview slot, and to confirm their response was received. Running these tasks as a workflow allows for a more deterministic way to handle repetitive tasks, so that the agent can drive the process and involve the HR Manager know whenever their input is needed.

As you noticed when you tested the flow, depending on how the confidence thresholds are set up in the document processing nodes, human verification can be requested to make sure field data is extracted correctly.

Combining agentic workflows with regular tools and individual tasks in an agent provides the greatest flexibility. A user can chat with the agent and invoke individual tasks as needed. For more complex, multi-step processes, agentic workflows are a powerful tool that can manage the entire process from beginning to end.
