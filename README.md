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
    1. At start-up, the counter **CNT is initialized equal to 1**.
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
    1. To start, click on the **invitation link** that teachers emailed you. A **new repository** will be spawned from this one, where you can provide your solution.
    2. [Create a **new branch**](https://help.github.com/articles/creating-and-deleting-branches-within-your-repository/) from `master`; you may call it as your `username`.
    3. [**Clone**](http://gitref.org/creating/#clone) the repository to your computer.
    4. Fill in the **gaps** and [**commit**](http://gitref.org/basic/#commit) changes to complete your solution.
    5. [**Push**](http://gitref.org/remotes/#push)/sync the changes up to GitHub.
    6. [Create a **pull request**](https://help.github.com/articles/creating-a-pull-request) with `master` as _base branch_ and `username` as _compare branch_.
    7. Within the pull request, [**mention** any teacher](https://help.github.com/articles/basic-writing-and-formatting-syntax/#mentioning-users-and-teams) to turn in the assignment.

- **Teachers** are required to follow these instructions:
    1. Once created, you can then do [**code review**](https://help.github.com/articles/about-pull-request-reviews) with line-by-line feedback directly within the pull request.
    2. Within the pull request, try to provide also a brief summary touching general remarks such as:
        - Did the student **comment** the code sufficiently?
        - Is the code **cross-compilable**?
        - To which **extent** are the requirements accomplished?
        - ...

## Reviewing Notes for Teachers
In case the student **did not create a new branch**, you can still launch a PR based review by doing:

1. Identify within the student repository the **last commit of the starter code**.
2. Create a **new branch** from that commit; call it `review-teacherusername`.
3. Start a **pull request** with `review-teachernusername` as _base branch_ and `master` as _compare branch_.
4. You can now do **code review** as explained above.
