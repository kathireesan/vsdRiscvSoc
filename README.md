# Week 1: RISC-V Toolchain Setup and Experiments

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

---

## 3. From C to Assembly

**Generate `.s` file:**
```bash
riscv32-unknown-elf-gcc -S -O0 hello.c
```

**Assembly Explanation:**  
Prologue and epilogue include stack manipulation like:
```asm
addi sp, sp, -16
sw ra, 12(sp)
```

---

## 4. Hex Dump & Disassembly

```bash
riscv32-unknown-elf-objdump -d hello.elf > hello.dump
riscv32-unknown-elf-objcopy -O ihex hello.elf hello.hex
```

Discuss instruction format: address, opcode, mnemonic, operands.

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

---

## 8. Exploring GCC Optimisation

Compile with both flags:
```bash
riscv32-unknown-elf-gcc -S -O0 hello.c -o hello_O0.s
riscv32-unknown-elf-gcc -S -O2 hello.c -o hello_O2.s
```

Compare for: dead-code elimination, register allocation, inlining.

---

## 9. Inline Assembly Basics

```c
static inline uint32_t rdcycle(void) {
    uint32_t c;
    asm volatile ("csrr %0, cycle" : "=r"(c));
    return c;
}
```

---

## 10. Memory-Mapped I/O Demo

```c
volatile uint32_t *gpio = (uint32_t*)0x10012000;
*gpio = 0x1;
```

---

## 11. Linker Script 101

```ld
SECTIONS {
    .text 0x00000000 : { *(.text*) }
    .data 0x10000000 : { *(.data*) }
}
```

---

## 12. Start-up Code & crt0

- Sets up `sp`, clears `.bss`, calls `main()`, then infinite loop.
- Refer to newlib or platform startup files.

---

## 13. Interrupt Primer

- Configure `mtimecmp`, enable `mie`, and `mstatus`.

```c
__attribute__((interrupt)) void timer_isr(void) {
    // Timer interrupt handler
}
```

---

## 14. rv32imac vs rv32imc – What’s the “A”?

- Adds: `lr.w`, `sc.w`, `amoadd.w`, etc.
- Use case: atomic locks, OS-level primitives.

---

## 15. Atomic Test Program

Use `lr/sc` to implement a mutex:
```c
while (1) {
    if (__sync_lock_test_and_set(&lock, 1) == 0) break;
}
```

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

---

## 📸 Screenshot

> 🖼️ **Paste your screenshot here:**  
> ![Screenshot](screenshot.png)

---
