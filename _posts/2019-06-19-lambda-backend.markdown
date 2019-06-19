---
title:  "A serverless backend using AWS Lambda with Node.JS"
date:   2019-06-18 22:54:30 +0200
layout: post
categories: [build instructions, backend]
tags: [build instructions]
excerpt_separator: <!--more-->
---

How reshelf works behind the scenes

<!--more-->

## Requirements-Engineering

The reshelf backend has to keep track of what items are in which shelf and allow users to take items from the shelf or put new items into it. This also necessitates a rudimentary user management.

## Technologies

The backend is written in TypeScript and runs serverless on AWS Lambda connected to Mongo database running on AWS EC2.
Originally we intended to use AWS DynamoDB as a database to be truly serverless however, due to limitation related to search queries as well as generally higher development effort, we chose to go with MongoDB for the prototype.

## Transaction time

The most complex interactions with the backend API are transaction, i.e. a user picking up or depositing an item. The complexity stems from the fact that a transaction requires coordination of the app, the shelf controller and the database state.  
To deposit an item in the shelf, the user takes a picture of the item and enters the necessary information about the item such as a description and a name. This information is uploaded to the API and stored as temporary item in the database. Next the app retrieves a list of shelves close to the user from the API. The user then picks a shelf from the map requests a transaction from the API.  
The backend chooses a free spot in the shelf and generates a unique PIN code which is stored in the database and returned to the app. The user then enters this PIN code via the keypad or it is transmitted via NFC if the user touches the shelf with their phone.  
The shelf controller sends the PIN code back to the API which returns the corresponding transaction id if the PIN was valid. The shelf then commits the transaction with another request. This request stores the previously temporary item information and its location in the database. The shelf receives the index of the box which will store the item and opens the lock.  
  
To pickup an item the process is essentially the same except that the app initially retrieves a list of items that are nearby. When the user picks an item and request to take it, a transaction is created.  
