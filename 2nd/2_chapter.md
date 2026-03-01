## 1. Non functional requiremeents 
- important

## 2. Buidling twitter
- constraints and peak load
- posts/second
- fan out issue
- latency, disk io
- pull model is not useful for regular users
- fanout on write : materialsed view for precomputed cache
    - shift from read time to write time 
    - each user has a cache with time line
    - it does multiple tiny writes to a multiple users cache
    - on incoming write fansout to multiple outgoing writes
    - useful when reading is the major part
- edge cases : celebrity, beeper bug
    - fanout for them is huge
    - can lead to head of line blockage
    - push model also fails 
- hybrid system 
    - for normal users use fanout
    - for vips pull model

## 3. Performance and latency
- Through put : request/sec
- resposne time : latency from user to server
    - servcie time :  how long the processor spinsfor a task 
    - latency  : service time + the waiting time in a queue
    - dont use mean : average is a lie doesn show UX
    - so use p95+
- AWS : the slowest percentile was the most valueable as they were the whales
    - it depends on the product
    - for them they had to optimize for p99
    - not always the case
- importance of tail latency, apart from aws and googles issue above
    - tail altency amplification usually happens in microservices
- reliability
    - infinite latency
    - fault : single component deviating from its task
    - failure :  sytem stops working as a whole
    - SRE : make systems fault tollerant to prevent failure

## 4. Hardware
- sharks eat the cables underwater
- construction workers
- thunder storms
- cosmic wave
- hard disk failure : 1 drive failure everyday 
    - redundancy

## 5. S/w
- s/w faults are not as independent as h/w, things cascade

## 6. Humans
- config changes
- new code deployment
- tweaking db setting
- changing routing setting
- prevent them
    - guardrails
    - confirmation dialog
    - automated roll back
    - blameless postmortems
- the system needs to prevent this, automate the detection, prevent the mistakes again

## 7. Scalability
- Its relative, coping mechanism
- whats the load thats growing?
- specific dimension to scale?
- scale up vs scale out
- Vertical : physics and budget wall hits
- Horizontal : painful, n/w of distributed system
- CAP : only two can be picked. but in distributed, its only either c or a
- A is eventual C
- Dont copy the bigger companies solution for early stage
- premature optimization
- dont design for scale thats not going to happen

## 8. Maintainence
- most costly part
- design for maintence : documentation
- operability : life managaeable for the operations team, visibility or metrics, proper logs
- simplicity : accidental complexity needs to be avoided, variable names, sphagetti code, random tech in codebase for no reason, abstraction
- evalability : ability to change systems later, agile principles, business laws, privacy laws change so adpat. maleable

- CHaos engineering : by netflix, turns of prod servers during the day, to see whats breaking and cascading effect. if architecture done properly, this shouldn scare you. Healing must be built in 