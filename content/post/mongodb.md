+++
date = "2016-09-13T13:46:10+02:00"
title = "CMake"
tags = [ "dev", "db", "mongo" ]
+++

# MongoDB

```
help

show dbs
show collections
show users
show roles
show profile
```

```
use <db>

coll = db.<collection>
coll.find();
```

## operators

* $eq, $gt, $gte, $lt, $lte, $ne, $in, $nin
* $and, $or, $not, $nor

## find

```
db.restaurants.find()
db.restaurants.findOne()
db.restaurants.find( { "grades.grade": "B" } )
```

```
db.users.find( { name: null } )
db.users.find( { name : { $exists: false } } )
db.users.find( { name : { $type: 10 } } )
```

```
db.restaurants.find( { "grades.score": { $gt: 30 } } )
db.restaurants.find( { "grades.score": { $lt: 10 } } )
//and
db.restaurants.find( { "cuisine": "Italian", "address.zipcode": "10075" } )
//or
db.restaurants.find(
   { $or: [ { "cuisine": "Italian" }, { "address.zipcode": "10075" } ] }
)
db.users.find( { status: { $in: [ "P", "D" ] } } )
db.users.find( { status: "A", age: { $lt: 30 } } )
db.users.find(
   {
     status: "A",
     $or: [ { age: { $lt: 30 } }, { type: 1 } ]
   }
)
```

### query embedded document

```
db.users.find( { "favorites.artist": "Picasso" } )
```

### query array

```
//exact match
db.users.find( { badges: [ "blue", "black" ] } )
//match array element
db.users.find( { badges: "black" } )
//match specific array element
db.users.find( { "badges.0": "black" } )
//Multiple Criteria for Array Elements
db.users.find( { finished: { $elemMatch: { $gt: 15, $lt: 20 } } } )
// Array of Embedded Documents
db.users.find( { 'points.0.points': { $lte: 55 } } )
db.users.find( { 'points.points': { $lte: 55 } } )
db.users.find( { points: { $elemMatch: { points: { $lte: 70 }, bonus: 20 } } } )
db.users.find( { "points.points": { $lte: 70 }, "points.bonus": 20 } )
```

## sort

```
db.restaurants.find().sort( { name: 1 } );
db.restaurants.find().sort( { name: -1 } );
db.restaurants.find().sort( { "borough": 1, "address.zipcode": 1 } )
```

## projection

```
db.restaurants.find({}, { name:true, restaurant_id:true} )
db.users.find( { status: "A" }, { name: 1, status: 1, _id: 0 } )
// return fields of embedded doc
db.users.find(
   { status: "A" },
   { name: 1, status: 1, "favorites.food": 1 }
)
// return all but excluded
db.users.find( { status: "A" }, { favorites: 0, points: 0 } )
// return  Specific Array Elements
db.users.find( { status: "A" }, { name: 1, status: 1, points: { $slice: -1 } } )
```

## limit

```
db.restaurants.find().limit(1)
```

## skip

```
db.restaurants.find().limit(10)
```

## insert

```
db.users.insertOne(
   {
      name: "sue",
      age: 19,
      status: "P"
   }
)
```

```
db.users.insertMany(
   [
     { name: "bob", age: 42, status: "A", },
     { name: "ahn", age: 22, status: "A", },
     { name: "xi", age: 34, status: "D", }
   ]
)
```

```
// insert single doc
db.users.insert(
   {
      name: "sue",
      age: 19,
      status: "P"
   }
)

// insert multiple docs
db.users.insert(
   [
     { name: "bob", age: 42, status: "A", },
     { name: "ahn", age: 22, status: "A", },
     { name: "xi", age: 34, status: "D", }
   ]
)
```

## update

* `{ upsert : true}` - update or insert new
* `{ multi : true}` - update multiple dics
* updateOne
* updateMany
* replaceOne/update

### update operators

* $inc, $mul, $rename, $setOrInsert, $set, $unset, $min, $max, $currentDate

```
db.restaurants.update(
    { "name" : "Juni" },
    {
      $set: { "cuisine": "American (New)" },
      $currentDate: { "lastModified": true }
    }
)
```

```
db.restaurants.update(
  { "restaurant_id" : "41156888" },
  { $set: { "address.street": "East 31st Street" } }
)
```

### update multiple docs

```
db.restaurants.update(
  { "address.zipcode": "10016", cuisine: "Other" },
  {
    $set: { cuisine: "Category To Be Determined" },
    $currentDate: { "lastModified": true }
  },
  { multi: true}
)
```

### update array

#### $elemMatch

```
db.students.update(
   {
     _id: 4,
     grades: { $elemMatch: { grade: { $lte: 90 }, mean: { $gt: 80 } } }
   },
   { $set: { "grades.$.std" : 6 } }
)
```

#### placeholder $

```
// positional $ operator acts as a placeholder for the first match of the update query
db.students.update(
   { _id: 1, grades: 80 },
   { $set: { "grades.$" : 82 } }
)
db.students.update(
   { _id: 4, "grades.grade": 85 },
   { $set: { "grades.$.std" : 6 } }
)
```

#### $addToSet

```
db.users.update(
  { letters : { $exists: true } },
  { $addToSet: { letters: "g" } }
)
```

#### $each

```
db.users.update(
  { letters : { $exists: true } },
  { $addToSet: { letters: { $each:["f","e"] } } }
)
```

### replace doc

```
db.restaurants.update(
   { "restaurant_id" : "41704620" },
   {
     "name" : "Vella 2",
     "address" : {
              "coord" : [ -73.9557413, 40.7720266 ],
              "building" : "1480",
              "street" : "2 Avenue",
              "zipcode" : "10075"
     }
   }
)
```

## remove

```
db.restaurants.remove( { "borough": "Manhattan" } )
db.restaurants.remove( { "borough": "Queens" }, { justOne: true } )
//remove all docs
db.restaurants.remove( { } )
//drop collection
db.restaurants.drop()
```

## aggregate

```
db.restaurants.aggregate(
   [
     { $group: { "_id": "$borough", "count": { $sum: 1 } } }
   ]
);
db.restaurants.aggregate(
   [
     { $match: { "borough": "Queens", "cuisine": "Brazilian" } },
     { $group: { "_id": "$address.zipcode" , "count": { $sum: 1 } } }
   ]
);
```
