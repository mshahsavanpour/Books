## What is an API, and What Does It Do?
An API (Application Programming Interface) is just a set of functions that programs can use to interact with the system. In the context of operating systems, the API comprises system calls. But what's a system call? It's like a function call, but it comes with special powers. It runs at a higher privilege level in the OS, giving it access to sensitive resources like disk memory or CPU, which regular functions can’t touch.

For instance, if your program wants to read data from a file on the disk, the OS will make that happen through a system call, not a regular function. System calls are sometimes blocked, meaning that while your program is waiting for the OS to complete the task (like reading from a disk), the process is temporarily stopped, or "blocked." The OS will then pull your process off the CPU until the task is done.

## Do You Need to Rewrite Your Programs for Every OS?
Different operating systems may have slightly different sets of system calls, but thankfully, you don’t have to rewrite your entire program for every OS. This is largely because of the POSIX API. POSIX is a standardized set of system calls that all major operating systems (like Linux and Windows) support. So, if you write your program using POSIX-compliant calls, it should run on any system that supports POSIX without major changes.

Most of the time, you won’t even deal with system calls directly. Libraries (like the C library) usually provide high-level functions that call the system calls for you. For example, if you want to write something to the screen, instead of calling the system call directly, you’d use a high-level function like printf(), which handles the system call in the background. This way, you don’t need to worry about how the system call works.

### Why Use System Calls?

System calls are your program's way of saying, "Hey, OS, I need you to handle this for me!" Your programs run in a safer environment (called user mode) where they can’t directly access hardware or control system resources. System calls give them the green light to switch to kernel mode, where the OS takes over and does the heavy lifting.

Here’s why system calls are important:

- **Security**: They ensure that only the operating system directly interacts with the hardware, protecting your system from potential damage or malicious activity.
- **Resource management**: The OS is in charge of handing out resources like memory, CPU time, and access to I/O devices, so it keeps things fair and efficient.
- **Portability**: APIs like POSIX make it easier for your programs to work on different systems without needing major changes—making your life as a developer much easier!


### Key Process-Related System Calls

1. **fork()**  
   The `fork()` system call creates a new process by duplicating the calling process. The new process, known as the "child," shares much of its state with the parent but has its own address space. The child can run independently of the parent, making `fork()` the foundation for creating concurrent or parallel programs.

   - **Copy-on-write (COW)**: When `fork()` is called, both parent and child share the same memory pages until one of them modifies a page. This optimizes memory usage by delaying the actual copying of data until necessary.
   - **Process control block (PCB)**: The child process gets its own PCB, containing unique attributes like process ID (PID), file descriptors, and scheduling information.

   Example (from p1.c):
   ```c
   int rc = fork();
   if (rc == 0) {
       // Child process
       printf("Child process (pid:%d)\n", getpid());
   } else {
       // Parent process
       printf("Parent process (pid:%d)\n", getpid());
   }
   ```

   - **Non-deterministic behavior**: The CPU scheduler determines which process runs first, so the parent and child may execute in different orders each time `fork()` is called.
   - **Resource sharing**: Although the child gets its copy of resources, some, like open file descriptors, are shared between parent and child, leading to potential race conditions if not handled carefully.

2. **wait()**  
   The `wait()` system call makes the parent process wait until one or more child processes have completed execution. This is especially useful in ensuring that a parent doesn’t terminate before its child or when it needs to synchronize the state with its child processes.

   **Variants**:
   - `waitpid()`: Provides more control, allowing the parent to wait for a specific child by its PID, and can be used in a non-blocking mode to poll the child’s status.

   Example (from p2.c):
   ```c
   int rc_wait = wait(NULL);
   printf("Parent waited for child (pid:%d)\n", rc_wait);
   ```

   - **Zombie processes**: When a child terminates but the parent doesn’t call `wait()`, the child becomes a zombie, occupying a slot in the process table until reaped by its parent.
   - **Orphan processes**: If a parent terminates before a child, the child is adopted by the `init` process (PID 1), which later reaps it.

3. **exec()**  
   The `exec()` family of system calls replaces the current process image with a new program. This allows a process to start execution of a different program without needing to create a new process. The key feature of `exec()` is that it doesn’t return unless there’s an error—once a new program is loaded, the original process image is discarded.

   **Variants**:
   - `execvp()`, `execlp()`, `execv()`, and more: These variants allow passing arguments and environment variables to the new program.

   Example (from p3.c):
   ```c
   char *args[] = {"ls", "-l", NULL};
   execvp(args[0], args);  // Run the "ls -l" command
   ```

   **Why Separate fork() and exec()?**:
   - This separation gives the parent process a chance to modify the child’s environment before executing the new program. For example, the parent can redirect I/O or set specific environment variables before calling `exec()`.

4. **exit()**  
   The `exit()` system call allows a process to terminate and return an exit status code to the operating system. This status can be retrieved by the parent process using `wait()`. The `exit()` call performs several housekeeping tasks, such as closing open file descriptors and releasing memory, before the process is removed from the system.

   Example:
   ```c
   exit(0);  // Successful termination
   ```

### Additional System Calls and Features

- **kill()**: Used to send signals to processes, such as SIGKILL (terminate) or SIGSTOP (pause). Signals are a fundamental way to communicate with and control processes.
  ```bash
  kill -9 <pid>  # Forcefully terminate a process
  ```

- **pipe()**: Provides a unidirectional communication channel between two processes. It’s commonly used in conjunction with `fork()` and `exec()` to pass data from one process to another.

  Example:
  ```c
  int fds[2];
  pipe(fds);
  ```

### Process Control in UNIX

In a multi-user system, process control is essential for resource management, security, and user experience. Processes are often managed by signals, allowing the OS or users to pause, resume, or terminate processes. Processes are also managed by user permissions, ensuring that regular users can only control processes they own, while superusers have full control over the system.

#### Signals in UNIX

Signals are asynchronous notifications sent to a process to notify it of an event, such as termination requests (SIGTERM) or interruptions (SIGINT). Programs can handle signals using the `signal()` system call to define custom signal handlers, allowing them to intercept and manage signals like termination or pause requests.

For instance, pressing `Ctrl + C` in a terminal sends a SIGINT signal, which typically terminates the running process.

### Case Study: How a Shell Works

In a typical operating system, the shell plays a vital role as a command interpreter. When a user types a command, the shell reads the input, forks a child process, and uses `exec()` to run the command. The parent shell process waits for the child to finish before presenting the user with another prompt. Common shell commands like `ls`, `grep`, and `cat` are executed this way.

```bash
prompt> ls > output.txt
```

In this example:
1. The shell forks a child process.
2. The child redirects its standard output to a file (`output.txt`).
3. The child calls `exec()` to replace its image with the `ls` command.
4. The parent waits for the child to complete.

This mechanism also enables more advanced features, such as pipes and I/O redirection:
```bash
prompt> cat file.txt | grep "word"
```

This command runs `cat` to output the contents of `file.txt` and pipes the output to `grep`, which searches for occurrences of "word."

### Practical Tools for Process Management

- **ps**: Displays a list of running processes along with information like PID, CPU usage, and memory usage.
  ```bash
  ps aux  # Display all running processes
  ```

- **top**: Provides a real-time view of system resource usage, showing which processes consume the most CPU or memory.
  ```bash
  top  # Display system resource usage in real-time
  ```

- **killall**: Sends a signal to all processes matching a given name.
  ```bash
  killall firefox  # Kill all Firefox processes
  ```

---

System calls like `fork()`, `exec()`, `wait()`, and `exit()` are the building blocks of process management in UNIX-like systems. By combining these calls, operating systems provide powerful mechanisms to create, control, and terminate processes. Shells utilize these mechanisms to manage user commands, supporting features like I/O redirection and pipes. Mastery of these system calls is essential for developing software that interacts deeply with the operating system and efficiently manages resources.
