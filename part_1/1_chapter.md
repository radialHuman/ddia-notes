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

## Reliability
- In context of s/w
    - app perfroms expected function
    - tolerate user errors and not cause mistakes
    - perf. is good enough when under load
    - and there is no unauthorized access
- continuing to work correctly even when thinsg go wrong
- fault tolerant : system must be able to anticipate and deal with faults
    - resilient
    - misleading : cant handle all types of failures, as theres a lot of them
- different from failure
    - fault : is one component not working a expected
    - failure : the sys as a whole is not working as expected
- cant make the probability of fault tol. to 0
- mechanisms to be built to prevent faults that cause failures
- One way is to deliberately cause a compoenent to not work and see what happens and take steps accordingly
    - better error handelling
    - netflix : chaos monkey #doubt
- but still prevention is better than cure
- unlike security instances where ones something hacked all is lost
    - this book will focus on things that be cured

### h/w faults
- hd crash, ram fault, power outage, wrong n/w cables
- MTTF: harddisk mean time to failure of about 10-15 yrs
    - in servers on an avg, 1 disk dies/day
    - response is to add physical redundancy in an array
- dual power supply
- hot swappable cpus
- data centers can have batteries/generators
- have a backup
- but recently these measures have proven to be not enough
    - as data vol, and applciation have evolved
    - they demand more
    - even virtual machines disappear
        - because the platforms are designed for flexibility and on demand load adjustment over single machine reliability
        - there is a move towards s/w redundancy than h/w : rolling upgrade is better 1node at a time

### s/w errors
- h/w faults a random and independent between the nodes, 
    - so lanrge number of them will nto fail at the same time (the possibility is low)
- s/w error on the other hand can cause massive outages because of its interconnectedness and replication
    - cascading failure 
    - problem with underlying dependency
    - usually there but dormant, then one fine day, there is some combination of inputs that triggers it
    - edge cases whichw as not tested
    - assumptions underlying logic
- how to prevent it
    - carefully think about assumptions and interactions in the sys
    - test edge cases a lot (vareity)
    - process isolation
    - allowing processes to crash and restart and see the over all beahiour change 
    - monitroing and analysing system behaviour in production
    - constant checking of the basic guarantee and alert when deviates

### Human errors
- intentions doesn lead to error free system
- operators configuration can lead to issues
    - leading cause of errors
- prevention
    - desiginged in a way that reduces oppurtunities for error
    - so there is a trade off
        - well desinged abstraction, apis and admin pages
        - restricted interfaces will make people work around it
    - decoupling where people can make mistakes and where failure will occur
        - fully featured sandbox environment must be provided where people can explroe and experiment
        - using real data for real users
        - test throughly : unit test, whole sys integration test and manual test
        - automated testing on rare and edge cases
        - recovery must be quick to reduce impact from such failure
            - fast to rollback configuration changes
            - rollout new code gradually so that bugs will affect only few and will be known and handelled
            - provide tools to recompute incase the old one was incorrect
            - detailed and clear monitoring factors perf metrics and error rate
                - telemetry
        - good mgmt and training (not in focus here)