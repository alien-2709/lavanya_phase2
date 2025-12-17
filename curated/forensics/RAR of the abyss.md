# RAR of the abyss:
>Two philosophers peer into the networked abyss and swap a secret. Use the secret to decrypt the Abyss’ RAwR and pull your flag from the void.

## Solution:
* I found the file type using `file abyss.pcap`
```
alien2709@BORKARS:~$ file abyss.pcap
abyss.pcap: pcap capture file, microsecond ts (little-endian) - version 2.4 (Ethernet, capture length 65535)
```
* I opened the capture file by using wireshark.
<img width="500" height="500" alt="Screenshot (519)" src="https://github.com/user-attachments/assets/42648469-d14f-4167-9999-222e66092a71" />

* I followed the conversation in the tcp channel and ultimately found the password:
* the convo:
 ```
Camus: One must imagine Sisyphus happy but are we happy ?
Nietzsche: You will be happy after reading my latest work
Rar!
wOdp
Camus: whats the password ?
Nietzsche: b3y0ndG00dand3vil
Camus: thanks
```
<img width="500" height="500" alt="Screenshot (521)" src="https://github.com/user-attachments/assets/c1dcd99b-31d6-4783-b178-f8fdc4d71315" />
password: ` b3y0ndG00dand3vil`.

* Now since the conversation hints at a RAR file . I stored the encoded data and put it into a .rar file which is a compressed file.
<img width="500" height="500" alt="Screenshot (522)" src="https://github.com/user-attachments/assets/bc7976a4-414c-47fe-a277-b58e18f59761" />
<img width="500" height="500" alt="Screenshot (524)" src="https://github.com/user-attachments/assets/f4b74a33-fe81-499b-93db-bf0f00945e03" />

* Now I learnt about RAR and how RAR stands for Roshal Archive,and it is used to reduce the size of files.
* I first created the rar file , I stored the encoded data as `encoded.txt` and then created a rar file `encoded.rar`.
```
alien2709@BORKARS:~$ rar a encoded.rar encoded.txt

RAR 6.23   Copyright (c) 1993-2023 Alexander Roshal   1 Aug 2023
Trial version             Type 'rar -?' for help

Evaluation copy. Please register.

Creating archive encoded.rar

Adding    encoded.txt                                                 OK
Done
```
* Next :
```
alien2709@BORKARS:~$ rar a -hpb3y0ndG00dand3vil data.rar encoded.txt

RAR 6.23   Copyright (c) 1993-2023 Alexander Roshal   1 Aug 2023
Trial version             Type 'rar -?' for help

Evaluation copy. Please register.

Updating archive data.rar

Adding    encoded.txt                                                 OK
Done
alien2709@BORKARS:~$ rar x data.rar

RAR 6.23   Copyright (c) 1993-2023 Alexander Roshal   1 Aug 2023
Trial version             Type 'rar -?' for help

Enter password (will not be echoed) for data.rar:


Extracting from data.rar


Would you like to replace the existing file flag.txt
    47 bytes, modified on 2025-10-22 19:23
with a new one
    47 bytes, modified on 2025-10-22 19:23

[Y]es, [N]o, [A]ll, n[E]ver, [R]ename, [Q]uit Y

Extracting  flag.txt                                                  OK

Would you like to replace the existing file encoded.txt
   572 bytes, modified on 2025-12-17 20:47
with a new one
   572 bytes, modified on 2025-12-17 20:47

[Y]es, [N]o, [A]ll, n[E]ver, [R]ename, [Q]uit Y

Extracting  encoded.txt                                               OK
All OK
alien2709@BORKARS:~$ cat flag.txt
nite{thus_sp0k3_th3_n3tw0rk_f0r3ns1cs_4n4lyst}
```
## Flag:
`nite{thus_sp0k3_th3_n3tw0rk_f0r3ns1cs_4n4lyst}`




