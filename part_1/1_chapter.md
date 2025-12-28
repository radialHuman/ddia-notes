# Chapter 1
- Many applicaitons are data intensive not compute (CPU)
- Data intensive basic building blocks : storage, remember using cache, search index, message async stream processing, batch processing
- DBs have different characteristics, choose based on situation
    - Example : various ways of caching, multiple ways to find and filter create search index etc
- Objective for a system must be to choose which tool and approach to be used based on perfromance
    - hard to combine tools 
- explore principle and practicality in this chapter

## 1. Thinking about data systems
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

## 2. Reliability
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

### Importance of reliability
- bugs in business operations can lead ot productivity loss and legal ramifications
- outages can damage reputation and bottom line
- trade off : reliability vs production cost
    - inscase of testing new prod for market research etc

## 3. Scalability
- todays system might not work in the future because of load or demand
- systems ability to cope with load
    - not one dimensional 
    - its in respect with something that will sound like
        - if this increases, how will the system grow and how to add resources to it
        - and this can be anythign depending on the situation
### Load
- Only when we know what laod is we can think of how it will grow
- use number : load parameters
    - depend on the archi of the system, can be
    - req/sec to a webserver
    - ratio of reads to writes to a db
    - number of concurrant users on system
    - hit rate on cache
- it can be all it can be one and it can be badly impacting just a few, need to figure it out as per the applciaiton

#### example : twitter (2012)
- posting : 4.6kreq/sec on avg, and 12kreq/sec on peak
- view home timeline : 3kreq/sec
- seems easy but the problem was not the req/sec but fan out
    ```
    FANOUT
    # of req that needs to be done to other system to serve the incoming req
    ```
    - each user follows many and each user is followed by many
- Two ways of solving this
    1. when a new post is received, store it in one big db and then when people ask for others tweet, fetch it from all the followed ones and show
    2. when a new one is received, store it as well as put in caches of all the followers, which will reduce the latency
        - also, since the tweets are right there it does need to look up in the huge db
- 1 struggles as they grew and they had to move to approach 2
    - because avg rate of new posts is 2x lesser than asking for others tweet in homepage
    - so here doing more work at write is better than at read
- 2 has a problem too
    - writting new tweet is a lot, given each user has 75 followers
    - so 4.6k tweets/sec = 345k writes/sec to the home timeline caches
    - also there is no guarantee that the followers are distributed equally and the how freq are the peaks
    - avg does show the distribution so if something has followers in millions, they will become a bottle neck
- distrubition of followers/user is now also a key load paramter, weighted by how often those users tweet
    - didnot see that coming
- this is the part of fan out load
- As of now they use hybrid abse don user
    - most of the users are treated one way (#2)
    - while the ones that are having a lot of follower are treated in a different way (#1)
- more in chapter 12


### Performance
- when the clearly defined load parameters increases, what happens as a consequence of that also increases
- two ways
    1. increase load parameters and see the system resources : cpu, memory, n/w bandwidth constant - how does the performance of the system get affected
    2. how much does increases load paramters need system resources changed, to maintain the current level of performance
- performance numbers examples
    - batch processing 
        - throughput : # of records that can be processed/sec
        - total time taken : on a job of processing a dataset of a certain size
    - realtime
        - service response time :  time between user sending a req and receiving a response (felt by the client)
            - processing time + n/w delays + queueing delays
            - can vary for the same request , as it depends on multiple things
                - context switch of bg process
                - loss of n/w package and tcp retrainsmissioon
                - garbage collection pause
                - page fault , then suing to read from disk
                - mechanical issue in server rack (vibration)
                - etc etc
            - it is not one number but a distribution of values that can be measured
            - most of the request will be fast but some outliers might be there
                - may be they are expensive ex : data heavy
            - avg response time is not that infromative
                - usually an arithmetic mean (not a good one)
                    - as it doesn say how many users got huge delays
            - so use percentiles, by sorting the time taken and then show a box plot or p99
                - use median (p50)
            - to find out how bad the outliers are look for higher percentiles like p95, p99 and p999 (99.9)
                - these will give tail latency
                - affects ux a lot
                - ex : Amazon talks in p999, i.e performance is bad even if 1 in 1000 req exp delay
                    - as they process a lot of data and purchase a lot and most valuable
                    - they have noticed, an increase in 100ms response time reduces sales by 1%
                    - 1s delaye reduces customer rating by 16%
                    - optimizing p9999 (1 in 10k req)  was to expensive and not worth the effort on profit
            - at very high percentiles , random events can affect and are difficult to control
                - benifits diminish
            - SLO and SLA (service level aggreement and objective)
                - contracts defines the availability and performance of a system
                - Example : if the response time is 200ms the service is up and p99 at 1 sec
                    - service must be up 99.9% of the time
                    - else refund
                    - queueing delays is usually the culprit as server can only do so much
                      ```
                      HEAD OF LINE BLOCKING
                      since server can processes only so much in ||
                      limited by cpu cores
                      a few slow request can hold up the entire queue
                      even if the down the line req are fast to process the big once will block the road for them
                      ```
            - so measure the response time in the client side and see how they feel
            - to test aftrifical load, the client must keep sending heavy request irrespective of the response time
        - latency : is duration the request is waiting to be handelled,  it remains latent waiting to be processed

#### Percentile in practice
```
TAIL LATENCY AMPLIFICATION
- one slow call is enough to make the entire ux bad
- even if small % of BE calls are slow, the prob of getting more slwo call increases if the user's request requires multile BE calls
- so making more request being slow
```
- To add response time percentile in a dashabord, it should be calculated on an ongoing basis
- rolling window of 10 mins , calculate median and various percentile and show in graph
- no a good way : to keep a per min sorted list of all resposne time in a given time window
- algorithms : forward decay, t digest, hdr historgram

#### Approaches to coping with load
- Architcture thats okay for x load will not work for 10x, needs to be remade
- vertical vs horizontal (shared nothing, scaling out)
- it can be an hybrid
- Scaling can be automatic : elastic, adds systems as the laod increases automatically (when thinsg are unpredictable)
- when a state is share dbetween multile systems (horizontal), there can be additional complexity
    - need to check ease of use and maintainability
- no magic scaling sauce - no std architrecture, depends on:
    - read, writes, data to store, complixity of data, resposne time, access patterns, or combination of these and more 


## 4. Maintainability
- major cost of a s/w is not in the development but in the maintence
    - fixing bug, keeping it operational, investigating failuers, adpating to new platforms
    - modifying for new use cases
    - adding new features
    - repaying technical debt
- ppl dislike : fixing someones bugs, outdated platforms, working on systems that were not built for something but are forced to
- no general advise to maintain legacy system
- making them properly is the key using design principles
    1. operability : making things easy fo ops team
    2. simplicity : for new folks to understand the system
    3. evolvability : so that new thinsg can be added easily later (modifiability, extensibility, plasticity)

### Operability SKIPPED
## Summary