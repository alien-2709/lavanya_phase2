# 1. GDB BABY STEP 1

> Can you figure out what is in the eax register at the end of the main function? Put your answer in the picoCTF flag format: picoCTF{n} where n is the contents of the eax register in the decimal number base. If the answer was 0x11 your flag would be picoCTF{17}.
Disassemble this.

## Solution:

- I tried using `strings -a debugger0_a` to find any readable characters in the file but didn't obtain anything worthwhile.

```
$ strings -a debugger0_a
/lib64/ld-linux-x86-64.so.2
libc.so.6
__cxa_finalize
__libc_start_main
GLIBC_2.2.5
_ITM_deregisterTMCloneTable
__gmon_start__
_ITM_registerTMCloneTable
u+UH
[]A\A]A^A_
:*3$"
GCC: (Ubuntu 9.4.0-1ubuntu1~20.04.1) 9.4.0
crtstuff.c
deregister_tm_clones
__do_global_dtors_aux
completed.8061
__do_global_dtors_aux_fini_array_entry
frame_dummy
__frame_dummy_init_array_entry
debugger0_a.c
__FRAME_END__
__init_array_end
_DYNAMIC
__init_array_start
__GNU_EH_FRAME_HDR
_GLOBAL_OFFSET_TABLE_
__libc_csu_fini
_ITM_deregisterTMCloneTable
_edata
__libc_start_main@@GLIBC_2.2.5
__data_start
__gmon_start__
__dso_handle
_IO_stdin_used
__libc_csu_init
__bss_start
main
__TMC_END__
_ITM_registerTMCloneTable
__cxa_finalize@@GLIBC_2.2.5
.symtab
.strtab
.shstrtab
.interp
.note.gnu.property
.note.gnu.build-id
.note.ABI-tag
.gnu.hash
.dynsym
.dynstr
.gnu.version
.gnu.version_r
.rela.dyn
.init
.plt
.plt.got
.text
.fini
.rodata
.eh_frame_hdr
.eh_frame
.init_array
.fini_array
.dynamic
.data
.bss
.comment
```
- Next , I found out what type of file it was using `file ./debugger0_a` and found out it was an ELF file.
```
$ file ./debugger0_a
./debugger0_a: ELF 64-bit LSB pie executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2,
BuildID[sha1]=15a10290db2cd2ec0c123cf80b88ed7d7f5cf9ff, for GNU/Linux 3.2.0, not stripped
```
- As it is an ELF file , I used `objdump -d ./debugger0_a|grep -n 'eax'` where objdump is a tool used to disassemble binaries such as ELF and -d is used to disassemble the executable sections of the binary file and show all the human readable part.
- `grep -n 'eax'` is used to find the string  `eax` in the file.
```
$ objdump -d ./debugger0_a|grep -n 'eax'
109:    1138:   b8 42 63 08 00          mov    $0x86342,%eax
``` 
- From this we obtained `0x86342` in the eax register so we convert it into decimal which is `549698` and thus obtain the flag.
## Flag:
```
picoCTF{549698}
```

## Concepts learnt:

- I learnt about `objdump -d` to disassemble binary files like ELF.



## Resources:

-  (https://unix.stackexchange.com/questions/343013/how-objdump-disassemble-elf-binary)


# 2.ARMssembly 1
>For what argument does this program print `win` with variables 87, 3 and 3? File: chall_1.S Flag format: picoCTF{XXXXXXXX} -> (hex, lowercase, no 0x, and 32 bits. ex. 5614267 would be picoCTF{0055aabb})

## Solution:

- I catted out the file in the terminal to know what it contains using `cat chall_1.S` and found out it was all assembly code that I wasn't able to understand.
```
$ cat chall_1.S
        .arch armv8-a
        .file   "chall_1.c"
        .text
        .align  2
        .global func
        .type   func, %function
func:
        sub     sp, sp, #32
        str     w0, [sp, 12]
        mov     w0, 87
        str     w0, [sp, 16]
        mov     w0, 3
        str     w0, [sp, 20]
        mov     w0, 3
        str     w0, [sp, 24]
        ldr     w0, [sp, 20]
        ldr     w1, [sp, 16]
        lsl     w0, w1, w0
        str     w0, [sp, 28]
        ldr     w1, [sp, 28]
        ldr     w0, [sp, 24]
        sdiv    w0, w1, w0
        str     w0, [sp, 28]
        ldr     w1, [sp, 28]
        ldr     w0, [sp, 12]
        sub     w0, w1, w0
        str     w0, [sp, 28]
        ldr     w0, [sp, 28]
        add     sp, sp, 32
        ret
        .size   func, .-func
        .section        .rodata
        .align  3
.LC0:
        .string "You win!"
        .align  3
.LC1:
        .string "You Lose :("
        .text
        .align  2
        .global main
        .type   main, %function
main:
        stp     x29, x30, [sp, -48]!
        add     x29, sp, 0
        str     w0, [x29, 28]
        str     x1, [x29, 16]
        ldr     x0, [x29, 16]
        add     x0, x0, 8
        ldr     x0, [x0]
        bl      atoi
        str     w0, [x29, 44]
        ldr     w0, [x29, 44]
        bl      func
        cmp     w0, 0
        bne     .L4
        adrp    x0, .LC0
        add     x0, x0, :lo12:.LC0
        bl      puts
        b       .L6
.L4:
        adrp    x0, .LC1
        add     x0, x0, :lo12:.LC1
        bl      puts
.L6:
        nop
        ldp     x29, x30, [sp], 48
        ret
        .size   main, .-main
        .ident  "GCC: (Ubuntu/Linaro 7.5.0-3ubuntu1~18.04) 7.5.0"
        .section        .note.GNU-stack,"",@progbits
```
- Since I wasn't able to understand it , I thought about converting it to C code would make it easier to understand . Thus , I used an online converter to do so:(https://www.codeconvert.ai/assembly-to-c-converter?id=c7b7bc59-335c-4c4f-ab05-406f033b6952)
- This is what I obtained:
```
#include <stdio.h>
#include <stdlib.h>

int func(int input) {
    int a = input;
    int b = 87;
    int c = 3;
    int d = 3;

    int temp = b << c;
    temp = temp / d;
    temp = temp - a;

    return temp;
}

int main(int argc, char *argv[]) {
    int val = atoi(argv[1]);
    int result = func(val);

    if (result == 0) {
        puts("You win!");
    } else {
        puts("You Lose :(");
    }

    return 0;
}
```
- From this , I found out the code algorithm , how it takes input 87,3 and 3 and goes through three processes :
- 1. 87<<3 which is a left shift by 3 so we multiply 87 * 2^3 and obtain 696.
  2. 696/3=232
  3. 232-input.
- Now if result(232-input)=0, it will print "You win", otherwise it will print "You lose".
- Thus, the only argument for which "You win" will be printed is `input=232`.
- 232 in hex, lowercase,no x0,32 bits is `000000e8`
## Flag:

```
picoCTF{000000e8}
```


