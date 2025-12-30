# Chapter 2
- important part : data models
- most of them are in layers
- question : how are they represented in each layer
- in an applciation, data can be stored as objects : json, tables etc.
- s/w engineer whill think of how to pass them around in an api
- below that, hardware people will think of how to convert bytes to electrical signals
- complexity varies but the fundamentals remain the same
- these abstractions allow different roels to work in sync
- different data model, different assumptions on how it can be sued
- There are many datamodels for storage and querying

## Relational model vs document model
- sql is the famous one (relational)
    - data is arranged into relations : tables
    - rows : tuple of unoerdered collections
    - RDBMs and sql got popular in 1980s
    - business data processing in mainframe
    - sales, banking, batch processing
- n/w and hierarchical model 
- object databases
- xml databases
- relational was generalizable and stood the test of time
- NoSql
    - not only sql
    - scalability
    - high write through put
    - os over closed
    - specilized query o/p
    - more dynamic than sql
``` 
POLYGLOT PERSISTANCE

- using both relational and non relational for an appliciaiton
```
## Object relational mismatch
```
IMPEDANCE MISMATCH

- most of the languages are based on OOPs
- so a middle layer is required to convert objects to rows and columns
```
- Object relational mapping (ORM) framework : active record and hibernate
    - reduces bolierplate code for this trasnlation
- example : resume, can be used in relational by sotring first name last name but
    - when it comes to jobe xp, it varies from person to person and thus can have a fixed structure offered by sql
    - 1:many
- json can handle this, and many relaitonal dbs are supporting this : ibm db2, mysql and postgres
- another option can be to store such varying data in json in a text column and let the applcaition handle, parse and extract for use
- json is simpler than xml : used in mongodb, couch, expresso
    - though it reduces the impedence mismatch
    - it has an encoding problem (Chapter 4)
    - for the resume example, if done purely in relationsla with multiple tables and joins, it take multiple queries
    - while in json everything is in one place
    - 1:many relation can be seen as a tree in json

### many to one and many to many relationship
- instead of passing address as a string in a text box, it should be standardized using dropdown
    - this will help in changing, searching and analysisng them later
- storing a value/category can be done using id
    - have a place where ids and string are matched, and in all the tables, id is used to represent the value
    - this si not great for reading but for storing, its more efficient
    - this way even if the string changes later, it only has to change in one place (mapping), the ids can remain the same
        - this is maintainability, it avoids write time, and inconcistencies while changing duplicates
```
NORMALIZATION in DBs

- Removing duplicate values and replacing them with ids and having it all mapped in one location 
- more on this in chapter 3 as denormalization is another debate
```
- Joins in document dbs are weak and not sually used as everything is right there, somewhere down the tree if related
    - and usually done by the applciation by quering it multiple times and writing custom logic
- in document dbs, these refernces of ids are not used, hence there can be duplicates
    - city being the same value but instead of id, its the actualy cities name for each candidate
- This problem was applicable even in the IMS by IBM which was a hierarchical data modelling

## The network model
- Standardized by a committe CODASYL 
- generalization of hierarchical model : every node had one parent
- every node can have multiple parent
- allowed many to many and one to many relations. Instead of primary key it used pointers to make relations
- Things were accessed by access path, simplest form : traversal of linked list
- In case of many to many, there can be a lot of paths, like navigating in n dimensional space
- The code to update and write in db was complicated and inflexible
    - without the path to data its difficult


## Relational model
- No nested structures, no access path, just open tuples of collections
- read all rows, use arbtrary conditions, filters, insert
- query optimizer : decides which part of the query to execute in what order
    - automatic, not by the appication developer
    - build after a lot of r&d, quite complicated
- easier to add new feature

## Dcoument db
- like hierarchy
- n:n and 1:n using foreign key
- join or follow up queries
- Flexible schema, better performance and matches the applciation data (json, dict)
    - schema on read (dynamic)
- Cons
    - cant refer to nest items inside the document
    - first.second.third type only
- no joins might be an issue if applciation is designed differently
- not suitable for n:n 
- joins needs to be inside applicaiton, can slow down performance
- huge documents have all required info in on locality
    - googles spanner db is relational but had localality like properties
    - orcels : clusters
- many relationla dbs have xml handlelling ability
- postgres, mysql and ibm db2 support json
- hybrid is good
- query language 
    - sql : relational algebra (declaritive), parallel execution
    - document : imperative, cant parallalize