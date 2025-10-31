# 1.iq test:
> let your input x = 30478191278.
wrap your answer with nite{ } for the flag.
As an example, entering x = 34359738368 gives (y0, ..., y11), so the flag would be nite{010000000011}

> <img width="500" height="700" alt="iqtest (1)" src="https://github.com/user-attachments/assets/96a19e0b-8f01-4396-949b-744200eda031" />

## Solution:
- I first converted `30478191278` to binary `1110000000001110000011110110110110` with x0=1,x1=1,x2=1,x3=0.....x35=0 and then did the gates manually.
- I obtained the (y0,y1,.....,y11) = `100010011000`
- so my flag would be nite{100010011000}

 ## Flag:
 `nite{100010011000}`

 # 2.I like logic:
 > i like logic and i like files, apparently, they have something in common, what should my next step be.
> attached is a `.sal` file.

## Solution:
- since the documents in `challenge.sal` cannot be opened directly , they must be exported .
- I checked the file type using ` file challenge.sal`
```
$ file challenge.sal
challenge.sal: Zip archive data, at least v2.0 to extract, compression method=deflate
```
- It consisted of zipped data that needed to be unzipped.
- the files  `digital-0.bin, digital-1.bin, digital-2.bin, digital-3.bin, digital-4.bin` are capture files and can be exported using `salleae logic 2` which i then downloaded.
- next , i exported binary data of the files.

<img width="500" height="500" alt="Screenshot (504)" src="https://github.com/user-attachments/assets/9d5964f0-1289-407f-9aec-0943f7ff08f0" />

- The size of digital_3.bin is suspicious , so I used `strings digital_3.bin` to see if there is any encoded text but it wasnt useful .
- Using A.I , I found out that the file had UART, 9600 baud, LSB-first, 8N1 framing and the analyzer used in logic 2 for UART is `Async Serial`.
  
 <img width="1030" height="381" alt="Screenshot (506)" src="https://github.com/user-attachments/assets/a85aacae-6c90-479b-a149-32b36b8bd618" />

- in logic 2 , I added the analyzer `Async Serial` for `UART` and checked the terminal , where I found a large chunk of data including the flag:

<img width="500" height="500" alt="Screenshot (505)" src="https://github.com/user-attachments/assets/f313071d-4cd5-4552-8934-7d1f63eb950e" />

## Flag:
`FCSC{b1dee4eeadf6c4e60aeb142b0b486344e64b12b40d1046de95c89ba5e23a9925}`


# 3.bare-metal-alchemist:

## Solution:
- used `strings -a firmware.elf` but nothing worthwhile.
```
$ strings -a firmware.elf�V���
[Ofm}
w|9^yq=t
w|9^yq=t
GCC: (GNU) 7.3.0
atmega328p
../../../../gcc/libgcc/config/avr/lib1funcs.S
/home/jenkins/workspace/avr-gcc-staging/label/debian7-x86_64/gcc-build/avr/avr5/libgcc
GNU AS 2.26
../../../../gcc/libgcc/config/avr/lib1funcs.S
/home/jenkins/workspace/avr-gcc-staging/label/debian7-x86_64/gcc-build/avr/avr5/libgcc
GNU AS 2.26
../../../../gcc/libgcc/config/avr/lib1funcs.S
/home/jenkins/workspace/avr-gcc-staging/label/debian7-x86_64/gcc-build/avr/avr5/libgcc
GNU AS 2.26
../../../../gcc/libgcc/config/avr
lib1funcs.S
../../../../gcc/libgcc/config/avr
lib1funcs.S
../../../../gcc/libgcc/config/avr
lib1funcs.S
avr-libc 2.0.0
uint8_t
uint16_t
__eeprom
UDR0
UCSR0A
UCSR0B
UCSR0C
UBRR0
TWAMR
TWBR
TWCR
TWSR
TWDR
TWAR
TIMSK1
TIFR1
TCCR1A
TCCR1B
TCCR1C
TCNT1
OCR1A
OCR1B
ICR1
GTCCR
TIMSK2
TIFR2
TCCR2A
TCCR2B
TCNT2
OCR2B
OCR2A
ASSR
GTCCR
ADMUX
ADCSRA
ADCSRB
DIDR0
ACSR
DIDR1
PORTB
DDRB
PINB
PORTC
DDRC
PINC
PORTD
DDRD
PIND
OCR0B
OCR0A
TCNT0
TCCR0B
TCCR0A
TIMSK0
TIFR0
GTCCR
EICRA
EIMSK
EIFR
PCICR
PCMSK2
PCMSK1
PCMSK0
PCIFR
SPDR
SPSR
SPCR
WDTCSR
OSCCAL
CLKPR
SREG
SPMCSR
MCUCR
MCUSR
SMCR
GPIOR2
GPIOR1
GPIOR0
EEAR
EEDR
EECR
__SP_H__
__SP_L__
__SREG__
__tmp_reg__
__zero_reg__
_ZL2z1v
timer0_millis
timer0_fract
timer0_overflow_count
_ZL2Xf
CSWTCH.14
_clear_bss.o
.do_clear_bss_start
.do_clear_bss_loop
_exit.o
__stop_program
__vector_22
__vector_1
__DATA_REGION_LENGTH__
__trampolines_start
_etext
__vector_24
__vector_12
__bad_interrupt
__data_load_end
__vector_6
__trampolines_end
__vector_3
__vector_23
__data_load_start
__dtors_end
__bss_end
__LOCK_REGION_LENGTH__
__vector_25
__vector_11
__init
_Z14serialEventRunv
__vector_13
__vector_17
__vector_19
__vector_7
__do_clear_bss
__eeprom_end
__vectors
__data_end
__vector_default
__vector_5
__SIGNATURE_REGION_LENGTH__
__ctors_start
__do_copy_data
__bss_start
main
__vector_4
__heap_end
__vector_9
__vector_2
__USER_SIGNATURE_REGION_LENGTH__
__vector_21
__vector_15
__dtors_start
__ctors_end
__stack
_edata
__vector_8
__EEPROM_REGION_LENGTH__
_exit
__vector_14
__vector_10
__vector_16
__data_start
__vector_18
__FUSE_REGION_LENGTH__
__TEXT_REGION_LENGTH__
__vector_20
.symtab
.strtab
.shstrtab
.data
.text
.bss
.comment
.note.gnu.avr.deviceinfo
.debug_aranges
.debug_info
.debug_abbrev
.debug_line
.debug_str
```
- using `file firmware.elf` , I found out:
```
$ file firmware.elf
firmware.elf: ELF 32-bit LSB executable, Atmel AVR 8-bit, version 1 (SYSV), statically linked, with debug_info, not stripped
```
- where firmware.elf is used in a microcontroller . (https://ww1.microchip.com/downloads/en/DeviceDoc/en590320.pdf)
- using `checksec`
```
Arch:       em_avr-32-little
    RELRO:      No RELRO
    Stack:      No canary found
    NX:         NX enabled
    PIE:        No PIE (0x800100)
    Stack:      Executable
    Stripped:   No
    Debuginfo:  Yes
```
- I found out what all of these meant:
  - RELRO-RELocation Read-Only protection is disabled. Normally, it makes sections like the GOT (Global Offset Table) read-only after startup.
  - Stack: No canary found->No stack-smashing protector (-fstack-protector) .
  - Non-Executable memory is enabled (the stack and heap are not executable).
  - Position Independent Executable is disabled. The binary is loaded at a fixed address every run
- I used the following code:
```
import re

pattern = re.compile(
    rb"[A-Za-z0-9_]{1,20}\{[A-Za-z0-9_\-\+\=\/\\\.\s]{10,200}\}"
)

with open("firmware.elf", "rb") as f:
    data = f.read()

for key in range(1, 256):
    decoded = bytes(b ^ key for b in data)
    matches = pattern.findall(decoded)
    for m in matches:
        print(f"Key: {key} Flag: {m.decode()}")
```
- and ran it to obtain the flag:
```
$ python3 hey.py
Key: 11 Flag: yjf{dgbenxTneo
                            TT}
Key: 11 Flag: TTnn{ydfTneo
                          TT}
Key: 57 Flag: 9mnxtk9mn{k9mnzk9mnjk9mn}
Key: 66 Flag: HBB{vBALxIyI
                          Q}
Key: 107 Flag: mzjyjhcpcNcxnkkkjzk{mzjyjhcpcNcxnkkk}
Key: 165 Flag: TFCCTF{Th1s_1s_som3_s1mpl3_4rdu1no_f1rmw4re}
```
## Flag:
`TFCCTF{Th1s_1s_som3_s1mpl3_4rdu1no_f1rmw4re}`



