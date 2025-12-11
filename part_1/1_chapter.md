# Chapter 1
- Many applicaitons are data intensive not compute (CPU)
- Data intensive basic building blocks : storage, remember using cache, search index, message async stream processing, batch processing
- DBs have different characteristics, choose based on situation
    - Example : various ways of caching, multiple ways to find and filter create search index etc
- Objective for a system must be to choose which tool and approach to be used based on perfromance
    - hard to combine tools 
- explore principle and practicality in this chapter

## Thinking about data systems
- DB, qs, cache are not that different but are different in perfromance characteristics and implementations
- each tool was created to optimize for a particular case
- a data store can also be sued as message qs : redis
- message qs that has db : kafka
- the way to distinguish them into neat buckets is getting difficult; so knowing what fits when where how and with what is important
- Data system desingin asks tricky questions
    1. how to ensure data remains corretc and complete even when thinsg go wrong internally
    2. how to provide good performance even when part of the system degrade
    3. how to scale with load
    4. in this situation how does a good api look like
- factors that impact the design
    - skills of the people involved in designing
    - legacy dependencies
    - time sclae of delivery
    - regulatory constraints
    - organization's toleration of risk
- three major concerns
    1. reliable : even when in trouble, the system must keep working as expected, be it h/w s/w human issue
    2. scaling : volume and complexity shouldn affect
    3. maintainig : over time, different people will work, they must be able to work on it productively
