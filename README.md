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
deals with the so-called _trajectories_ of natural numbers. It is called the **Collatz
conjecture**.

Learn on [Wikipedia](https://en.wikipedia.org/wiki/Collatz_conjecture) what
trajectories are all about and how you can compute them for each given natural N,
supposedly ending up always in **1**.

# Assignment
We ask you to design a **distributed client/server computing architecture**
(see the figure below) whose plumbing is based on YARP to progressively verify
the **Collatz conjecture**. The clients will thus perform concurrent verifications
running on a cluster of computers.

![architecture](/misc/architecture.png)

## Client Side
The Client is required to:
- Talk to the Server according to this [**protocol**](#protocol).
- The Client requests the Server to obtain a **natural N** and a **threshold T** to
then give back the **test outcome**.
- **Verify the pair (N,T)**. The test terminates successfully if, at any step, the
trajectory of N becomes lower than or equal to T. Conversely, the test will not
terminate; therefore, the Server is responsible for constantly monitoring the
current state of pending requests.

## Server Side
The Server is required to:
- Communicate with the Clients according to this [**protocol**](#protocol).
- Handle a [**FIFO**](https://en.wikipedia.org/wiki/FIFO_(computing_and_electronics))
of pending requests received from the Clients according to the following policy:
    1. At start-up, the counter **CNT is initialized equal to 0**.
    2. At each request received from Client _Ci_, CNT is increased and pushed back
    into FIFO, while the pair **(CNT,HEAD-1)** is provided to _Ci_, being HEAD the
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
| **vocab_req** | **N** |

- **vocab_req** is a proper identifier.
- **N** corresponds to the natural whose previous test was successful, or is **0**
at start-up.

### Server :arrow_right: Client response
A **Bottle** whose format is:

| Header | payload |
| :---: | :---: |
| **vocab_item** | **N**, **T** |

- **vocab_item** is a proper identifier.
- **N** is the natural for which a test is required against the threshold **T**.

# How to complete the assignment
- **Students** are required to follow these instructions:
    1. To start, [fork the repository](https://guides.github.com/activities/forking/) by clicking on the [**invitation link**](https://classroom.github.com/assignment-invitations/cf638c49aa7f83e39b0598829c410605).
    2. [**Clone**](http://gitref.org/creating/#clone) the repository to your computer.
    3. Fill in the [**code directory**](/code) and [**commit**](http://gitref.org/basic/#commit) changes to complete your solution.
    4. [**Push**](http://gitref.org/remotes/#push)/sync the changes up to GitHub.
    5. [Create a **pull request**](https://help.github.com/articles/creating-a-pull-request) on the original repository to turn in the assignment.
    
- **Reviewers** are required to follow these instructions:
    1. Once created, you can then do **code review** with line-by-line feedback directly within the pull request. If students are allowed to submit corrections, they can push fixes up to their forks, which will be reflected in the pull request.
    2. Since we don't want any solutions in the original assignment repository, you should **leave the pull request unmerged**.
    3. When you're finished giving feedback, you can **close the pull request** and leave a :+1: (`:+1:`) in a final comment.
