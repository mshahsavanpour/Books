# CPU Scheduling Policies

The concept of CPU scheduling is one of the most important concepts in the operating system. CPU scheduling enables different processes to share the same CPU; it determines the order in which the CPU executes the processes. Efficient CPU scheduling enhances many performance metrics, including CPU utilization, throughput, turnaround time, response time, and fairness.

# 1. Overview of Scheduling Policies

## 1.1 First-In-First-Out

First-In-First-Out, or First-Come, First-Served, simply schedules the processes in the order they arrive. A process continues to run until it completes before the beginning of the next process.

**Pros:**
- Easy to implement.

**Cons:**
- Susceptible to "convoy effect," wherein a short process gets behind long processes thus yielding high average turnaround time.

<p align="center">
    <img src="image/Scheduling/fifo.png"/>
</p>


**Example:**
- Assume there are three processes A, B and C which arrives at the same time. Process A requires 100ms whereas B and C requires 10ms each. B and C will have to wait until the process A terminates hence, increasing their turnaround time.

## 1.2 Shortest Job First (SJF)
SJF always chooses the process with the shortest execution time. It is a nonpreemptive scheduling algorithm; once a process has been started, it runs to completion.

**Pros:**
- Ensures minimum average waiting time for jobs arriving together.

**Cons:**
- Shorter processes will be waiting behind longer ones if the scheduler is unable to preempt.
- In practice, knowing the process burst times usually is impracticable.

<p align="center">
    <img src="image/Scheduling/sjf_1.png"/>
</p>

<p align="center">
    <img src="image/Scheduling/sjf_2.png"/>
</p>

**Example:**
In the system where A, B and C have the burst times of 100ms, 10ms, and 10ms, SJF will schedule B and C before A. That will decrease the average time from start to finish .

## 1.3 Shortest Time-to-Completion First (STCF)
Another name is Shortest Remaining Time First (SRTF). STCF is the preemptive version of SJF. If there is a new arrival of the process and its remaining time is less than the current process's, STCF preempts the current process.

**Advantages:**
- Best for reducing average turn around time when all the processes arrive at different times.
**Drawbacks:**
- Needs to know the exact remaining time, which is not possible practically.

**Example:**
- Suppose that A enters at 0 ms and has a burst period of 100 ms. B and C enter after 10 ms each with burst time 10 ms; STCF will preempt A and shall allow B and C to finish so that on an average, turnaround time reduces .

<p align="center">
    <img src="image/Scheduling/stcf.png"/>
</p>

## 1.4 Round Robin (RR)
In Round Robin scheduling, each process will be assigned a fixed time quantum. If a process fails to finish its execution in its given quantum, then it will be preempted and sent to the rear for its next turn.

**Advantages:**

• Suitable for time-sharing systems. It ensures responsive performance for interactive users

**Disadvantages:**

• High Turnaround Time for a process with short burst times because of more frequent context switching.

<p align="center">
    <img src="image/Scheduling/rr.png"/>
</p>

**Example:**
- For three processes A, B, and C all requiring 5 seconds, and given a time quantum of 1 second, RR will cycle through A, B, and C continuously, yielding an average response time of 1 second but stretching overall turnaround.

## 1.5 Multi-Level Feedback Queue (MLFQ)
MLFQ changes the priority of the processes dynamically based on their behavior. It uses multiple queues for different priority levels and time quantums.

**Basic Rules:**
1. If a process uses up its time slice in a queue, it moves down to a lower-priority queue.
2. Processes that come out of the CPU before their time slice expires-for instance, due to an I/O request-maintain their priority.

**Advantages:**
- It combines the merits of SJF and RR; good interactive responses and CPU-bound processes still have good efficiency.

**Limitations:**
- Parameters such as time slice length, number of queues, and rules for priority adjustment are sensitive to tuning.

**Example:**
- If an interactive job and a long-running job arrive, the interactive job is given a high priority that ensures quick response while, as the long job moves down in priority, it prevents starvation of interactive tasks.

# 2. Scheduling Metrics

## 2.1 Turnaround Time:
The total time taken for a process from arrival to completion. Lower turnaround time is generally desirable.

## 2.2 Response Time:
The time between the arrival of a process and its first scheduling. This is an important performance measure for interactive systems where quick responses to user inputs are expected.

## 2.3 CPU Utilization:
The percentage of time the CPU spends in the actual execution of processes. The higher the CPU utilization, the better the system performance.

## 2.4 Fairness:
Each process gets a fair share of the CPU. No single process is starved of CPU.

# 3. Multi-Level Feedback Queue (MLFQ)

## 3.1 Overview

The Multi-Level Feedback Queue (MLFQ) scheduler dynamically adjusts the priority of processes based on their behavior and execution history. It was first introduced by Corbato et al. in 1962 as part of the Compatible Time-Sharing System (CTSS) and has since evolved through various implementations in modern operating systems.

## 3.2 Objectives

MLFQ aims to optimize two main criteria:
1. **Turnaround Time**: Prioritize short jobs, akin to Shortest Job First (SJF).
2. **Response Time**: Make the system responsive to interactive processes.

## 3.3 Basic Rules and Mechanism

MLFQ consists of several priority queues, where each queue represents a different priority level. The scheduler follows these rules:

1. **Rule 1**: If Priority(A) > Priority(B), process A runs before process B.
2. **Rule 2**: If Priority(A) = Priority(B), both run in a round-robin fashion using the time slice of the queue.

The priority of a process is not fixed. MLFQ modifies the priority based on the process's behavior:

- **Interactive Processes**: Processes frequently relinquishing the CPU (e.g., waiting for I/O) maintain a high priority.
- **CPU-bound Processes**: Processes using the CPU for extended periods are moved to lower priority queues.

## 3.4 Priority Adjustments

The priority of a process changes based on the following rules:

1. **Rule 3**: A new job is placed at the highest priority (top queue).
2. **Rule 4a**: If a job uses up its time slice, its priority is lowered (moved down one queue).
3. **Rule 4b**: If a job relinquishes the CPU before using its time slice (e.g., for I/O), it remains at the same priority.

## 3.5 Addressing Starvation and Fairness

MLFQ incorporates mechanisms to prevent starvation and ensure fair CPU allocation:

1. **Priority Boost**: All processes are periodically moved to the highest priority queue to prevent starvation (Rule 5).
2. **Better Accounting**: To prevent gaming the scheduler, MLFQ tracks total CPU time used, ensuring that processes are demoted even if they relinquish the CPU frequently.

## 3.6 Tuning Parameters

- **Number of Queues**: Determines the granularity of priority levels.
- **Time Slice**: Duration a process runs before being preempted.
- **Priority Boost Interval**: Frequency at which all processes are boosted to the top queue.

The choice of these parameters significantly impacts the performance and fairness of the scheduling.


# 4. Lottery Scheduling

## 4.1 Overview

Lottery Scheduling is a proportional-share scheduling algorithm that provides each process with a probability of running proportional to the number of tickets it holds. It was introduced by Waldspurger and Weihl in 1994.

## 4.2 Basic Concept

Each process is allocated a number of tickets representing its share of the CPU. A lottery is held at each scheduling decision point, and the process holding the winning ticket is selected to run. This probabilistic approach aims to approximate fair resource distribution over time.

## 4.3 Ticket Mechanisms

- **Ticket Assignment**: Processes receive tickets proportional to their required CPU share.
- **Ticket Currency**: Users can allocate tickets within their own processes, which are converted to global values for fair distribution.
- **Ticket Transfer**: Allows processes to transfer their tickets to other processes, useful in client-server models.
- **Ticket Inflation**: A process can temporarily increase its tickets, reflecting a higher need for CPU time.

## 4.4 Implementation

Lottery Scheduling requires a random number generator and a data structure to track processes and their tickets. The scheduler draws a random number within the range of total tickets, and the process corresponding to the winning ticket runs.

## 4.5 Stride Scheduling

Stride Scheduling is a deterministic alternative to Lottery Scheduling. It uses "strides," inversely proportional to the number of tickets, to schedule processes precisely according to their share of the CPU.

## 4.6 Use Cases and Limitations

Lottery Scheduling is ideal for systems where dynamic and proportional resource allocation is required. However, it can be less predictable than deterministic schedulers like MLFQ or Stride Scheduling, especially over short periods.

---

# 5. Comparison of MLFQ and Lottery Scheduling

| **Feature**                   | **MLFQ**                                                  | **Lottery Scheduling**                             |
|-------------------------------|----------------------------------------------------------|---------------------------------------------------|
| **Scheduling Type**           | Priority-based, dynamic adjustment                       | Proportional-share, probabilistic                 |
| **Main Objective**            | Optimize turnaround and response time                    | Fair CPU distribution proportional to ticket share|
| **Adaptability**              | Adjusts based on process behavior                        | Allocates based on assigned tickets               |
| **Fairness**                  | Fair to both interactive and CPU-bound processes         | Fair in the long run but not guaranteed in short term |
| **Implementation Complexity** | Requires multiple queues and rules                       | Requires random number generator and ticket management|
| **Use Cases**                 | General-purpose systems, interactive workloads           | Virtualized environments, controlled resource allocation|