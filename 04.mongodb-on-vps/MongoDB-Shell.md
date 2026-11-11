# Connect to MongoDB using MongoDB Shell
**MongoDB Shell (mongosh) is a command-line interface for interacting with MongoDB.**

```py
mongosh

# Check the server:
db.runCommand({ ping: 1 })

# Get MongoDB server information:
db.version()

# Show all databases:
show dbs
```


## MongoDB database and collection commands

```py
use techBook
db.createCollection("students")
show collections
```

## insert data in mongodb

```
db.students.insertOne({
    name: "Rahul",
    age: 22,
    email: "rahul@gmail.com",
    course: "Python",
    city: "Delhi"
})
```
