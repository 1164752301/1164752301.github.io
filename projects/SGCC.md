---
title: SGCC Knowledge Community
layout: page
---

## Inspiration

The largest company of China, SGCC, wants to reduce the number of customer service
stuff. So my institution is delegated to design an application to reach this target.
This application provides a safe place for users to communicate so that some of the
phone call tasks now will be handled by this application.

## What it does

* This community allows users to ask questions and answer questions from others. Users should login
first before they operate any action other than registering. 
* Users can also change password and logout. Every question can have a lot of answers. 
* People can view these questions, answers and the related information. 
* People can like or dislike any answers, but they can't do it repeatedly.
* People can view all questions, view questions by category, view their own questions, answers and
likes. 
* People can also search for the knowledge including some specific words.
Users can insert images or designate their own fonts in this app. knowledge can be
batch imported into this application.
* We also included a robot which allows users to ask questions and if the question is
in our knowledge base, then the user will be answered. otherwise, the user will be answered
with a fixed sentence.

## How we built it

* We used vue as the frontend framework, springboot as the backend framework,
mybatis as our persistence framework and Oracle as our database. At first we
used a plugin called mybatis generator which helps us generate mybatis mappers,
but as we extend functions of the app, we found that this plugin can't handle
complicated SQLs like union search. so we started to write our own mapper.
* We introduced a plugin called PageHelper to help us realize the paging function.
* We introduced a rich text editor called tinymce so that users can upload images
and designate their own fonts. The images should be accepted in form of Multipart file.
* We used a package called easyExcel to handle excel input and output.
* We used a series of tools called swagger to generate dynamic and static documents of
  interface.  
* We used logback as our log service provider.
* We used Ali cloud OSS to save our images and We deployed the oracle database with docker
  on our own.  
* We used docker to deploy the app onto the cloud, and used jenkins to make this
progress automatic.

## Challenge we ran into

* We used a batch insert to batch import data. Since Oracle doesn't
  have a serial type, And we wanted to use serial id to speed up the
  sort. We considered two methods to handle this.
  1. Get a large bundle of numbers from a serial of Oracle, set them
     into the objects to be inserted and then insert all these objects
     with ids. This method works but it consumes too much time. for a
     data bundle with about a million answers in it, one insert action
     will take about an hour.
  2. Read the current value of a Oracle serial and reset it to a new value
     (which equals current value plus size of data to be inserted).
     Then we set and increase it for every data to be inserted, and insert these
     data. This method is much faster than the first one, but if an insert happens
     between the read and the reset, there will be duplicated key in id. So this
     method actually requires a transaction management, which makes the read and
     reset operations an atom.
  ---
  We chose the first method to deploy but we updated the second method to a newer
     version.   
* We got three knowledgeIds and we wanted to search for them consecutively. However,
in order to avoid the unintended click of users, we prevented any operation which
  hasn't received a response yet. Since we call the service in a for loop, we decided
  to use a async in the service so that one call will not be executed until no other
  call is executed.
* We had three tiers in our app. When we click on a knowledge base, we want to get into
the knowledge in it. Since all knowledge bases share exactly one page, it's tricky to
  make the page different for different knowledge base. We decided to store the 
  knowledge base id into local storage when users click on a knowledge base. 
  Since any time a user wants to enter a new knowledge base, the user will click on it.
  So we don't have to worry that users get wrong knowledge in a knowledge base.
* When uploading .xlsx or .json files into the app, at first it was always rejected.
we tried to debug in chrome but we found it reported CORS Error. We changed the
  CORS filter and we added annotations related to CORS but it didn't work. Then we
  found that it could accept very small single file but it rejected large single file or
  a bundle of files. Finally we changed configurations in application.properties and
  increased the max size of files to be transported.
  