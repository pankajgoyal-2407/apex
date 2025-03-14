# Vector Search using Search Configurations

## Introduction

In this lab, you will learn how to load ONNX models into Oracle Database and create a Vector Provider to generate vector embeddings. By leveraging Oracle Vector Search, you will configure a semantic similarity search, allowing users to find relevant results regardless of how they phrase their queries. Finally, you will build a search page that utilizes these configurations to enhance search accuracy and user experience.


>*Note: This lab requires you are using Oracle Database 23ai.*

Estimated Time: 10 minutes

### Objectives

- Load ONNX Models
- Create a Vector Provider
- Generate Vector Embeddings
- Create Search Configuration based on Oracle Vector Search
- Create an Oracle Vector Search Page

## Downloads: Missed Previous Labs? Download and Install the Application

If you're stuck or missed out on completing the previous labs, don't worry! You can download and install the application as follows:

1. Click [here](https://c4u04.objectstorage.us-ashburn-1.oci.customer-oci.com/p/EcTjWk2IuZPZeNnD_fYMcgUhdNDIDA6rt9gaFj_WZMiL7VvxPBNMY60837hu5hga/n/c4u04/b/livelabsfiles/o/apex%2Fobs-lab17.zip) to download the completed application zip file.

2. Import the **downloaded zip** file into your workspace. To import the app, go to **App Builder > Import**.

3. Follow the steps in the Install Application wizard to install the app along with the Supporting Objects.

4. Update the hashed password for user01:

    - Go to **SQL Workshop > Object Browser > Tables > OBS\_USERS > Data**.

    - Click the **edit icon** for user01 and update the password to **secretPassword**.

5. Update the Web Credential and Generative AI:

    - To create an OCI API Key, Refer to [Task 1: Generate API Keys using OCI Console](?lab=8-build-an-ai-assistant-chat-widget#Task1:GenerateAPIKeysusingOCIConsole) of [Lab 8: Build an AI-Assistant Chat Widget](?lab=8-build-an-ai-assistant-chat-widget)

    - Edit the apex\_ai\_cred details to match with your OCI API Key,  Refer to [Task 2: Create Web Credentials](?lab=8-build-an-ai-assistant-chat-widget#Task2:CreateWebCredentials) of [Lab 8: Build an AI-Assistant Chat Widget](?lab=8-build-an-ai-assistant-chat-widget)

    - To edit the generative ai, Refer to [Task 3: Configure Generative AI Service](?lab=8-build-an-ai-assistant-chat-widget#Task3:ConfigureGenerativeAIService) of [Lab 8: Build an AI-Assistant Chat Widget](?lab=8-build-an-ai-assistant-chat-widget)

6. Update the Picture URL:

    - Go to **SQL Workshop > RESTful Services > Modules > User Images > profile_pic/:id > GET** and copy Full URL.

    - Go to **App Builder > Online Bookstore > Shared Components > Application Computations > PICTURE\_URL > Computation >**, and replace **<****picture\_url****>** with the copied FULL URL up to ..profile_pic/.

7. Update the Google Auth Credential:

    - To create an Google API Key, Refer to [Task 5: Login with Google](?lab=12-onboarding#Task5:LoginwithGoogle) of [Lab 12: Improve User Onboarding](?lab=12-onboarding), following steps 1 to 10.

    - Edit the Google Authentication details to match with your Google API Key, Refer to [Task 5: Login with Google](?lab=12-onboarding#Task5:LoginwithGoogle) of [Lab 12: Improve User Onboarding](?lab=12-onboarding), following steps 12 to 16.

8. Generate Push Notification Credentials and enable Push Notifications on the device:

    - To generate Push Notification Credentials, go to **App Builder > Online Bookstore > Shared Components > Progressive Web App > Push Notifications**, click **+ Generate Credentials** for Credentials.

    - To enable push notifications, run the application and click on your username in the top-right corner. Then, select **Settings**. By default, push notifications are disabled. Click Off and check the box labeled **Enable push notifications on this device**.

9. Setup Compartment ID to run the Automation for AI Vision Service:

    - Go to **App Builder > Online Bookstore > Shared Components > Automations > Index Book Cover With OCI Vision > Actions**, click the **edit icon** for **Invoke REST Data Source** and replace **#COMPARTMENT\_ID#** with your **COMPARTMENT\_ID** at two places in the query.

10. Update the Razorpay Auth Credential:

    - To create an Razorpay API Key, Refer to [Task 1: Create Web Credential](?lab=15-razorpay-payement-integration#Task1:CreateWebCredential) of [Lab 15: Razorpay Payment Gateway Integration](?lab=15-razorpay-payement-integration), following steps 1 to 4.

    - Edit the Razorpay API details to match with your Razorpay API Key, Refer to [Task 1: Create Web Credential](?lab=15-razorpay-payement-integration#Task1:CreateWebCredential) of [Lab 15: Razorpay Payment Gateway Integration](?lab=15-razorpay-payement-integration), following steps 5 to 9.

11. Update ICON_URL for the application icon in the emails:

    - Go to **App Builder > Online Bookstore > Page 100001 - Email Verification for User SignUp > Processing > Send Verification Email > Send Email Process**, under Settings, select **Placeholder Values** and update **ICON_URL**.

        - In **ICON_URL**, replace **online\_bookstore\_app** with your **workspace\_name**, and update the domain apex.oracle.com if your environment differs.

    - Go to **App Builder > Online Bookstore > Page 100002 - Email Verification for Forgot Password > Processing > Send Verification Email > Send Email Process**, under Settings, select **Placeholder Values** and update **ICON_URL**.

        - In **ICON_URL**, replace **online\_bookstore\_app** with your **workspace\_name**, and update the domain apex.oracle.com if your environment differs.

12. All set. Now, run the application and see it in action!.

## Task 1: Load the ONNX Model

Before uploading the ONNX model to your database schema, you must grant the schema the privilege to create mining models while logged in as SYS/ADMIN.

Login as SYS/Admin User and execute the below command.

>**Command**: GRANT create mining model TO **YourSchemaName**;

![Page Designer](images/18-1-0.png ' ')

1. From your APEX workspace homepage, click the Down Arrow next to **SQL Workshop** and select **SQL Commands**.

    ![Page Designer](images/18-1-1.png ' ')

2. In the SQL Commands Page, copy and paste the below code and click **Run**.

    ```
    <copy>
        DECLARE
        L_PAR_URL       VARCHAR2(1000);
        L_RESPONSE_BLOB BLOB;
        BEGIN
            L_PAR_URL := 'https://adwc4pm.objectstorage.us-ashburn-1.oci.customer-oci.com/p/eLddQappgBJ7jNi6Guz9m9LOtYe2u8LWY19GfgU8flFK4N9YgP4kTlrE9Px3pE12/n/adwc4pm/b/OML-Resources/o/all_MiniLM_L12_v2.onnx'
            ;
            L_RESPONSE_BLOB := APEX_WEB_SERVICE.MAKE_REST_REQUEST_B(
                P_URL         => L_PAR_URL,
                P_HTTP_METHOD => 'GET'
            );
            DBMS_VECTOR.LOAD_ONNX_MODEL(
                MODEL_NAME => 'DOC_MODEL',
                MODEL_DATA => L_RESPONSE_BLOB,
                METADATA   =>
                        JSON(
                            ' {
                        "function" : "embedding",
                        "embeddingOutput" : "embedding" ,
                        "input":{"input": ["DATA"]}
                    }'
                        )
            );

        END;
    </copy>
    ```

    ![Page Designer](images/18-1-2.png ' ')

## Task 2: Create a Vector Provider

In this task, you will create a Vector Provider that will be used later to set up a Search Configuration.

1. From your SQL Commands page, click the Down Arrow next to **App Builder** and select **Workspace Utilities**. Then click **All Workspace Utilities**.

    ![Page Designer](images/18-2-1.png ' ')

2. In the Workspace Utilities page, select **Vector Providers**.

    ![Page Designer](images/18-2-2.png ' ')

3. In the Vector Providers page, click **Create**.

    ![Page Designer](images/18-2-3.png ' ')

4. In the Vector Provider Configuration page, enter the following:

    - Under Identification:

        - Provider Type : **Database ONNX Model**

        - Name: **DB ONNX Model**

        - Static ID: **db\_onnx\_model**

    - Under Local Embedding:

        - For ONNX Model Owner: **Select your Parsing Schema**

        - ONNX Model Name: **DOC_MODEL**

    Click **Create**.

    ![Page Designer](images/18-2-4.png ' ')

5. Your Vector Provider is now created.

    ![Page Designer](images/18-2-5.png ' ')

## Task 3: Create Vectors on Book Description

In this task, you will enhance the **OBS\_BOOKS** table by adding a new column, **DESCRIPTION\_VECTOR**, to store description vector embeddings. Starting with APEX 24.2, the new PL/SQL API **APEX\_AI.GET\_VECTOR\_EMBEDDINGS** streamlines this process. You will utilize the **APEX\_AI.GET\_VECTOR\_EMBEDDINGS** API to insert vector embeddings.

1. From your Vector Provider page, click the Down Arrow next to **SQL Workshop** and select **SQL Commands**.

    !["Page Designer"](images/18-3-1.png "")

2. Create a new column in the **OBS\_BOOKS** table to store description vector embeddings. Copy and paste the SQL code below, then click **Run**.

     ```
    <copy>
    ALTER TABLE OBS_BOOKS ADD DESCRIPTION_VECTOR VECTOR;
    </copy>
     ```

    !["Page Designer"](images/18-3-2.png "")

3. Copy and paste the SQL code below, then click **Run**

    ```
    <copy>
    UPDATE OBS_BOOKS
    SET DESCRIPTION_VECTOR = APEX_AI.GET_VECTOR_EMBEDDINGS(
        P_VALUE             => DESCRIPTION,
        P_SERVICE_STATIC_ID => 'db_onnx_model'
    );
    </copy>
     ```

    !["Page Designer"](images/18-3-3.png "")

4. Navigate to **Object Browser** via **SQL Workshop**

    !["Page Designer"](images/18-3-4.png "")

5. In the object tree, right-click **Triggers** and select **Create Trigger**

    !["Page Designer"](images/18-3-5.png "")

6. Enter/Select the following and click **Create Trigger**

    - Table: **OBS_BOOKS**

    - Firing Point: **Before**

    !["Page Designer"](images/18-3-6.png "")

7. Replace the existing code with the below code:

    ```
    <copy>
    CREATE OR REPLACE TRIGGER "OBS_BOOKS_T_1"
    BEFORE INSERT OR UPDATE ON "OBS_BOOKS"
    FOR EACH ROW
    BEGIN

        IF :NEW.DESCRIPTION != :OLD.DESCRIPTION THEN
            :NEW.DESCRIPTION_VECTOR := APEX_AI.GET_VECTOR_EMBEDDINGS(
                    P_VALUE             => :NEW.DESCRIPTION,
                    P_SERVICE_STATIC_ID => 'db_onnx_model'
            );
        END IF;
    END;
    /
    </copy>
     ```

8. Click **Save and Compile**

    !["Page Designer"](images/18-3-8.png "")

## Task 4: Create a Search Configuration

In this task, you will set up a Search Configuration based on Oracle Vector Search.

1. From the Navigation bar in your workspace, click **App Builder**.

    !["Page Designer"](images/18-4-1.png "")

2. In the **App Builder** page, select your Application and then click **Shared Components**.

    !["Page Designer"](images/18-4-2.1.png "")

    !["Page Designer"](images/18-4-2.2.png "")

3. From Shared Components, under **Navigation and Search**, click **Search Configurations**.

    !["Page Designer"](images/18-4-3.png "")

4. In the Search Configurations page, click **Create**.

    !["Page Designer"](images/18-4-4.png "")

5. In the Create Search Configuration Detail Wizard, enter the following and click **Next**.

    - Name : **Online Bookstore Search - Vector**

    - Search Type: **Oracle Vector Search**

    !["Page Designer"](images/18-4-5.png "")

6. In the Create Search Configuration **Source** Wizard, enter the following and click **Next**.

    - Vector Provider : **DB ONNX Model**

    - Source Type: **Table**

    - Table/View Owner: **Select your Parsing Schema**

    - Table/View Name: **OBS\_BOOKS**

    !["Page Designer"](images/18-4-6.png "")

7. In the Create Search Configuration **Column Mapping** Wizard, enter the following and click **Create Search Configuration**.

    - Primary Key Column: **BOOK_ID (Number)**

    - Vector Column: **DESCRIPTION_VECTOR (Vector)**

    - Title Column: **TITLE (Varchar2)**

    - Description Column: **DESCRIPTION (Varchar2)**

    !["Page Designer"](images/18-4-7.png "")

## Task 5: Create an Oracle Vector Search Page

In this task, you'll create a Vector Search Page to enable advanced search functionality for books. You'll configure the search page, update navigation settings, and test the feature to retrieve relevant book results.

1. Navigate to the application home page and click  **Create Page**.

    !["Page Designer"](images/18-5-1.png "")

2. Select **Search Page**.

    !["Page Designer"](images/18-5-2.png "")

3. Under Create Search Page wizard, enter the following:

    - Under Page Definition:

        - Page Number: **5**

        - Name: **Books Vector Search**

    - Search Configurations > Online Bookstore Search - Vector: **Toggle On**

    - Under Navigation:

        - Use Breadcrumb: **Toggle Off**

        - Use Navigation: **Toggle Off**

    Click **Create Page**.

    !["Page Designer"](images/18-5-3.png "")

4. Navigate to **Shared Components**.

    !["Page Designer"](images/18-5-4.png "")

5. Under **Navigation and Search**, select **Navigation Bar List**.

    !["Page Designer"](images/18-5-5.png "")

6. Click **Navigation Bar**.

    !["Page Designer"](images/18-5-6.png "")

7. Click **Create List Entry**.

    !["Page Designer"](images/18-5-7.png "")

8. Enter/select the following and click **Create List Entry**.

    - Under Entry:

        - Sequence: **2**

        - Image/Class: **fa-search-plus**

        - List Entry Label: **Advanced Search**

    - Under Target:

        - Page: **5**

        - Clear Cache: **5**

    !["Page Designer"](images/18-5-8.1.png "")

     - Under Conditions:

        - Condition Type: **Current Page Is NOT in Expression 1 (comma delimited list of pages)**

        - Expression 1: **5**

    !["Page Designer"](images/18-5-8.2.png "")

9. Run the application and click **Advanced Search** entry in the navigation bar.

    ![Click](images/18-5-9.png " ")

10. Search for any keyword, press Enter, and observe the results of books related to that keyword.

    ![Click](images/18-5-10.png " ")

## Summary

In this lab, you loaded ONNX models into Oracle Database, created a Vector Provider, and generated vector embeddings. You then configured Oracle Vector Search for enhanced search capabilities and built a search page to leverage these embeddings for efficient and accurate search results.

## Acknowledgements

- **Author**: Pankaj Goyal, Member Technical Staff
- **Last Updated By/Date**: Pankaj Goyal, Member Technical Staff, March 2025
