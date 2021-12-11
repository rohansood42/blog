---
layout: article
title:  ResilientDoc - Verify Documents with Blockchain 
author: alex
tags: Project Walkthrough
aside:
    toc: true
---


## Problem Statement
Contracts and legal documents are prone to getting changed and cannot be verified using a digital medium, parties can be cheated by forging the document or by changing the contents of it and the terms and conditions which were initially set in. This is where the motivation for our project came into the picture.

When we started on the project we were building this solution to be blockchain agnostic so as to integrate any blockchain fabric for this solution. We started to build this project on ResilientDB which is the in-house blockchain fabric developed at UC Davis.

This solution would help reduce fraud in the system and also help in digitizing of the records which were earlier present in only a physical format.

## Solution: ResilientDoc
![alt text]({{ site.baseurl }}/assets/resilientdoc.jpg)

We built **ResilientDoc** with an aim to provide the key principles of transparency, distributed and fault tolerance to the users so that they would be able to keep their documents with utmost trust that it would be able to fulfill the main task for the application. 

While working on the project we envisioned to use blockchain as a notoriety service, which would be able to ratify documents and prove the originality and non temperability of it using blockchain. 

**ResilientDoc** uses a blockchain to store the contracts which is confirmed by all the parties and then commited onto a blockchain. Blockchain is used since it becomes very difficult to commit internal fraud or external hacks of sensitive records. Access to the blockchain is limited and controlled and even those with access can’t arbitrarily make changes to the record.

**ResilientDoc** is a full stack application built using React in the front end along with Node.js/Express in the backend. The frontend application is handling all the UI logic associated with the application, whereas the Express backend is built envisioning it as a modular unit which can be reused with another application to be connected with the blockchain of your choice and can be modified along with a new use case of the application. It will automatically handle the connection with the blockchain once you connect the interface which handles the endpoint for the blockchain.

## Front-End Development
The frontend for the application was developed in React, where we utilized all the new features such as functional components with state hooks so as to have maintainable and clean code. Also we developed multiple components which we will be discussing below and what all we were aiming to achieve in this application.

<img src="{{ site.baseurl }}/assets/landing-page.png" alt="Landing-page"/>

<center>Landing Page for Resilient Doc</center>

<img src="{{ site.baseurl }}/assets/Activity-Diagram-2.png" alt="Activity-Diagram-2"/>
<center>Sign Up Activity Diagram</center>

* Landing page: The landing page is the first interface the user will interact with. It contains the following components:
    * Signup: A button that will invoke a modal that takes new usernames and password information, and stores them in the database for future authentication. A user who has already signed up will be redirected to the login modal.
    
    * Login: A button that will invoke a modal that takes a username and password and compare these credentials to the database for existing users. If the user’s credentials are validated, they will be taken to the home page.
    
<img src="{{ site.baseurl }}/assets/home-page.png" alt="Home-page"/>

<center>Home Page with no files</center>

<img src="{{ site.baseurl }}/assets/home-page2.png" alt="Home-page2"/>

<center>Home page with confirmed files and history</center>

<img src="{{ site.baseurl }}/assets/Activity-Diagram.png" alt="Activity-Diagram"/>
<center>Document Activity Diagram</center>

* Home page: The home page is the dashboard where users can manage their documents in the workflow. It contains the following components :

    *  Profile: the profile component displays the user’s initial as a profile picture and user full name used for signing up. We are looking into adding new features to allow users to upload pictures during the signup process and display uploaded pictures in this section.
    
    *  Tab Filter: The tab filter component contains three sessions that represent three different document statuses: Confirmed, Pending, Rejected. The confirmed session only displays documents that the user has uploaded and cosigned/confirmed by other users; The pending session only displays documents that the user has uploaded and waiting for other users’ approval; The rejected session only displays documents that are uploaded by the user by not being approved by other users.
    
    *  Document Card: The document card component contains the basic information (file name, upload time, extension, modification status, etc) of an uploaded document. It also contains buttons that allow users to delete documents or, modify cosigners.
    
    *  Upload: A button that will redirect the user to the upload page.

    
<img src="{{ site.baseurl }}/assets/upload-page.png" alt="Upload-page"/>

<center>Upload Page for Resilient Doc</center>

* Upload page: The upload page is where the user can upload an original document and add cosigners to the document. It contains the following components:

   *  Form: The basic react form component allows the user to choose a document to be uploaded and add cosigners for the document. The first input space takes in a document and displays the basic information (doc name, doctype) of that document. In the backend, it also generates a hash value to uniquely identify the uploaded document for future verification use.
    
   *  Upload: A button invokes backend requestst to store doc hash value as well as cosigners’ request to the database. It will also send requests to listed cosigners for approval.

## Back-End Development
We built the backend for the application in Node.js using the Express framework and the persistent database that we are using for the project is a MongoDB document storage. The choice for this particular database was because the data that we have is not really relational in nature and has fluid nature in the schema. Also, we are using a JS stack it is much easier to integrate a JS database which enables easy interfacing with the backend as well. We created multiple API endpoints to enable the ResilientDoc to function and connect with the blockchain layer as well. We have also created instructions on how to run the backend and connect to the local MongoDB instance or an Atlas instance in the cloud which is available on GitHub page linked in the related section.

We are using JWT tokens to have authenticated routes for the backend so that unauthorized access is prevented to gain access to the data and change it via the API's that have been provided. Authorization token needed for all the other APIs, header x-access-token: '' needs to be passed. The current implementation does not have a Blockchain interfaced to the solution but instead we are mocking the blockchain with a MongoDB document store which is being used with minimal API, which is basically as a key value store.

API endpoints as below:

| API endpoint|REST Verb| Auth token needed|Request Type| Request Structure |Request Description|
|--|--|--|--|--|--|
| `/auth/register` | POST |No|JSON| `{"first_name": String,"last_name": String,"email": String,"password": String}`|Register a new user
| `/auth/login` | POST |No|JSON| `{"email": String,"password": String}`|Login a user and get JWT token to access other APIs
| `/users/all` | GET |Yes|| |Get all the users registered in the app
| `auth/search?id=""` OR `/auth/search?email=""` | GET |Yes|| `{"first_name": String,"last_name": String,"email": String,"password": String}`|Get a particular user with email of ObjectId of the user
| `/document/upload` | POST |Yes|Form-data| `{"file": Upload a file here,"name": String,"associated_users": Array of Strings of ObjectId,"document_hash" : String}`|Modify the document
| `/document/upload` | PATCH |Yes|Form-data| `{"file": Upload a file here,"name": String,"associated_users" :Array of Strings of ObjectId,"document_hash" : String}`|Register a new user
| `/document/data` | PUT |Yes|JSON| `{"_id": String,"name": String,"associated_users": Array of ObjectID}`|Edit the data for the particular document
| `/document/download?id=""` | GET |Yes|| |Download a particular file
| `/document/user?type=` | GET |Yes|| |Get all the documents for a particular user pass [Confirmed] , [Pending] , [Rejected] to get pending or transactions
| `/document/verify` | POST |Yes|JSON| `{"_id": String,"document_hash": String}`|Verify a document with a particular fileId
| `/document/reject` | POST |Yes|JSON| `{"_id": String}`|A particular user rejects all the changes in the document
| `/document/delete?id=""` | GET |Yes|||Delete a particular file
| `/document/approve` | POST |Yes|JSON| `{"_id": String}`|A particular user approves all the changes in the document


UML diagram for the different tables showing their relationship and how data is referenced between each other.

<img src="{{ site.baseurl }}/assets/Document_Verification_UML.png" alt="UML_Diagram"/>


## Integrations between the interfaces

For communication between the backend and frontend, we consumed the REST API endpoints created on the backend using axios which can be installed using the node package manager on the frontend. Axios allowed us to make promise based HTTP requests on React and hit the endpoints created on our backend to exchange the necassary data. We used local storage for storing and acessing the user's session data and the token that is used to validate each request.
>  Enable  CORS  (Cross  Origin  Resource  Sharing)  headers  in  the  backend  when  dealing  with  requests  from  other  platforms  or  you  will  get  CORS  errors  whenever  you  make  cross  origin  requests.

For communicating with ResilientDB, we elected to use the [Crow](https://crowcpp.org/) framework. Since ResilientDB is written in C++,  a C++ based micro-framework is needed so that we can expose endpoints on ResilientDB that can be accessed from the application's backend. Since ResilientDB is packaged and deployed using Docker, a network port had to be exposed by adding the necessary configurations in the Docker make file. Then you can add your crow server in the ResilientDB code itself and run the consensus algorithms using data passed to it from the backend. Endpoints can also be exposed to get the data stored in the blockchain and send it to the backend.

## Demo

The demo for our application can be found [here](https://drive.google.com/file/d/1lFcGJO3s-MqLLGMsGTBHpZm0eYUz4AQA/view?usp=sharing).

## Future Work

We feel this project is very extensible and we would like to add more features to it, and make it a full scale production level application with ResilientDB or any other blockchain in place. Ideally, We would like to use ResilientDB to connect as the blockchain layer for this application, as ResilientDB is one of the in-house fabrics developed in UC Davis and it would make help as a guide for other students to build on top of ResilientDB by following how were building our own application.

To achieve this we have been thinking of having an interface which would handle the blockchain connections and would replace the current MongoDB commits that are mimicking the commits of blockchain layer. This interface would be acting connecting to the blockchain and have functions which would make the asynchronous calls to blockchain and would not be waiting for the response directly from the blockchain. Instead we would be using a RabbitMQ or a Kafka bus to receive the responses from the ResilientDB once consensus is formed on the transactions. Our Node.js layer interface would be observing the Kafka or RabbitMQ bus for updates using a pub-sub model. Once an update would be pushed to that system, the interface would update our local MongoDB which stores the status of all the transactions along with the history and update it with what it receives from the bus.

Also we can add more features to the front-end application and eliminate file uploading in our own layer but instead integrate with a cloud file storage like Google Drive or Box, which would have files and contracts stored in that and we can use those by connecting with the respective SDK and have links which would be stored in the local storage without actually having to store the files on our server. This make our solution more ubiquitous and easy to use and would help users onboard  the files onto our ResilientDoc server without have duplicity and redundancy.

## Links

[React UI Repository [Frontend]](https://github.com/MichaelY666/resilientdb_frontend)

[Express Repository [Backend]](https://github.com/rohansood42/resdb_backend_documentapp)

## Team

**Rohan Sood**: Responsible for organizing the whole team, along with architecting the solution in a modular way. Creating the backend APIs for the application on Node.js/Express and integrating them with the React frontend. Maintaining the database for the application on MongoDB is also one of the tasks taken up. Will also be helping on the front end development to expedite the whole process.

**Utkarsh Drolia**: Responsible for developing the microservice on ResilientDB that will communicate with the backend for external applications and talk to ResilientDB to perform transactions, get transaction history, etc. The microservice will be built using the Crow framework. Also responsible for integrating the frontend and backend by using the API’s created on the backend for authentication and data exchanges.

**Michael Yang**: Responsible for the front-end development of the web application. The application consists of three major parts: The user interface developed in React, the backend server developed in Express using RESTful API, and a communication channel between the backend and ResilientDB. Specifically, Michael is responsible for designing a user interface that allows original document owners to upload their documents that contain information that is sensitive to changes. It will also allow other users who intend to verify if their document is identical to the original copy to upload their documents for verification. The users will receive feedback and prompt from the app regarding their uploading and verification status.

**Kavyasree Kollipara**: Responsible for developing the backend using Express framework and integrating REST APIs of ResilientDB to the web application.The purpose is to connect the UI and REST APIs of ResilientDB. The backend is to implement the verification of the document by communicating the state with ResilientDB and reporting it to the user.

<img src="{{ site.baseurl }}/assets/gantt-chart.png" alt="Upload-page"/>
<center>Gantt Chart for the tasks</center>