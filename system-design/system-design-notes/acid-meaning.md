# ACID & CAP

## ACID Meaning

**Atomicity**: something that cannot be broken into smaller parts  
**Consistency**: application specific notion of the DB beginning in a good state  
**Isolation**: if accessing same DB records at the same time, will run into concurrency problem.  
**Durability**: provide a safe place where data be stored without losing it. 

Where ACID is used?   
A lot of databases offer ACID properties. DB transactions can be effectively used to **atomically** write data while ensuring consistency, either succeed or failed as a unit.   

## CAP Theorem

consistency, availability and partition tolerance

CA,   
AP,   
CP

