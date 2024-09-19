# Virtualization, Virtual Machines, and Hypervisors
# 1. Definitions

## 1.1 Virtualization

Virtualization is an application of the **layering principle**, enforcing modularity. The virtual resource exposed acts just like the physical resource underlying it being virtualized. Virtualization is a technique for abstracting the compute environment, comprising the CPU, memory, and I/O components, in such a manner that the system would not realize any discrepancy between the virtual and physical resources.

## 1.2 Virtual Machines

A Virtual Machine is a **completely encapsulated compute environment** decoupled from the actual physical hardware by a layer of abstraction provided by the virtualization of the processor, memory, and I/O subsystems. Virtual machines operate their own operating systems and applications in isolation to one another.

## 1.3 Hypervisor

A hypervisor is a specialized system software responsible for managing and running multiple virtual machines on a single physical system. It consists of two fundamental parts:

- **Virtual Machine Monitor (VMM)**: It carries out the virtualization of CPU and memory.
- **Hypervisor**: It will provide the management of overall system resources and virtual machine scheduling at a broader scale.
# 2. Principles of Virtualization

There are, in general, two principles on which virtualization depends:

- **Layering**: This is the concept of abstracting physical resources by introducing an indirection.
- **Enforced Modularity**: This ensures that virtual resources are safely kept within stringent control from getting directly to the physical layer.
These principles reduce complexity, enhance operational simplicity, and let the system be flexible.

3. Types of Virtualization

Virtualization is not limited to virtual machines; it is a general paradigm that permeates modern computer architecture, operating systems, and I/O subsystems.

Computer Architecture: Virtual memory employs memory management units (MMUs) to abstract the physical memory into virtual memory. Depending on this, multiple mappings of physical memory are possible, and the system functions transparently with these multiple mappings.

Operating Systems: The OS provides these abstracted CPU, memory, and I/O resources to multiple applications running parallel through virtualization.
- I/O Subsystems: RAID and SSD controllers present operating systems with abstractions of a virtual disk. This allows I/O operations in one big block, irrespective of the physical configuration of the disks.
# 4. Techniques of Virtualization

There are three major techniques in use to implement virtualization:

- Multiplexing: where a physical resource is shared among multiple virtual entities, either in space-partitioning or in time-scheduling
- Aggregation: where multiple physical resources are combined into a single virtual abstraction. An example is RAID arrays where multiple disks are combined into one.
Emulation: Software representation of a hardware resource; the abstraction that is virtualized acts just like physical hardware, even if the underlying physical device is actually different.
# 5. Virtual Machines and Classifications

There are primarily three kinds of virtual machines:

Language-Based Virtual Machines: Examples include JVM and CLR that are a runtime for executing application-level code.
Lightweight Virtual Machines: virtual environments based on OS-level isolation, like Docker containers and FreeBSD Jails, which provide a secure sandbox for applications.
System-Level Virtual Machines: these VMs virtualize an entire system; install the operating system independently on virtual hardware. The hypervisor controls such virtual environments to offer isolation but shares allocated resources.
# 6. Hypervisors

A **hypervisor** is the system software used in creating, managing, and running virtual machines on a physical server. It provides for efficient VM execution by controlling system resources like CPU, memory, and I/O.

## 6.1 Hypervisor Types

Hypervisors can be categorized into two types:

- **Type-1 (Bare-Metal)**: The hypervisor runs directly on the physical hardware and controls all system resources like VMware ESXi and Microsoft Hyper-V.
Type-2 (Hosted): The hypervisor runs on top of an already running operating system; it shares resource management with the host OS. Examples include VMware Workstation and Oracle VirtualBox.
# 7. Hypervisor Virtualization Techniques

Hypervisors use **multiplexing** and **emulation** for virtual machine handling:

Multiplexing: The physical resources like CPU, memory are shared among the Virtual Machines using scheduling.
- **Emulation**: Software emulates the hardware element like any I/O device. This lets each virtual machine run without any regard to the underlying physical hardware configuration.
# 8. Approaches to Virtualization

There are several approaches followed in virtualization systems:

- **Full (Software) Virtualization**: This form of virtualization can run unmodified guest operating systems simply by emulating hardware resources .
- **Hardware Virtualization (HVM)**: Hardware features are used for efficient virtualization and thus allows for the direct execution of VM instructions on the physical CPU.
- **Paravirtualization**: Requires guest operating system modifications for improved performance on hardware without complete virtualization support.
# 9. Virtualization Benefits

Virtualization offers several advantages in today's IT systems:

Diverse Operating System: Multiples of OS can be run on one machine; this enhances flexibility during the development and testing stages of software.

Server Consolidation: Virtual machines allow the consolidation of several servers, which reduces hardware costs while enhancing resource utilization.
• **Rapid Provisioning**: Virtual machines are fast to be created and deployed, making the IT function agile.
• **Security**: Hypervisors offer an additional layer of management involved in security and monitoring without interfering with the guest operating system.
• **High Availability**: Virtual machines maximize fault tolerance due to failover and migration between physical hosts with no disruption.