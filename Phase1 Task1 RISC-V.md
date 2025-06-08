# Week 1: RISC-V Toolchain Setup and Experiments

---

## 1. Install & Sanity-Check the Toolchain

**Question:**  
I have downloaded `riscv-toolchain-rv32imac-x86_64-ubuntu.tar.gz`. How exactly do I unpack it, add it to PATH, and confirm the `gcc`, `objdump`, and `gdb` binaries work?

**Steps:**
```bash
tar -xzf riscv-toolchain-rv32imac-x86_64-ubuntu.tar.gz
export PATH=$HOME/riscv/bin:$PATH
```

**Add to `~/.bashrc`:**
```bash
echo 'export PATH=$HOME/riscv/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

**Verify installation:**
```bash
riscv32-unknown-elf-gcc --version
riscv32-unknown-elf-objdump --version
riscv32-unknown-elf-gdb --version
```

**📸 Screenshot:**
![image alt]
(https://github.com/kathireesan/vsdRiscvSoc/blob/4bf6ddd713943a2ab48ba684209195295fedab24/Images/Screenshot%20from%202025-06-08%2018-34-27.png)

---

## 2. Compile "Hello, RISC-V"

**Minimal Hello World in C:**
```c
#include <stdio.h>
int main() {
    printf("Hello, RISC-V!\n");
    return 0;
}
```

**Compile:**
```bash
riscv32-unknown-elf-gcc -march=rv32imc -mabi=ilp32 -o hello.elf hello.c
file hello.elf
```

**📸 Screenshot:**
![Step 2 Screenshot](images/step2.png)

---

## 3. From C to Assembly

**Generate `.s` file:**
```bash
riscv32-unknown-elf-gcc -S -O0 hello.c
```

**Prologue example:**
```asm
addi sp, sp, -16
sw ra, 12(sp)
```

**📸 Screenshot:**
![Step 3 Screenshot](images/step3.png)

---

## 4. Hex Dump & Disassembly

```bash
riscv32-unknown-elf-objdump -d hello.elf > hello.dump
riscv32-unknown-elf-objcopy -O ihex hello.elf hello.hex
```

**📸 Screenshot:**
![Step 4 Screenshot](images/step4.png)

---

## 5. ABI & Register Cheat-Sheet

| Register | ABI Name | Role              |
|----------|----------|-------------------|
| x0       | zero     | Constant zero     |
| x1       | ra       | Return address    |
| x2       | sp       | Stack pointer     |
| x3       | gp       | Global pointer    |
| x4       | tp       | Thread pointer    |
| x5-x7    | t0-t2    | Temporaries       |
| x8       | s0/fp    | Saved register/frame pointer |
| x9       | s1       | Saved register    |
| x10-x17  | a0-a7    | Function arguments/return |
| x18-x27  | s2-s11   | Saved registers   |
| x28-x31  | t3-t6    | Temporaries       |

**📸 Screenshot:**
![Step 5 Screenshot](images/step5.png)

---

## 6. Stepping with GDB

```bash
riscv32-unknown-elf-gdb hello.elf
(gdb) target sim
(gdb) break main
(gdb) run
(gdb) info reg a0
(gdb) disassemble
```

**📸 Screenshot:**
![Step 6 Screenshot](images/step6.png)

---

## 7. Running Under an Emulator

**Spike:**
```bash
spike --isa=rv32imc pk hello.elf
```

**QEMU:**
```bash
qemu-system-riscv32 -nographic -kernel hello.elf
```

**📸 Screenshot:**
![Step 7 Screenshot](images/step7.png)

---

## 8. Exploring GCC Optimisation

```bash
riscv32-unknown-elf-gcc -S -O0 hello.c -o hello_O0.s
riscv32-unknown-elf-gcc -S -O2 hello.c -o hello_O2.s
```

**📸 Screenshot:**
![Step 8 Screenshot](images/step8.png)

---

## 9. Inline Assembly Basics

```c
static inline uint32_t rdcycle(void) {
    uint32_t c;
    asm volatile ("csrr %0, cycle" : "=r"(c));
    return c;
}
```

**📸 Screenshot:**
![Step 9 Screenshot](images/step9.png)

---

## 10. Memory-Mapped I/O Demo

```c
volatile uint32_t *gpio = (uint32_t*)0x10012000;
*gpio = 0x1;
```

**📸 Screenshot:**
![Step 10 Screenshot](images/step10.png)

---

## 11. Linker Script 101

```ld
SECTIONS {
    .text 0x00000000 : { *(.text*) }
    .data 0x10000000 : { *(.data*) }
}
```

**📸 Screenshot:**
![Step 11 Screenshot](images/step11.png)

---

## 12. Start-up Code & crt0

Sets up stack pointer, clears `.bss`, calls `main()`, infinite loop. Use newlib or platform-specific start files.

**📸 Screenshot:**
![Step 12 Screenshot](images/step12.png)

---

## 13. Interrupt Primer

```c
__attribute__((interrupt)) void timer_isr(void) {
    // Timer interrupt handler
}
```

**📸 Screenshot:**
![Step 13 Screenshot](images/step13.png)

---

## 14. rv32imac vs rv32imc – What’s the “A”?

Adds atomic instructions: `lr.w`, `sc.w`, `amoadd.w`, etc. Useful for lock-free structures.

**📸 Screenshot:**
![Step 14 Screenshot](images/step14.png)

---

## 15. Atomic Test Program

```c
while (1) {
    if (__sync_lock_test_and_set(&lock, 1) == 0) break;
}
```

**📸 Screenshot:**
![Step 15 Screenshot](images/step15.png)

---

## 16. Using Newlib printf Without an OS

```c
int _write(int fd, char *buf, int len) {
    volatile char *UART_TX = (char*)0x10000000;
    for (int i = 0; i < len; i++) {
        *UART_TX = buf[i];
    }
    return len;
}
```

**📸 Screenshot:**
![Step 16 Screenshot](images/step16.png)

---

## 17. Endianness & Struct Packing

```c
union {
    uint32_t i;
    uint8_t b[4];
} u;
u.i = 0x01020304;
printf("%02x %02x %02x %02x\n", u.b[0], u.b[1], u.b[2], u.b[3]);
```

**📸 Screenshot:**
![Step 17 Screenshot](images/step17.png)

---
