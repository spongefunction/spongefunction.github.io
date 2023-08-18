---
title: First trial on MongoDB
slug: mongodb-hands-on-notes
date: 2023-08-18T01:38:37-04:00
draft: false
categories:
- database
- hands-on
tags: 
- mongodb
summary: "Playing with a new db is fun! Especially when it is not in the required courses."
---



This is my note from Wilson Ren's web dev course on Udemy. Wilson is using version 4.4.2 to build a demo project while I prefer to explore with newer version and add some structure and background knowledge to my wander.

# Installation with homebrew

Official document for installation on Mac:

https://www.mongodb.com/docs/v6.0/tutorial/install-mongodb-on-os-x/

```bash
brew tap mongodb/brew
brew update
brew install mongodb-community

# To start mongodb/brew/mongodb-community now and restart at login:
brew services start mongodb/brew/mongodb-community
brew services stop mongodb/brew/mongodb-community

# To verify that MongoDB is running, perform one of the following:
brew services list

# to use MongoDB
mongosh

# to use the MongoDB Database Tools
mongotop
```

The binary command to start MongoDB changed from `mongo` (v4.4)to `mongosh`(current version 6.0.6), which may cause some confusion. At first I ran `mongo` and `mongod`, and neither gave me correct response.

After checking `/usr/local/bin`(you can get this path by running `brew --prefix`) directory, I found all binary commands related with mongo, and legitimately guessed which to run.

> list of mongo related binary commands:
> 
> mongod
> mongodump
> mongoexport
> mongofiles
> mongoimport
> 
> mongorestore
> mongos
> mongosh
> mongostat
> mongotop

As the document says: "The legacy `mongo` shell was deprecated in MongoDB 5.0 and removed in MongoDB 6.0. The new MongoDB Shell, `mongosh`, offers numerous advantages over the legacy shell. ... Some legacy methods are unavailable or have been replaced with updated methods in `mongosh`. To maintain backwards compatibility, the legacy methods that `mongosh` supports use the same syntax as the corresponding methods in the `mongo` shell."

# Basic facts to know about MongoDB

- MongoDB is not relational database.
  
- MongoDB use BSON instead of JSON to store things. BSON stands for Binary JSON. It is a binary encoded serialization of JSON documents, and supports more data formats than JSON - for example, float, and binary objects.
  
- Collections in MongoDB is like tables in MySQL.
  
- Mongo's shell is a fully functional javascript shell, so it can process js code.
  

# Basic commands

Within `mongosh` console, here are some common commands to use:

```js
db // to show current database
use [someDB]
use [anotherDB]
show dbs // to show all databases
show collections // to show all collections
```

# CRUD

Document: https://www.mongodb.com/docs/mongodb-shell/crud/

## Create -> insert

```js
// db.collection.insertOne()
use sample_mflix
db.movies.insertOne(
  {
    title: "The Favourite",
    genres: [ "Drama", "History" ],
    runtime: 121,
    rated: "R",
    year: 2018,
    directors: [ "Yorgos Lanthimos" ],
    cast: [ "Olivia Colman", "Emma Stone", "Rachel Weisz" ],
    type: "movie"
  }
)

// db.collection.insertMany() pass an array of documents to method
use sample_mflix
db.movies.insertMany([
   {
      title: "Jurassic World: Fallen Kingdom",
      genres: [ "Action", "Sci-Fi" ],
      runtime: 130,
      rated: "PG-13",
      year: 2018,
      directors: [ "J. A. Bayona" ],
      cast: [ "Chris Pratt", "Bryce Dallas Howard", "Rafe Spall" ],
      type: "movie"
    },
    {
      title: "Tag",
      genres: [ "Comedy", "Action" ],
      runtime: 105,
      rated: "R",
      year: 2018,
      directors: [ "Jeff Tomsic" ],
      cast: [ "Annabelle Wallis", "Jeremy Renner", "Jon Hamm" ],
      type: "movie"
    }
])
```

## Read(query) -> find

```js
// db.collection.find()
use sample_mflix
db.movies.find() // to return all documents from collection
// = SELECT * FROM movies

db.movies.find( { "title": "Titanic" } )
// = SELECT * FROM movies WHERE title = "Titanic"

db.movies.find( { rated: { $in: [ "PG", "PG-13" ] } } )
// = SELECT * FROM movies WHERE rated in ("PG", "PG-13")

db.movies.find( { countries: "Mexico", "imdb.rating": { $gte: 7 } } )
// implicit AND operator (there is no actual AND)

db.movies.find( {
     year: 2010,
     $or: [ { "awards.wins": { $gte: 5 } }, { genres: "Drama" } ]
} )
// explicit OR operator
```

## Update -> update

```js
// db.collection.updateOne()
// $set
use sample_mflix
db.movies.updateOne( { title: "Twilight" },
{
  $set: {
    plot: "A teenage girl risks everything–including her life–when she falls in love with a vampire."
  },
  $currentDate: { lastUpdated: true }
})

// db.collection.updateMany()
use sample_airbnb
db.listingsAndReviews.updateMany(
  { security_deposit: { $lt: 100 } },
  {
    $set: { security_deposit: 100, minimum_nights: 1 }
  }
)

// db.collection.replaceOne()
db.accounts.replaceOne(
  { account_id: 371138 },
  { account_id: 893421, limit: 5000, products: [ "Investment", "Brokerage" ] }
)
db.accounts.findOne( { account_id: 893421 } )
```

## Delete -> delete

```js
// db.collection.deleteMany()
// if want to delete all, pass an empty filter document {} to method
use sample_mflix
db.movies.deleteMany({})
db.movies.deleteMany( { title: "Titanic" } )

// db.collection.deleteOne()
// will delete only the first that matches condition
use sample_mflix
db.movies.deleteOne( { cast: "Brad Pitt" } )
```