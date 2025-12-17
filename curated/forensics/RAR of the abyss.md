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
alien2709@BORKARS:~$ echo '526172211a07010094ceedc121040000010f644537b9278a61e25503b4957506e3674391aa0766e843dca3d75f52dc08f7774f6470a0a32680ccd66b612ae47347d80e85f7a2d1907178728fa65de05fe2ec158a6fb40be196b2fe5b2abbe7f1568c445080a86b1b49eebe54ef777e1886c796fabafd16e51baaa431b1c0ed3a6f3a0700a6c34b4f64ea7103e939aff7a95a9e9b2ea0b87cbc186159a252d0048f5b1da871387f8f930fbde37df8dd1e41a8215c00b045e59f374c21e67c9f1dd02fb983ee101784fadaa92929ed7b6a61c4da9d9ffa7487f7e6853dadd1ebf40b1998b716f0ab41d19d1b6a25000c58c2fc96f9b0c4cdcfa7ee79b2ef926b051dff7132a4c4a3f84b75dbd9760b67fba3cb5c797bd7bdf1d7de52a94a7f'| xxd -r -p > data.rar
alien2709@BORKARS:~$ ls | grep data.rar
data.rar
alien2709@BORKARS:~$ rar x data.rar -pb3y0ndG00dand3vil

RAR 6.23   Copyright (c) 1993-2023 Alexander Roshal   1 Aug 2023
Trial version             Type 'rar -?' for help


Extracting from data.rar


Would you like to replace the existing file flag.txt
    42 bytes, modified on 2025-12-16 19:14
with a new one
    47 bytes, modified on 2025-10-22 19:23

[Y]es, [N]o, [A]ll, n[E]ver, [R]ename, [Q]uit Y

Extracting  flag.txt                                                  OK
All OK
alien2709@BORKARS:~$ cat flag.txt
nite{thus_sp0k3_th3_n3tw0rk_f0r3ns1cs_4n4lyst}
```
## Flag:
`nite{thus_sp0k3_th3_n3tw0rk_f0r3ns1cs_4n4lyst}`




