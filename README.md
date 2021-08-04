# TooInstance (Previously known as EviThemis)
## Orbital 2021
- [1. Introduction](#introduction)
- [2. Tech Stack](#tech-stack)
- [3. Architecture](#architecture)
- [4. Setting up](#setting-up)
- [5. User Stories](#user-stories)
- [6. Components](#components)
  - [6.1. Main Components](#main-comps)
  - [6.2. Features](#features)
    - [6.2.1. Milestone 1](#mile1)
    - [6.2.2. Milestone 2](#mile2)
    - [6.2.3. Milestone 3](#mile3)
    - [6.2.4. Future Add-ons](#future)
- [7. How to Use TooInstance?](#howToUse)
- [8. Testing](#testing)
  - [8.1. Plan for Testing](#testing-plan)
  - [8.2. Actual Testing](#actual-testing)
    - [8.2.1 Basic Testing](#basic-testing)
    - [8.2.2 User Testing](#user-testing)
    - [8.2.3 Bug Testing](#bug-testing)


## <a name="introduction"></a>1. Introduction

**Proposed Level of Achievement :** Apollo 11

**Deployed Web Application (Link) :**
[https://2dua5jkdm2.execute-api.ap-southeast-1.amazonaws.com/dev](https://2dua5jkdm2.execute-api.ap-southeast-1.amazonaws.com/dev/)

*Note - If you have previously created TooInstances with us during Milestone 2, they may be gone as we have done a hard reset of the TooInstances*

![Poster](https://github.com/lkldev/TooInstance_Public/blob/main/images/2808.png)

**Motivation**

There are numerous open-sourced tools available online. However, to use these tools, we would need to download and create the necessary environment for the specific tool/code before we can run them. This becomes a hassle as different tools would require different environments to be set up and also proves to be inefficient for multiple users to replicate the necessary environment.

**Aim**

We hope to automate the creation of the necessary environment to run open-sourced tools and to allow the ease of sharing such tools through a serverless architecture

**Proposed Solution and Features**

A Web Application hosted on AWS services that automates the creation of the docker image of a specified tool and allows the user to share the instance of the tool with others.


## <a name="tech-stack"></a> 2. Tech Stack
1. Python Flask
2. AWS DynamoDB
3. AWS ECS (Elastic Container Service)
4. AWS Lambda
5. AWS CodeBuild
6. Amazon S3
7. AWS Lambda
8. Amazon API Gateway
9. Docker
    - Dockerized Python
    - Dockerized NodeJS


## <a name="architecture"></a> 3. Architecture
**Overview of our Architecture**

![Architecture](https://github.com/lkldev/TooInstance_Public/blob/main/images/TI_Architecture.jpg)

**Details of our Architecture**

Instead of the usual monolithic architecture, we decided to explore creating our project by using the serverless architecture. This would mean we have to take more time into designing our architecture, and the whole project would be more complex. However, having a serverless architecture will be more cost efficient, and our whole project can be turned into microservices, which makes each services independent. This improves our development work, and it makes each services more fault tolerant. If there is a fault in one service, it would not affect the abilities of other services. We would be able to better debug where the error is coming from and fix the issue quickly without affecting other services. Also, should there be an increase in volume traffic, we will be able to scale each services as required, without having to scale the whole project.

Using AWS as our cloud provider, we will be able to cut down a lot of cost by utilizing AWS services such as AWS lambda and AWS Elastic Container Service instead of the usual AWS EC2. Below is the breakdown our architecture:

- Our dashboard is hosted on AWS lambda, and users can reach the dashboard via the Amazon API Gateway. This setup would only incur cost when somebody visits our dashboard, and no cost would be incurred if there are no traffic to the dashboard. The job of the dashboard is to query and update the DynamoDB database, as well as allowing the user to download the output content from their tools.

- By using Amazon Cognito as our authentication service provider, we are able to seamlessly provide access control for each user. This allow us to focus on other areas of the project, instead of developing a secure user authentication system. However, we spent some time improving the wrapper for the service, as the available libraries are not able to handle our requirements (option to add Multi-factor authentication). Also, it provides secure access to our S3 bucket without making the bucket public to all.

- By using Amazon DynamoDB, we are able to take advantage of its DynamoDB streams feature which allow us to trigger the Lambda on new record. Thus, when new records of creation and/or running are added to the DynamoDB, it will trigger our Lambda service to execute it based on the new records.

- Our two main functions of our project is to create and run an instance. We separate each service into an Lambda service, Docker Creation and Run Tool Instance. As mention above, both of these lambda services are triggered by DynamoDB.
  1. The Docker Creation Lambda would process the record provided by DynamoDB and invoke our create-docker container hosted on AWS Fargate. 
      - The create-docker container would then pull the repository the user have provided and add the required Dockerfile and scripts into the repository. The updated repository is then pushed into our private GitHub account. The container then creates a new build project on CodeBuild pointing to our GitHub account. 
        - CodeBuild will then proceed to pull the repository and build the docker image. CodeBuild will update it status to Amazon DynamoDB. Once the docker image have been successfully built, it is stored into our ECR. CodeBuild will register the image with ECS and the image is ready to run.
  2. The Run Tool Instance would process the record provided by DynamoDB and invoke the correct instance hosted on AWS Fargate with the arguments supplied by the user. The result would then be uploaded to Amazon S3 which the user would be able to download.

With the design of our architecture, should one of our core function fails (Docker Creation stops working), it will not affect the user that are executing their instances that have been created. 


**Simple Flow of Application**

![Program Flow](https://github.com/lkldev/TooInstance_Public/blob/main/images/flow.jpg)


## <a name="setting-up"></a>4. Setting Up
*Note - Sample Set Up on Windows Machine*
#### Prerequisites
  - Any IDE (Atom, Sublime Text etc.)
  - Python3
    - *optional - you may set up a virtual environment to install the required packages*
  - pip (For installing Python Packages)
    - How to download? - https://pip.pypa.io/en/stable/installing/
  - awscli (To configure your AWS Access and Secret key)
    - How to download? - https://aws.amazon.com/cli/
    - How to Configure the keys? - https://docs.aws.amazon.com/cli/latest/userguide/cli-configure-quickstart.html
      - Note - You can leave the output format as default none (Don't type anything and click enter to leave it as default None)
#### Setting Up on Localhost
  - Below is an overview of our project directory structure

![overviewDir](https://github.com/lkldev/TooInstance_Public/blob/main/images/overviewDirStructure.jpg)

  1. Using Commandline (Please note - I have enabled virtual environment on my end), traverse to the dashboard folder which contains the requirements.txt file.

![requirements](https://github.com/lkldev/TooInstance_Public/blob/main/images/requirementsTxt.jpg)

  2. Afterwards, run "pip3 install -r requirements.txt" and let it install the necessary packages.
  3. Next, traverse back to the root folder (TooInstance/). Then, add the lines as seen below and save it in this root folder as "awsconfig.json".

![awsconfigjson](https://github.com/lkldev/TooInstance_Public/blob/main/images/awsconfigJson.jpg)

  4. At the same root folder, run "python genAWSConfig.py" and then, you are set to run the flask application.
  5. Traverse to the dashboard folder again, then run "python flask_web.py". The application should now be running at 127.0.0.1:5000.

![runningApp](https://github.com/lkldev/TooInstance_Public/blob/main/images/runningApp.jpg)


## <a name="user-stories"></a>5. User Stories

**High priority \> Must Have: \* \* \***

**Medium priority \> Should Have: \* \***

**Low priority \> Good to Have: \***

**A User is a general term for any one who uses our application, this includes a user that needs parcel delivered as well as deliverers.**

| **Priority** | **As a...** | **I want to be able to ...** | **So that I can ...** |
| --- | --- | --- | --- |
| \* \* \* | User | Create an account | Have my own personal account|
| \* \* \* | User | Login to my own account | To create/run tool instances |
| \* \* \* | User | Create a new TooInstance | Later run the instance with my desired input |
| \* \* \* | User | Enter my command inputs | Create a run of the TooInstance with my desired inputs |
| \* \* \* | User | Run my TooInstance | Test/Run my Tool without any physical environment set up |
| \* \* \* | User | See all the TooInstances I have created | See my previous outputs and its corresponding input |
| \* \* \* | User | See all public TooInstances that the community has created | I can test out interesting tools without having to create an instance on my account |
| \* \* \* | User | Private and share the TooInstance only with someone I know | Let only my friend/collaborator/colleague to try out and not the general public |
| \* \* \* | User | See the TooInstances that have been shared with me | Run/test out these Private TooInstances |
| \* \* | User | See my past Run History | Easily see my most recent run without going through a long process of traversing to find them |
| \* \* | User | Reset my password | Change my password if I forgot |
| \* \* | User | Search for a specific tool instance within the public listing of instances created | Use the instance to test out with my desired inputs |


## <a name="components"></a>6. Components
### <a name="main-comps"></a> 6.1 Main components
- Dashboard Page
- Create TooInstance Page
- Update TooInstance Page
- Create New Run Page
  - Backend Processing of inputs, to accept eg. ("-A -O output.txt")
- Output View Page
- "How to Use" Page
- "Shared With Me" Page
- Public TooInstance Listing Page

Sample Screenshots of our application components.

Dashboard Page:
![Empty Dashboard Page](https://github.com/lkldev/TooInstance_Public/blob/main/images/emptyToolInstance.JPG)
![Dashboard Page](https://github.com/lkldev/TooInstance_Public/blob/main/images/toolInstanceList.JPG)

Create TooInstance Page:
![Create ToolInstance](https://github.com/lkldev/TooInstance_Public/blob/main/images/createNewTool.JPG)

Update TooInstance Page:
![Update ToolInstance](https://github.com/lkldev/TooInstance_Public/blob/main/images/updatedTI.JPG)

Create New Run Page:
![New Run](https://github.com/lkldev/TooInstance_Public/blob/main/images/newRun.JPG)

Output View Page:
![OutputList](https://github.com/lkldev/TooInstance_Public/blob/main/images/runOutputList.JPG)

Public Listing Page:
![PublicListing](https://github.com/lkldev/TooInstance_Public/blob/main/images/publicListingWorking.JPG)

Run History Page:
![RunHistory](https://github.com/lkldev/TooInstance_Public/blob/main/images/runHistory.JPG)

Shared With Me Page:
![SharedWithMe](https://github.com/lkldev/TooInstance_Public/blob/main/images/sharedWithDev.JPG)

How To Use Page:
![HowToUse](https://github.com/lkldev/TooInstance_Public/blob/main/images/howToUse.JPG)


### <a name="features"></a> 6.2 Features
#### <a name="mile1"></a> 6.2.1 Features implemented in Milestone 1
1. Login/Register
    - Register User
    - Login as User
    - Logout of User Account

2. Sample logged on Dashboard page with list of test data

3. UI and UX changes for the web pages


#### <a name="mile2"></a> 6.2.2 Features Implemented in Milestone 2
1. Create TooInstance (New Instance of Tool)
    - Upload to DynamoDB Table, the following Parameters:
      - Instance Name
      - Git Link
      - Privacy (Public vs Private)
      - Access List (Optional - Unless Private) \*May make changes to this\*
    - Trigger Lambda to create Docker

2. Update TooInstance
    - Change information of the respective TooInstance
    - Update these changed information to DynamoDB

3. Create New Run for TooInstance
    - Create a New Run of the TooInstance (that has been created before)
      - Given Input Parameters and Arguments in the form of json format/file (eg. {"0": "-O", "1": "output.txt", "2": "-T", "3": "js,php"})
      - You can check out more examples at our "How To create New Run" Page
    - Trigger running of Tool in Docker with the fed input arguments

4. List the Run Outputs corresponding to the specific TooInstance
    - Displaying Inputs used for that run, status and Output in the form of a file download

5. Automate Docker creation
    - Lambda triggered will call Fargate docker-creation perform the following:
      1. Pull repository from the git link user have provided.
      2. Add dockerfile and run scripts which are used to create and run the program.
      3. Push repository into TooInstance Github.
      4. Create Elastic Container Registry.
      5. Create Code Build project to create the image of the tool the user wanted.
         - Code Build will create a fargate task using the image.


#### <a name="mile3"></a> 6.2.3 Features Implemented in Milestone 3
1. Public Listing
    - Display all Non-Private TooInstances
    - Search Filtering by TooInstance Names

2. Shared with Me Functionality
    - Displays All Instances that have been Private but whitelisted/allowed to you
    - Backend Processing Changes for Create/Update Page
      - Enable Privacy and Access List

3. Added additional backend Processing for New Run
    - Allow users to give simple command line args format instead of just json format (Tough for less technical users)
      - We now also accept eg. -O output.txt -T js,php
      - Afterwards, processed in backend to be converted to json format

4. How To ~Run~ Use Page
    - Least of Priority in terms of functionality
    - Mainly to boost/help with the user usage on the TooInstance Run

5. Run History
    - Allows users to view past runs from a history list

6. Nodejs RunTime Implementation
    - To allow Running of Nodejs TooInstances

#### <a name="future"></a> 6.2.3 Future Add-Ons
1. MFA (Multi-Factor Authentication)
2. More Runtime Implementations
3. Github Integration
4. Capture File Output (now only supports capturing Standard Outputs and returning as a file)
5. Deleting of TooInstances
6. Versioning of TooInstances

## <a name="howToUse"></a>7. How To Use?
<h3 id="howToCreate">How to Create a TooInstance</h5>
To start off, simply click on the "+ Add Tool" button on the bottom right of your dashboard as shown in the screenshot below.<br><br>

![createToolScreenshot](https://github.com/lkldev/TooInstance_Public/blob/main/images/createToolScreenshot.JPG) <br><br>

*Alternatively, you may also use the right navbar to create the tool. You may ignore this step if you are already at the Create Tool Page.<br><br>

![createToolLeftNavBar](https://github.com/lkldev/TooInstance_Public/blob/main/images/createToolLeftNavBar.JPG) <br><br>

Now, you should be at the Create Tool Page. Simply fill in the information regarding the tool. First thing would be the Tool(or TI) Name you would like to give it, then gitlink of the tool (eg. https://github.com/sherlock-project/sherlock), Output Type (File Output option to be Implemented), the Runtime (Eg. What language is the tool running?),
Entry Point - How the tool is usually ran (eg. python script.py, then script.py is your entry point) and then finally, the privacy settings. <br><br>

![createToolPage](https://github.com/lkldev/TooInstance_Public/blob/main/images/createToolPage.JPG)<br><br>

If you have decided to make the TooInstance Private, then an Access List should appear for you to provide the emails/UIDs (Only if you know their UID) of those you would like to give access to (will appear under "Shared with Me" on their end). If you do not provide any other user's email/UID, this means it will be available to be ran by you only.<br><br>

![accessList](https://github.com/lkldev/TooInstance_Public/blob/main/images/accessList.JPG)<br><br>

Once you are done filling in the relevant informations, simply click submit and it will be sent to our backend to be created. You will then be directed back to your dashboard and you would see its status "pending". The creation time would take about an estimated 5 mins.<br><br>
<u>Before Created Successfully</u><br><br>

![creationNotReady](https://github.com/lkldev/TooInstance_Public/blob/main/images/creationNotReady.JPG)<br><br>

<u>After Created Successfully</u><br><br>

![creationSuccess](https://github.com/lkldev/TooInstance_Public/blob/main/images/creationSuccess.JPG)

<h3 id="howToCreate">How to Run the TooInstance</h5>
To start a new Run of the TooInstance, simply click on the "New Run" button corresponding to the desired TooInstance. For the example below, I will be creating a new Run for the TooInstance called "Sherlock 2.0".<br><br>

![newRunButton](https://github.com/lkldev/TooInstance_Public/blob/main/images/newRunButton.JPG) <br><br>

Once you have clicked on the "New Run" button, you should be directed to the "Create New Run" Page. Here, you would have to provide us with the command arguments to run the TooInstance. There would be 2 ways of providing this Input Details field, which I would provide examples for below. I will be using the Test case of "python sherlock --timeout 1 google".<br><br>
<u> Example 1 - By providing simple command line argument format</u> <br><br>
  Since usual way we run the tool on the command line (console) is "python sherlock --timeout 1 google". Then here, we can simply input "--timeout 1 google" into the <b>Input Details</b> field. An example is shown below.

![runExample1](https://github.com/lkldev/TooInstance_Public/blob/main/images/runExample1.JPG) <br><br>

<u> Example 2 - By providing inputs in JSON format</u><br><br>
For the JSON format, you may format them nicely as shown below.<br><br>

![runExample2](https://github.com/lkldev/TooInstance_Public/blob/main/images/runExample2Neat.JPG) <br><br>

Or, you can simply clump them all together as seen below. Either of the 2 JSON formats are accepted.<br><br>

![runExample2Clump](https://github.com/lkldev/TooInstance_Public/blob/main/images/runExample2Clump.JPG) <br><br>

Personally, the command line format would be more easy to use and less time consuming. Anyways, after the Run has been created, you would be directed to the "Run Outputs" Page and you would have to wait a while (varies between different Tools - eg. Sherlock without the Timeout Argument provided runs on even after 10-20mins). However, once the Run has completed, you should be able to see an "Output" button which you can download and view the output from the Run.<br><br>

![runFinished](https://github.com/lkldev/TooInstance_Public/blob/main/images/runFinished.JPG) <br><br>

## <a name="testing"></a> 8. Testing
### <a name="testing-plan"></a> 8.1 Plan for Testing
#### <u>Plan</u>
1. Basic Testing with multiple Accounts
    - Testing with most likely 2 Accounts
    - Test Original Program Flow
2. User Testing
    - Invite friends/family/adviser to try out
    - Gain Feedback/Improvements
3. Bug Testing
    - Try out invalid inputs to trigger/find bugs


### <a name="actual-testing"></a> 8.2 Actual Testing
#### <a name="basic-testing"></a> 8.2.1 Basic Testing
We tested using 2 accounts - themis@example.com (Themis) and lkldev@outlook.com (Dev)

First, for themis@example.com, we will be creating TooInstances and sharing it with lkldev@outlook.com.

Themis first logs in and is presented with a dashboard containing all Themis's TooInstances(TI).

![Dashboard](https://github.com/lkldev/TooInstance_Public/blob/main/images/dashboard.JPG)

Themis then creates a new TI by clicking on the bottom right "+ Add Tool" button. It then redirects to the Create Tool Page.

![CreateToolPage](https://github.com/lkldev/TooInstance_Public/blob/main/images/createTool.JPG)

Themis then fills in the necessary Tool Information for this TI. Most importantly, Themis made sure to private this TI and shared it only with Dev through the access List before clicking on the "Submit" button.

![filledUpCreatePage](https://github.com/lkldev/TooInstance_Public/blob/main/images/filledInCreate.JPG)

Once it has been submitted, the TI will be created in the backend using AWS technologies. Themis sees the status of the TI updating to provide Themis with more information on its creation. Finally, it returns a "Success" status showing that the TI has been created successfully.

![themisWaiting](https://github.com/lkldev/TooInstance_Public/blob/main/images/themisWaits.JPG)

![themisStatus](https://github.com/lkldev/TooInstance_Public/blob/main/images/themisSeesStatus.JPG)

![themisSuccess](https://github.com/lkldev/TooInstance_Public/blob/main/images/themisSuccess.JPG)

Themis can then start a new run of that TI by clicking on the corresponding "New Run" button. It will be redirected to a New Run Page then request for Input Details from Themis. Themis, being a less technical user (not familiar with JSON formats) and a fan of Iron Man provides TI with the inputs as "--timeout 1 robertdowneyjr" before clicking on the "Submit". Themis can then see the JSON-parsed Inputs as well as the status which updates to provide information on the phase of the TI run. Once, it is finished, the status updates again to become "Success" and a "OUTPUT" button appears which allows Themis to download to view the output of the run.

![themisIronMan](https://github.com/lkldev/TooInstance_Public/blob/main/images/themisIronManRun.JPG)

![themisRunningInstanceUpdate](https://github.com/lkldev/TooInstance_Public/blob/main/images/themisRunningInstanceUpdate.JPG)

![themisRunSuccess](https://github.com/lkldev/TooInstance_Public/blob/main/images/themisRunSuccess.JPG)

![themisOutput](https://github.com/lkldev/TooInstance_Public/blob/main/images/output.JPG)

On Dev's end, Dev goes to the "Shared with me" Page through the left Navbar.

![leftNavBar](https://github.com/lkldev/TooInstance_Public/blob/main/images/sharedNavBar.JPG)

Dev is redirected to the "Shared with Me" Page containing Private TIs that has been shared with only Dev. Dev can then use the TI to start a New Run of it.

![sharedWithDev](https://github.com/lkldev/TooInstance_Public/blob/main/images/sharedWithDev.JPG)

Dev being sus of our application, goes to the Public Listing Page to make sure that our application did not accidentally make the TI as public. However, it was indeed private as Dev could not see it in the Public Listing and from there, Dev was relieved. And they lived happily ever after. \<FIN\>

![publicListingWorking](https://github.com/lkldev/TooInstance_Public/blob/main/images/publicListingWorking.JPG)

#### <a name="user-testing"></a> 8.2.2 User Testing
1. TLDR of User Feedbacks (From friends, family, orbital teams and adviser)
    - Vague Notification of the creation/run progress
    - Run Inputs was hard to use - Some who were less exposed to JSON needed more time to use it
    - Simple UI
    - Feel that the File Upload option for New Run was unnecessary (Since the file is basically the JSON input)
    - Difficult to understand how to use TooInstance
    - Hassle to view the outputs (eg. Browsing other stuff and wanting traverse back to the specific TI through dashboard, then clicking to finally be able to view the outputs)
2. Solutions/Changes
    - Updated our frontend to display more informative status updates (eg. Pushing image to Git...)
    - Made changes to allow for simple CLI inputs as the Run Inputs (eg. --timeout 1 dlwlrma)
      - Our backend will then parse it into Json Format for the user
      - *However, users may also use the JSON format for the Inputs, we accept either)
    - Removed the File Upload option for New Run
    - Added a How To Use Page, to help act as a 'guide' for users
    - Added a Run History Page, this allows users to view their previous runs and outputs within going through the long route of pages.

*Current Feedbacks(summarised for now) from Teams, Adviser and Friends (Studying IT related) - Mainly the issues were focusing on the vague progression of the creation and run, as well as the run inputs(hard to use - only json allowed)*
#### <a name="bug-testing"></a> 8.2.3 Bug Testing
Bug #1 - Able to See/Run (Still Creating/Error) TooInstance in Public List
  - Reason was due to lack of checks on whether the status has been "Successful"
  - Fix - *Changes to Backend to not return any TIs that are Successful yet*

Bug #2 - Some Run Inputs not parsed into Json format (in backend)
  - Sample Input - "http://www.google.com" (Includes the quotations)
  - Credits to our Adviser Neil
  - Reason for bug was due to the check whether the input is in JSON format, which went through as True, thus causing the input to be not parsed into JSON format.
  - Fix - *Another layer of checking to verify whether it is in JSON format.*

Bug #3 - Server Error when Creating Tool that is Public
  - Server Error - Regarding the variable being referenced before assignment (variable related to Access List value being used to be passed to function)
  - Reason of the Bug/Error was due to mistake of not changing the variable name correctly. (Although, there was no issues on Local Test Environment)
  - Quick Fix - *Change the variable name/Assign it a decoy value*

Bug #4 - Server Error when Creating Tool that is Private with emails/UIDs provided.
  - Server Error - Policies/Permissions regarding Cognito (AWS) operations
  - Reason was due to lack of permissions/policies to execute the Cognito-related commands (For translating the emails -> to UIDs)
  - Fix - *Give the necessary Cognito Policies to the "instance" of our Application running*

Bug #5 - Neatly formatted version of JSON-provided Run Inputs
  -  Reason was when the Input Parameters are nicely formatted in JSON format, resulting JSON-ed Inputs break/not as expected due to the \r\n being included (other characters incl. \t, \u2003)
  - Fix - *Strip out the carriage returns (\r\n), tabs (\t, \u2003 - from the sample JSON format at the left side on the page)*

Bug #6 - Updating of run script
  - Reason was due to our scripts originally having their code built together into the docker image, which would mean that if we change our script, we will have to rebuild the whole docker image.
  - To reduce the time it takes to build the docker image, we instead allow our scripts to be pulled from s3 instead.
  - Fix - *Everytime we run, the docker will pull the latest scripts required.*

Bug #7 - Missing packages on Python
  - Some GitHub repository found online has no requirements.txt, hence the tool will not run
  - Fix - *found a pip package pipreqs  which helps to generate all the required packages the project.*

Bug #8 - AWS IAM extensive permission
  - To ensure the security of our environment, each docker is ran with minimal permission. However, this would occasionally resulted in the program crashing as it does not have the required permissions
  -  Fix - *Ensure that all the permission for basic workflow is granted by performing basic testing*

Bug #9 - Docker hub pull limit
  - Docker Hub has a pull limit for public users. Since our project requires the python and nodejs image from docker hub, we faced a pull limit exceeded during one of our testing.
  - Fix - *Pull docker hub images into our private AWS ECR. Our scripts would then pull from our own repository instead.*

Bug #10 - AWS ECS creation
  - Using AWS web console, the ECS created as intended, however, when we programmatically create the ECS Task, we were faced with numerous errors. We had to break down what the web console did and add the missing steps into our script
  - Fix - *Add missing AWS components created by the web console abstracted from the user into our script to ensure that the ECS is created smoothly.*
