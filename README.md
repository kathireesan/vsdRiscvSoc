# INSTALLATION OF RISC-V TOOLCHAIN
## UNPACK OF TOOLCHAIN
<pre> ```tar -xvzf riscv-toolchain-rv32imac-x86_64-ubuntu.tar.gz ``` </pre>
this will extract the tar file
## ADD TOOLCHAIN TO THE PATH
<pre> ```export PATH=/opt/riscv/bin:$PATH``` </pre>
<pre> ```source ~/.bashrc``` </pre>
In the path paste your directory path 

| Register Number | ABI Name | Role / Description               | Calling Convention Role                |
|-----------------|----------|--------------------------------|---------------------------------------|
| x0              | zero     | Hardwired zero (always 0)       | —                                     |
| x1              | ra       | Return address                  | Callee saves return address           |
| x2              | sp       | Stack pointer                  | Points to the current stack frame     |
| x3              | gp       | Global pointer                 | Points to global data                  |
| x4              | tp       | Thread pointer                 | Thread local storage pointer          |
| x5              | t0       | Temporary / caller-saved       | Caller-saved temporary register       |
| x6              | t1       | Temporary / caller-saved       | Caller-saved temporary register       |
| x7              | t2       | Temporary / caller-saved       | Caller-saved temporary register       |
| x8              | s0 / fp   | Saved register / frame pointer | Callee-saved, also frame pointer      |
| x9              | s1       | Saved register                 | Callee-saved                         |
| x10             | a0       | Function argument 0 / return value 0 | Used to pass argument 0, return val   |
| x11             | a1       | Function argument 1 / return value 1 | Used to pass argument 1, return val   |
| x12             | a2       | Function argument 2             | Arguments                            |
| x13             | a3       | Function argument 3             | Arguments                            |
| x14             | a4       | Function argument 4             | Arguments                            |
| x15             | a5       | Function argument 5             | Arguments                            |
| x16             | a6       | Function argument 6             | Arguments                            |
| x17             | a7       | Function argument 7             | Arguments                            |
| x18             | s2       | Saved register                 | Callee-saved                        |
| x19             | s3       | Saved register                 | Callee-saved                        |
| x20             | s4       | Saved register                 | Callee-saved                        |
| x21             | s5       | Saved register                 | Callee-saved                        |
| x22             | s6       | Saved register                 | Callee-saved                        |
| x23             | s7       | Saved register                 | Callee-saved                        |
| x24             | s8       | Saved register                 | Callee-saved                        |
| x25             | s9       | Saved register                 | Callee-saved                        |
| x26             | s10      | Saved register                 | Callee-saved                        |
| x27             | s11      | Saved register                 | Callee-saved                        |
| x28             | t3       | Temporary / caller-saved       | Caller-saved                       |
| x29             | t4       | Temporary / caller-saved       | Caller-saved                       |
| x30             | t5       | Temporary / caller-saved       | Caller-saved                       |
| x31             | t6       | Temporary / caller-saved       | Caller-saved                       |


Screenshot from 2025-06-08 15-28-49.png
