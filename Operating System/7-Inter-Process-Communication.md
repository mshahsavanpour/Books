# Inter-Process Communication (IPC)

Inter-Process Communication (IPC) is the mechanism whereby processes running on a computer system communicate and coordinate with each other. Unlike threads, processes do not share the memory space. Various methods of IPC are, therefore required to enable interactions among processes either on the same machine or across different machines.

Inter-process communication in operating systems is important in enabling processes to cooperate towards the accomplishment of tasks. This paper takes a look at some common mechanisms inter-process communication has: shared memory, signals, sockets, pipes, message queues, and blocking versus non-blocking communications.

## Mechanisms of IPC
### 1. Shared Memory
Shared memory is one of the fastest forms of IPC due to the fact that multiple processes are sharing a common region of memory. Data may be directly interchanged between processes by merely reading and writing to this shared memory. However, this method requires explicit synchronization, which may lead to the risk of one process overwriting the data from another process.

**Key Characteristics:**

- Processes access the same memory segment through the shmget system call.
- Two processes share the same memory segment by specifying the same key.
- Due to lack of implicit synchronization, it is highly recommended to use other mechanisms, such as semaphores to control the access of shared memory simultaneously.

Code Example:

```c
int shmget(key_t key, int size, int shmflg);
```
**Disadvantages:**

- Synchronization risks: One process may overwrite data before other process reads it.
- It is more complex to use than other methods of IPCs, like message queues and pipes.

### 2. Signals
Signals are a type of software interrupt sent to a process by the operating system or another process. They are one of the basic inter-process communication mechanisms. Each signal has an default action, but custom handlers can be written in order to do specific things upon receiving certain signals.

**Types of Signals:**

Some signals have well-defined meanings; for instance, **SIGINT** means that you want to interrupt or kill the running process, and usually is sent when you type Ctrl+C.
Other signals are user-defined and may be used for specific communication between processes. 

**Handling of Signal:**

- The signal handler is a function or piece of code that predefines the response of the process to a particular signal.
- The default signal handlers are overridden by user-defined handlers in order to execute user-defined code upon reception of certain signals.

**Example Use Case:**

Termination of the processes upon receiving a signal regarding SIGKILL.

### 3. Sockets
Sockets allow processes to communicate with each other either over the network or on the same machine. The communications can take the form of network communications, such as TCP/UDP protocols, or local communications through Unix domain sockets.

**Type of Sockets:**

- **TCP/UDP Sockets:** These are used in communicating between two different machines over a network.
- **Unix Domain Sockets:** These are used for communication between processes on the same machine.

**How Sockets Work:**

- The processes open the sockets and then connect them, creating a channel for communication.
- Data written to one socket can be read from the other socket.
- The operating system handles the flow of data between socket buffers.

**Some Common Use Cases:**

**Networking applications** - where processes on different systems would want to communicate with each other.

**Processes on the same machine**, using networking for local communication in client-server architectures.

### 4. Pipes
The other IPC mechanism is pipes, which are mainly used in the communication of processes having a parent-child relationship. A pipe provides a channel of one-way communication where one process writes data and the other process reads it.

**Types of Pipes:**

**Unnamed Pipes(Regular pipes):** The communication takes place with the processes having a common ancestor, usually parent to child, where a child process is created using the fork().

**Named Pipes(FIFOs):** These are pipes that can also be used across unrelated processes. Communication in this case would be provided through a file-based mechanism.

**Key Characteristics:**

- Pipes are half-duplex; data flows only one way at a time.
- They are buffered in the operating system between the write and read actions.

**Issues:**
- Limited to a single direction of communication.
- Less flexible for complex applications compared to other IPC mechanisms like message queues.

### 5. Message Queues
Message queues provide a mechanism where processes can exchange discrete messages, buffered by the operating system. Message queues use a mailbox abstraction, where processes can send or receive messages asynchronously.

**How It Works:**

- The processes open a mailbox at a given location and use that mailbox for sending and receiving messages.
- The operating system does buffering and transmits between processes.

**Advantages:**

- Flexibility of communication between processes without the need for synchronization mechanisms required in shared memory.

- Processes can communicate with each other in an asynchronous way such that a sender may not necessarily wait for a receiver to take the message instantly.

**Use Cases:**

Useful in systems where the processes do tasks that require them to send and receive discrete data in well-framed formats.

## Blocking vs. Non-Blocking Communication
The various IPC mechanisms may be used in either blocking or non-blocking mode. The question of blocking versus non-blocking has a significant impact on how processes will respond when data is not available or the buffer space of the channel is full.

### Blocking Communication:

**In blocking mode**, the system call will pause until it can continue. for example, it waits for data to arrive in a socket or for space to become available in a buffer.

**Example:** A read from an empty socket will block the process until data becomes available. 

### Non-blocking Communication:

**In non-blocking mode**, the call immediately returns to the user, usually with an error code, if it cannot proceed. **Example:** A non-blocking read from a socket will immediately return with an error if no data is available instead of blocking the process. 

**Pros and Cons:**

**Blocking:** Easier to implement but may lead to inefficient use of resources, as the process can be left waiting.

**Non-Blocking:** More efficient but requires more complex error handling and flow control mechanisms.