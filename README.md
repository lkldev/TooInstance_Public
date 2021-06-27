# TooInstance (Previously known as EviThemis)
## Orbital 2021
NOTE: **TBD - To Be Done**
- [1. Introduction](#introduction)
- [2. User Stories](#user-stories)
- [3. Components](#components)
  - [3.1. Main Components](#main-comps)
  - [3.2. Features](#features)
    - [3.2.1. Milestone 1](#mile1)
    - [3.2.2. Milestone 2](#mile2)
- [4. Architecture and Flow](#architectureFlow)


## <a name="introduction"></a>1. Introduction

**Proposed Level of Achievement :** Apollo 11

**Deployed Web Application (Link) :**
[https://2dua5jkdm2.execute-api.ap-southeast-1.amazonaws.com/dev/](https://2dua5jkdm2.execute-api.ap-southeast-1.amazonaws.com/dev/)

![Poster](https://github.com/lkldev/TooInstance_Public/blob/main/images/posterV3.png)

**Motivation**

There are numerous open-sourced tools available online. However, to use these tools, we would need to download and create the necessary environment for the specific tool/code before we can run them. This becomes a hassle as different tools would require different environments to be set up and also proves to be inefficient for multiple users to replicate the necessary environment.

**Aim**

We hope to automate the creation of the necessary environment to run open-sourced tools and to allow the ease of sharing such tools through a serverless architecture

**Proposed Solution and Features**

A Web Application hosted on AWS services that automates the creation of the docker image of a specified tool and allows the user to share the instance of the tool with others.

## <a name="user-stories"></a>2. User Stories

**High priority \> Must Have: \* \* \***

**Medium priority \> Should Have: \* \***

**Low priority \> Good to Have: \***

**A User is a general term for any one who uses our application, this includes a user that needs parcel delivered as well as deliverers.**

| **Priority** | **As a...** | **I want to be able to ...** | **So that I can ...** |
| --- | --- | --- | --- |
| \* \* \* | User | Create an account | Have my own personal account |
| \* \* \* | User | Login to my own account | To create/run tool instances |
| \* \* \* | User | Create a new Tool Instance | Run the instance with my desired input |
| \* \* \* | User | See all the tools instances I have created | See my previous outputs and its corresponding input |
| \* \* \* | User | See all public tools instances that the community has created | I can test those tools without having to create an instance on my end |
| \* \* \* | User | Share the tool with other users | Let my friend/collaborator/colleague to try out without having to replicate the required environment |
| \* \* \* | User | See the logs of the tools I have ran | See what errors have occurred |
| \* \* | User | Reset my password | Change my password if I forgot |
| \* \* | User | Search for a specific tool instance within the public listing of instances created | Use the instance to test out with my desired inputs |
| \* | User | Enable 2FA | Prevent my account from being taken over |

## <a name="components"></a>3. Components
### 3.1 Main components
- Dashboard Page
- Create TooInstance Page
- Update TooInstance Page
- Create New Run Page
- Output View Page
- How to Run? Page (To be done in Mile3)
- Public TooInstance Listing Page (To be done in Mile 3)

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



### <a name="features"></a> 3.2 Features
#### <a name="mile1"></a> 3.2.1 Features implemented in Milestone 1
1. Login/Register
    - Register User
    - Login as User
    - Logout of User Account
2. Sample logged on Dashboard page with list of test data
3. UI and UX changes for the web pages

#### <a name="mile2"></a> 3.2.2 Features Implemented in Milestone 2
1. Create TooInstance (New Instance of Tool)
    - Upload to DynamoDB Table, the following Parameters:
      - Instance Name
      - Git Link
      - Privacy (Public vs Private)
      - Access List (Optional - Unless Private) \*May make changes to this\*
    - Trigger Lambda creation of Dockerfile
2. Update TooInstance
    - Change information of the respective TooInstance
    - Update these changed information to DynamoDB
3. Create New Run for TooInstance
    - Create a New Run of the TooInstance (that has been created before)
      - Given Input Parameters and Arguments in the form of json format/file (eg. {"-O", "output.txt", "-U": { "0": {"S": "url"}, "1": {"S" : "quality"}})
      - You can check out more examples at our "How To create New Run" Page (To be implemented in Milestone 3)
    - Trigger running of Tool in Docker with the fed input arguments
4. List the Run Outputs corresponding to the specific TooInstance
    - Displaying Inputs used for that run, status and Output in the form of a file download


## <a name="architectureFlow"></a>4. Architecture and Flow

**Overview of Architecture**

![Architecture](https://github.com/lkldev/TooInstance_Public/blob/main/images/TI_Architecture.jpg)

**Sample Flow of Application**

![Program Flow](https://github.com/lkldev/TooInstance_Public/blob/main/images/flow.jpg)
