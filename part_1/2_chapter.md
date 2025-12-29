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
- 