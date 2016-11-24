Message Passing with YARP
=========================

# Prerequisites
You should know what the following classes are up to:
- [yarp::os::Thread](http://www.yarp.it/classyarp_1_1os_1_1Thread.html).
- [yarp::os::RateThread](http://www.yarp.it/classyarp_1_1os_1_1RateThread.html).
- [yarp::os::Semaphore](http://www.yarp.it/classyarp_1_1os_1_1Semaphore.html).
- [yarp::os::Port](http://www.yarp.it/classyarp_1_1os_1_1Port.html).
- [yarp::os::Bottle](http://www.yarp.it/classyarp_1_1os_1_1Bottle.html).

# Preamble
There exists a beautiful and yet undemonstrated conjecture in Mathematics that
deals with the so-called _trajectories_ of natural numbers. It is called **Collatz
conjecture** or, alternatively, **Half-or-Triple-Plus-One** (HOTPO).

Learn on [Wikipedia](https://en.wikipedia.org/wiki/Collatz_conjecture) what
trajectories are all about and how you can compute them for each given natural n,
supposedly ending up always in 1.

# Problem
We aim to design a **distributed client/server computing architecture** (see below)
whose plumbing is based on YARP to progressively verify the HOTPO conjecture.
The clients will thus perform concurrent verifications running on a cluster of
computers.

![architecture](/misc/architecture.png)

## Client Side
The Client is required to:
- Talk to the Server according to this [**protocol**](#protocol).
The Client requests the Server to obtain a **natural n and a threshold t** to then
give back the **test outcome**.
- **Verify the pair (n,t)**. The test terminates successfully if, at any step, the
trajectory of n becomes lower than or equal to t. Conversely, the test will not
terminate; therefore, the Server is responsible for constantly monitoring the
current state of pending requests.

## Server Side
The Server is required to:
- Communicate with the Clients according to this [**protocol**](#protocol).
- Handle a **FIFO** of pending requests received from the Clients according to the
following policy:
    1. At start-up, the counter **CNT is initialized equal to 0**.
    2. At each request received from Client C, CNT is increased and pushed back
    into FIFO, while the pair **(CNT,head-1)** is provided to C, being head the
    element stored at the top of FIFO.
    3. If the Client request contains the outcome of a test, then the Server
    removes the corresponding element from FIFO.
- **Monitor periodically the content of FIFO** in search of those elements which
have not been removed since long. These elements might be possible counterexamples
to the conjecture. It is sufficient to **report the content of FIFO at a given rate**.

## Protocol

### Client :arrow_right: Server request
A **Bottle** whose format is:

| Header | Payload |
| :---: | :---: |
| **vocab_req** | **n** |

- **vocab_req** is a proper identifier.
- **n** corresponds to the natural whose previous test was successful, or is **0**
at start-up.

### Server :arrow_right: Client response
A **Bottle** whose format is:

| Header | payload |
| :---: | :---: |
| **vocab_item** | **n**,**t** |

- **vocab_item** is a proper identifier.
- **n** is the natural for which a test is required against the threshold **t**.

# How to deal with this assignment
- **Students** are required to follow these [instructions](https://education.github.com/guide/forks#3-completing-assignments).
- **Reviewers** are required to follow these [instructions](https://education.github.com/guide/forks#4-reviewing-assignments).
