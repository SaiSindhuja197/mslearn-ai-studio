# Generative AI Application Using Your Own Data

## Estimated Duration: 60 Minutes

## Lab Overview

Retrieval Augmented Generation (RAG) is a design pattern that enables AI developers in Azure to build intelligent applications by combining large language models with organization-specific data. Instead of relying only on the model’s pre-trained knowledge, RAG retrieves relevant information from custom data sources—such as documents, databases, or knowledge bases—and incorporates it into the prompt to generate more accurate, context-aware responses. This approach is widely used for developing chat-based and enterprise AI applications. In this exercise, AI developers will use Microsoft Foundry within Azure to integrate custom data into a generative AI solution, enabling more reliable and domain-specific outputs.

## Lab Objectives

    - Task 1: Provision Microsoft Foundry Hub and Project
    - Task 2: Deploy a Model
    - Task 3: Use Prompt Engineering in the Playground
    - Task 4: Create a RAG application
    - Task 5: Run the RAG application

## Task 1: Provision Microsoft Foundry Hub and Project

To use the Foundry features in this task, you must first create a project that is built on a Foundry hub resource. The hub acts as the central resource that manages shared services such as model access, connections, and security. Once the hub is created, projects linked to it can access these capabilities and allow you to build, manage, and deploy your AI solutions.


1. In a new window browser, navigate to the **Microsoft Foundry** portal at `https://ai.azure.com` and Click on **Sign in to get started (1)**. 

    ![](./media/foundryportal.png)

    >**Note**: Close any tips or quick start panes that are opened the first time you sign in, and if necessary use the Foundry logo at the top left to navigate to the home page, which looks similar to the following image (close the Help pane if it’s open).

1. Once after the login is succeeded, you should see the Home page of the **Microsoft Foundry**.

    ![](./media/home.png)

1. Navigate to `https://ai.azure.com/managementCenter/allResources`. 

    ![](./media/mgmtcentre.png)

1. Select **Create new (1)**, then choose the option to create a new **AI hub resource (2)** then select **Next (3)**.

    ![](./media/create.png)

    ![](./media/create2.png)

1. In the **Create a new project** wizard, enter **aiproject- <inject key="DeploymentID" enableCopy="false" />(1)** for your project. Select the option **Rename hub (2)** to create a new hub. 

    ![](./media/T1S5.png)

1. Enter **aihub- <inject key="DeploymentID" enableCopy="false" /> (1)** name for your new hub and click on **Next (2)**.

    ![](./media/T1S6.png)

1. Expand **Advanced options (1)**, and set the following settings for your project:

    - **Subscription**: Use an existing Azure subscription **(2)**
    - **Resource group**: Select **openai-<inject key="DeploymentID" enableCopy="false" /> (3)**
    - **Region**: Select **East US2 / Sweden Central (4)**
    - **Foundry or Azure OpenAI**: Click on **Create new Foundry (5)**, provide name as **aifoundry-<inject key="DeploymentID" enableCopy="false" /> (6)** and click on **ok(7)**
    - Click on **Create (8)**

    ![](./media/T1S7.png)


1. Now lets wait for your project to be created.

    ![](./media/T1S8.png)

1. Once the project is successfully created, you will be automatically navigated to the overview page of the **aiproject- <inject key="DeploymentID" enableCopy="false" />**.

    ![](./media/T1S9.png)

## Task 2: Deploy a model

In this task, you will configure two models within your Azure Foundry project to implement a Retrieval Augmented Generation (RAG) solution. First, you will use an embedding model to convert your custom text data into vector representations, enabling efficient indexing and retrieval of relevant information. Then, you will use a generative language model to process the retrieved data and generate accurate, context-aware natural language responses. Together, these models enable your solution to deliver intelligent answers grounded in your organization’s data.

1. In **aiproject- <inject key="DeploymentID" enableCopy="false" />** inside the **Micrsoft Foundry**, select **Model catalog** from the navigation pane on the left.

    ![](./media/T2S1.png)


1. On the filter panel, select the **Collections (1)** and check the box fror **Azure OpenAI (2)** to filter by Azure OpenAI collections only.

    ![](./media/T2S2.png)

1. Search for **gpt-4.1(1)**, select it **(2)**. On the detail page select **Use this model (3)**.

    ![](./media/T2S3.png)

    ![](./media/T2S3i.png)

1. There will be a pop-up with purchase options appears, select the **Direct from Azure models** option.

    ![](./media/T2S4.png)

1. Use the following settings in the **Deploy model wizard** and then click on **Deploy**:

    - **Deployment name**: **`gpt-4.1` (1)**
    - **Deployment type**: **`Global Standard` (2)**
    - In order to edit the Deployment details click on **Customize (3)**.

        ![](./media/T2S8i.png)

    - **Model version**: **`2025-04-14(Default)`(4)**
    - **Connected AI resource**: **`Select the resource created previously` (5)**
    - **Tokens per Minute Rate Limit (thousands)**: **`10k` (6)**
    - **Content filter**: **`DefaultV2` (7)**

        ![](./media/T2S8ii.png)
    
    > **Note:** If your current AI resource location doesn’t have quota available for the model you want to deploy, you will be asked to choose a different location where a new AI resource will be created and connected to your project.

    > **Note:** Reducing the Tokens Per Minute (TPM) helps avoid over-using the quota available in the subscription you are using. 10,000 TPM is sufficient for the data used in this exercise.

1. Now lets return to the **Models catalog (1)** page, Make sure the collection is set to **Azure OpenAI (2)**  search and select for **text-embedding-ada-002 (3)**.On the detail page select **Use this model (4)**.

    ![](./media/T2S6.png)

    ![](./media/T2S6i.png)

1. There will be a pop-up with purchase options appears, select the **Direct from Azure models** option.

    ![](./media/T2S4.png)

1. Use the following settings in the **Deploy model wizard** and then click on **Deploy**:

    - **Deployment name**: **`text-embedding-ada-002` (1)**
    - **Deployment type**: **`Global Standard` (2)**
    - In order to edit the Deployment details click on **Customize (3)**.

        ![](./media/T2S5i.png)

    - **Model version**: **`2(default)`(4)**
    - **Connected AI resource**: **`Select the resource created previously` (5)**
    - **Tokens per Minute Rate Limit (thousands)**: **`10k` (6)**
    - **Content filter**: **`DefaultV2` (7)**

        ![](./media/T2S5ii.png)
    
    > **Note:** If your current AI resource location doesn’t have quota available for the model you want to deploy, you will be asked to choose a different location where a new AI resource will be created and connected to your project.

1. Once the models are deployed, on the left navigation click on **Models + Endpoints (1)** and view the newly deployed models **(2)**.

    ![](./media/T2S9.png)

## Task 3: Use Prompt Engineering in the Playground

Before using your index in a RAG-based prompt flow, let’s verify that it can be used to affect generative AI responses.

1. In the navigation pane on the left, select the **Playgrounds (1)** page and click on the **Try the Chat playground (2)**.

    ![](./media/T5S1.png)

1. On the **Chat playground** page, in the Setup pane, ensure that your **gpt-4.1 [version:2025-04-14 (Default)](1)** model deployment is selected. 

    ![](./media/T5S2.png)

1. Then, in the main chat session panel, enter the prompt 
    ```
    Where can I stay in New York?
    ```
    ![](./media/T5S3.png)

1. Review the response, which should be a generic answer from the model without any data from the index.

    ![](./media/T5S4.png)

1. In the **System message field (1)**, enter the following prompt and click on **Apply Changes (2)**,then click on **Continue (3)** on update system message wizard:

    ```
    You are a travel assistant that provides information on travel services available from Margie's Travel.

    ```
    ![](./media/T5S5.png)
    ![](./media/T5S5i.png)

1. In the chat window, enter the same query and review the response.

    ```
    Where can I stay in New York?
    ```
1. Lets give a try with another prompt as below and observe how the model responds without grounding data.

    ```
    What destinations does Margie's Travel offer?
    ```
    ![](./media/T5S7.png)

## Task 3: Add grounding data in the playground

In this task, you will add data to your Azure Foundry project to support your generative AI application. The dataset consists of travel brochures in PDF format from the fictitious travel agency Margie’s Travel. You will upload these documents to the project so they can be indexed and used as a custom data source. This step ensures that the AI solution can retrieve relevant information from the brochures and generate accurate, context-aware responses based on their content.

1. Open **File Explorer 📁 (1)** from the **Start** menu and navigate to `C:\labfiles`. 

1. Create a new folder **📁New Folder (1)** and name it as **brochures (2)**.

1. In a new browser tab, download the zipped archive of brochures on to the **LabVM**.
    ```
    https://github.com/MicrosoftLearning/mslearn-ai-studio/raw/main/data/brochures.zip 
    ```
1. Open downloaded folder location **📁(1)**.Extract it to a folder named **brochures** on LabVM i.e,`C:\labfiles`.

1. Navigate back to the Foundry portal, on the Chat Playground wizard in **Add your data (1)** section click on **+Add new data source (2)**.

    ![](./media/T3S5.png)

1. In the **Add your data** wizard, add a new index with the following settings:

    - **Source data**:

        - **Data source**: **`Upload files` (1)**.
        - Click on **`Upload`(2)** 
            
            ![](./media/T3S6.png)

        - On the File explorer dialogue, Select the all the files from **`brochures`** folder as data source.

            ![](./media/.png)

        - Click on **`Next`**.

            ![](./media/T3S6ii.png)
            
    - **Index configuration**:

        - **Select Azure AI Search Service**: **`Create a new Azure AI Search resource`**.
        
            ![](./media/T3S6iii.png)

            > **Note**: This will redirect you to the Azure Portal

        - Provide the following details to create Azure AI Search Service:

            - **Subscription**: Choose the Default Azure subscription **(1)**
            - **Resource group**: Select **openai-<inject key="DeploymentID" enableCopy="false" /> (2)**
            - **Service name**: Name the resource name as **aisearch-<inject key="DeploymentID" enableCopy="false" /> (3)**
            - **Location**: Use the same location as your AI hub resource **(4)**
            - **Pricing tier**: Make sure **Basic (5)** is selected
            - Click on **Review + Create (6)** and then **Create**.

            ![](./media/T4S2iii.png)

            ![](./media/T4S2iv.png)

1. Wait for the **AI Search** resource to be created. 

    ![](./media/T4S3.png)

1. Lets return to the **Create a vector index** wizard in Microsoft Foundry, click on the drop down of **Select Azure AI Search service** and select **Connect other Azure AI Search (1)** resource and adding a connection to the AI Search resource you just created by clicking on **Add Connection (2)**.

    ![](./media/T3S8.png)

    ![](./media/T3S8i.png)

1. Once the AI search connection is established, from the drop down select **aisearch-<inject key="DeploymentID" enableCopy="false" />**

    - Vector index: **`brochures-index` (1)**
    - Virtual machine: **`Auto select`(2)**
    - Click on **Next (3)**

        ![](./media/T3S9.png)

    - **Search settings**:
        - **Vector settings**: Make sure the checkbox is checked for **`Add vector search to this search resource`(3)**
        - **Azure OpenAI connection**: Select the default Azure OpenAI resource for your hub **(4)**.
        - **Embedding model**: **`text-embedding-ada-002`** **(5)**
        - Embedding model deployment: **`text-embedding-ada-002`** **(6)**
        - Click on **Next (7)**

        ![](./media/T3S9i.png)
    - **Review and Create** : Click on **`Create vector index`** **(8)**

        ![](./media/T3S9ii.png)

1. Wait for the indexing process to be completed, which can take a while depending on available compute resources in your subscription.

1. The index creation operation consists of the following jobs:

    - Crack, chunk, and embed the text tokens in your brochures data.
    - Create the Azure AI Search index.
    - Register the index asset.

## Task 4: Creating RAG application


1. On the **Microsoft Foundry** portal, go to the Overview page for your project.

1. Find the Foundry endpoint displayed on the welcome screen (for example, https://<your-resource>.services.ai.azure.com/api/projects/<your-project>). Copy this endpoint — you’ll use it to connect to your model.

    > **Note**: The Microsoft Foundry SDK handles authentication and endpoint routing automatically when you use AIProjectClient.get_openai_client(). Make a note of this endpoint.

1. Open Visual Studio Code on your local computer. 

1. Open a terminal in VS Code (Terminal > New Terminal) and clone the GitHub repo containing the code files for this exercise:

    ```
    git clone https://github.com/microsoftlearning/mslearn-ai-studio mslearn-ai-foundry
    ```
1. After the repo has been cloned, open the folder in VS Code (File > Open Folder), and navigate to the mslearn-ai-foundry/labfiles/foundry-rag/python folder.

1. In the VS Code Explorer pane, review the files in the folder:

    - `.env` - A configuration file for application settings.
    - `rag-app.py` - The Python code file for the RAG application.
    - `requirements.txt` - A file listing the package dependencies.

1. Open a terminal in VS Code and navigate to the project folder, then install the required libraries:

    ```
    cd mslearn-ai-foundry/labfiles/foundry-rag/python
    python -m venv labenv
    ``` 
1. Activate the virtual environment:

    ```
    labenv\Scripts\activate
    ```
1. Install the required packages:

    ```
    pip install -r requirements.txt
    ``
1. In VS Code, open the **`.env`** file, replace the placeholders and then Save the **`.env`** file.:

    - Replace your_foundry_endpoint with the Foundry endpoint you copied from the project overview page.
    - Replace your_model_deployment with the name of your gpt-4.1 model deployment (for example, gpt-4.1).

    > **Note**: If you deployed your gpt-4.1 model to a different region due to insufficient quota, on the Models + Endpoints page, select your model and use its Target URI as your endpoint instead.


1. Copy the brochures folder you extracted earlier into the mslearn-ai-foundry/labfiles/foundry-rag/python folder. The code will upload these files to create a vector store for file search, lets proceed to write the code to implement the RAG pattern.

1. In VS Code, open the **`rag-app.py`** file.
In the code file, note the existing statements that have been added at the top of the file to import the necessary packages. Then, find the comment Add references, and add the following code to reference the libraries you installed:

    ```
    # Add references
    import glob
    from azure.identity import DefaultAzureCredential
    from azure.ai.projects import AIProjectClient
    ```
1. In the main function, under the comment Get configuration settings, note that the code loads the Foundry endpoint and model deployment name values you defined in the configuration file.
Find the comment Initialize the project client, and add the following code to connect to your Microsoft Foundry project:

    ```
    # Initialize the project client
    project_client = AIProjectClient(
        endpoint=foundry_endpoint,
        credential=DefaultAzureCredential(),
    )
    ```
1. Find the comment Get an OpenAI client from the project, and add the following code to get an authenticated OpenAI client from your project:

    ```
    # Get an OpenAI client from the project
    openai_client = project_client.get_openai_client()
    ```
1. Find the comment Upload file and create vector store, and add the following code to upload the brochure files and create a vector store for file search:

    ```
    # Upload file and create vector store
    print("Uploading files and creating vector store...")
    vector_store = openai_client.vector_stores.create(
        name="travel-brochures"
    )
    file_streams = [open(f, "rb") for f in glob.glob("brochures/*.pdf")]
    if not file_streams:
        print("No PDF files found in the brochures folder!")
        return
    file_batch = openai_client.vector_stores.file_batches.upload_and_poll(
        vector_store_id=vector_store.id,
        files=file_streams
    )
    for f in file_streams:
        f.close()
    print(f"Vector store created with {file_batch.file_counts.completed} files.")
    ```
    > **Note**: This code uploads all PDF files from the brochures folder, creates a vector store, and waits for the files to be processed. The vector store will be used by the file search tool to find relevant information when answering questions.

1. Note that the code includes a loop to allow a user to input a prompt until they enter “quit”, and it tracks conversation state using previous_response_id. Find the comment Get a response and add the following code to send the user input to your model using the Responses API with the file search tool:

    ```
    # Get a response
    response = openai_client.responses.create(
        model=model_deployment,
        instructions="You are a travel assistant that provides information on travel services available from Margie's Travel. Only answer questions based on the provided travel brochure data.",
        input=input_text,
        previous_response_id=previous_response_id,
        tools=[{
            "type": "file_search",
            "vector_store_ids": [vector_store.id]
        }]
    )
    print(response.output_text)
    previous_response_id = response.id
    ```
    > **Note**: The Responses API uses previous_response_id to maintain conversation history automatically. The file_search tool is configured with the vector store containing the uploaded brochure data, so the model can search through the documents to find relevant information before responding.

1. Save the file (Ctrl+S).

## Task 5: Run the RAG application

1. In the VS Code terminal, sign into Azure:

    ```
    az login
    ```
1. Run the below command to log in to Azure, navigate to the device login URL `https://microsoft.com/devicelogin` in the browser and copy the authentication code.

   ``` 
   az login
   ```

   ![](media/aiwshared.png)
   
1. Enter the copied **Authentication code** **(1)** and click on **Next** **(2)**.

   ![](media/Link-code-login.png)
   
1. On the **Sign in to Microsoft Azure** tab, you will see a login screen. Enter the following email/username and then click on **Next**.

   * Email/Username: **<inject key="AzureAdUserEmail"></inject>**
   
    ![](media/corsspf-username.png)

1. Now enter the following password and click on **Sign in**.

   * Password: **<inject key="AzureAdUserPassword"></inject>**

     ![](media/GS4.png)

    > **Note**: In most scenarios, just using az login will be sufficient. However, if you have subscriptions in multiple tenants, you may need to specify the tenant by using the –tenant parameter. See Sign into Azure interactively using the Azure CLI for details.

1. After you have signed in successfully, in the same terminal run the application:
    ```
    python rag-app.py
    ```
1. When prompted, enter a question, such as **`Where should I go on vacation to see architecture?`** and review the response from your generative AI model.

1. Note that the response should include information grounded in the travel brochure data, with references to the source documents.

1. Try a follow-up question, for example **`Where can I stay there?`**

1. When you’re finished, enter quit to exit the program.

## Summary

In this lab, you have accomplished the following:
- Provisioned an Azure OpenAI resource.
- Deployed an Azure OpenAI model within the Microsoft Foundry.
- Used the chat playground to utilise the functionalities of prompts, parameters, and code generation.

### Conclusion
By completing this hands-on lab, you’ve gained practical experience with Azure OpenAI Service and Microsoft Foundry. You started by provisioning an Azure OpenAI resource and deploying a model that supports both conversational and instruction-based scenarios. You then explored the Chat playground, experimenting with prompts, parameters, and few-shot examples to shape model responses. Finally, you tested the model’s ability to generate code, highlighting its potential for developer productivity.

These exercises introduced not just the mechanics of deploying and interacting with models, but also how to configure them for different use cases, whether that’s conversational AI, educational Q\&A, or programming assistance. With this foundation, you’re now better equipped to integrate Azure OpenAI into real-world applications that demand scalability, flexibility, and secure access through the Azure ecosystem.

### You have successfully completed the Hands-on lab!
