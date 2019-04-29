# OS161_CodeReading01
OS/161 Code Reading, Traps, Interrupts, System Calls, and Debugging Practice

**Questions**:

1. Traps and interrupts are mechanisms used to transfer control between user processes and the operating system. Tell us where we can find the first line of OS/161 code that is executed when a trap occurs. Then tell us where control gets transferred to from that point (i.e., what function services the trap). Be sure to describe the control flow for each type of trap that may occur (e.g., system calls, VM faults, and hardware interrupts).

> Answer: 

These data contain kern, archs, locore, mips and exception-mips. S has handling codes. But, there is an exception of it, for instance, the code has UTLB exception. Thus, the first code going to be 69. The first line will be 87 in case of a general exception. Code lines 69 and 87 commonly have simple j common_exception. Typically, Common_exception creates a typeframe which gets moved to mips_trap located in kern/arch/,mips/locore/trap.c. From that, directly, syscalls will be sent to syscall function located in kern/arch/mips/syscall.c. VM faults will go directly or else will be directed to vm_fault, that is now kern/arch/mips/vm/dumbvm.c.Typically, interrupts are directed to mainbus_interrupt that is kern/arch/sys161/dev/lamebus_machdep.c. There are interprocessor interrupts, which are carried to interprocessor_interrupt that’s located in the kern/thread/clock.c. Primarily, the interrupts are handled outside arch directory. When there is an invalid TLB entries or any other error Panic occurs. 
 




2. Where is the first line of code for constructing a trapframe? Describe at a high level what the code is doing here.

> Answer: 

The trapefram is a composed code that is kern/arch/sys161/conf/conf.arch. More often, the code is relevant to that of mips/conf/conf.arch. Counting 37 uint32_ts inside of the kern/arch/mips/include/trapeframe.h. a total of 148 bytes with approximately 4 bytes each, which is trapframe size as a result of its requirement to store around 37 registers as general purpose for instance, cause and EPC registers. 




3. Each OS/161 exception has its own code. What are the exception codes for the exceptions: **interrupt**, **system call**, and **arithmetic overflow**?

> Answer: 
 Zero exception code for Interrupt, system call has 8 exception code, and arithmetic overflow has 12 exception code.
system call: src/kern/startup/main.c.
Interrupt: public InterruptedException()





4. What information is saved in **struct trapframe**?

> Answer: 

The information set is context that allows continuing the execution of a task precisely at where it was stopped or was left. The trapframe struct has uint edi. It is the register for the string operations. It contains esi, which is the string operations source index and ebx that base index. Thus, registers and context are contained in trapframe struct. 



5. What is the name of the function that is the system-call handler in OS/161? In which directory is this function implementation located?

> Answer: 
 In the OS/161, system call handler is named handler syscall.c that is found in os161/src/kern/arch/mips/mips




6. The kernel's main function is to provide support for user-level programs. Most such support is accessed via "system calls”. For example, consider the system call **reboot()**, which is implemented in the function **sys_reboot()** in *src/kern/main/main.c*. Using GDB, put a breakpoint on **sys_reboot** and run the "reboot" program (by typing "p /sbin/reboot" at the OS/161 menu prompt). Use "backtrace" to see how it got there. **Show the output of your debugging session as well as the output printed by the OS/161.**



> Answer: 

An exclusive identifier number is for every system call. In OS/161, those numbers are typically defined within kern/include/kern/syscall.h:
#define sys_reboot
The syscall reg are placed by the library associated with the reboot(). number in register (v0) and in turn issues trap to the OS. By assembly language exceptions, syscall handler gets the data structure ‘’trapframe’’ that has the system number and other information. In a switch case statement, system call number is used to select the function
void syscall(struct trapframe *tf)
...
switch (callno) {
case sys_reboot:
err = sys_reboot(tf->tf_a0);
break;

