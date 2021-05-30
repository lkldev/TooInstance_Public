# TooInstance (Previously known as EviThemis)
## Orbital 2021
NOTE: **TBD - To Be Done**
- [1. Introduction](#introduction)
- [2. User Stories](#user-stories)
- [3. Architecture and Flow](#architectureFlow)

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
| \* \* \* | User | Share the tool with other users | Let my friend/collaborator/colleague to try out without having to replicate the required environment |
| \* \* \* | User | See the logs of the tools I have ran | See what errors have occurred |
| \* \* | User | Reset my password | Change my password if I forgot |
| \* \* | User | Search for a specific tool instance within the public listing of instances created | Use the instance to test out with my desired inputs |
| \* | User | Enable 2FA | Prevent my account from being taken over |


## <a name="architectureFlow"></a>3. Architecture and Flow

**Overview of Architecture**

![Architecture](https://github.com/lkldev/TooInstance_Public/blob/main/images/TI_Architecture.jpg)

**Sample Flow of Application**

![Program Flow](https://github.com/lkldev/TooInstance_Public/blob/main/images/flow.jpg)
