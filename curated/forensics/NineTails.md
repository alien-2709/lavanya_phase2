# Nine Tails:
>Looks like I got a little too clever and hid the flag as a password in Firefox, tucked away like one of NineTails’ many tails. Recover the "logins" and the "key4" and let it guide you to the flag.
Hint:
I named my Ninetails "j4gjesg4", quite a peculiar name isn't it?

## Solution:
* from the description , I found out that Firefox stores saved passwords using two files:
   - logins.json → encrypted usernames & passwords
   - key4.db → encryption key database (NSS)
* You need both to decrypt saved passwords.
* And `j4gjesg4` is the primary password or Firefox master password.
* First I extracted the ninetails.rar file:
```
alien2709@BORKARS:~$ rar x ninetails.rar

RAR 6.23   Copyright (c) 1993-2023 Alexander Roshal   1 Aug 2023
Trial version             Type 'rar -?' for help


Extracting from ninetails.rar


Would you like to replace the existing file ninetails.ad1
843166472 bytes, modified on 2024-09-06 22:58
with a new one
843166472 bytes, modified on 2024-09-06 22:58

[Y]es, [N]o, [A]ll, n[E]ver, [R]ename, [Q]uit Y

Extracting  ninetails.ad1                                             OK
All OK
```
* now we obtain a ad1 file .
* An AD1 file is a forensic image created by AccessData FTK.
* it stands for `access data digital image`
* it basically gives the disk image instead of the raw files.
* now to extract the image , we use ftk imager , however I wasnt able to download it so I found another way using chatGPT.
```
alien2709@BORKARS:~$ 7z l ninetails.ad1

7-Zip [64] 16.02 : Copyright (c) 1999-2016 Igor Pavlov : 2016-05-21
p7zip Version 16.02 (locale=C.UTF-8,Utf16=on,HugeFiles=on,64 bits,8 CPUs Intel(R) Core(TM) i5-1035G1 CPU @ 1.00GHz (706E5),ASM,AES-NI)

Scanning the drive for archives:
1 file, 843166472 bytes (805 MiB)

Listing archive: ninetails.ad1

--
Path = ninetails.ad1
Type = Cab
WARNINGS:
There are data after the end of archive
Offset = 394302
Physical Size = 71954
Tail Size = 842700216
Method = MSZip
Blocks = 1
Volumes = 1
Volume Index = 0
ID = 0

   Date      Time    Attr         Size   Compressed  Name
------------------- ----- ------------ ------------  ------------------------
2024-05-14 09:27:02 ....A       186277               authroot.stl
------------------- ----- ------------ ------------  ------------------------
2024-05-14 09:27:02             186277    843166472  1 files

Warnings: 1
```
* from this we see The .ad1 is not a normal CAB.It contains a small CAB header (≈ 394 KB) followed by a huge tail (~803 MB) of raw data.
* The real data (Firefox profile) is in the “tail”, not in the CAB file that 7-Zip is showing.Thus , we can see `authroot.stl`.
```
alien2709@BORKARS:~$ dd if=ninetails.ad1 of=tail.img bs=4M skip=466256 iflag=skip_bytes status=progress
549453824 bytes (549 MB, 524 MiB) copied, 20 s, 27.5 MB/s
200+1 records in
200+1 records out
842700216 bytes (843 MB, 804 MiB) copied, 21.1134 s, 39.9 MB/s
alien2709@BORKARS:~$ file tail.img
tail.img: data
```
* next , I checked if there exists a file system:
```
alien2709@BORKARS:~$ fdisk -l tail.img
Disk tail.img: 803.66 MiB, 842699776 bytes, 1645898 sectors
Units: sectors of 1 * 512 = 512 bytes
Sector size (logical/physical): 512 bytes / 512 bytes
I/O size (minimum/optimal): 512 bytes / 512 bytes
```
* there are no partitions.
* next:
```
alien2709@BORKARS:~$ strings tail.img | grep -i logins.json
strings tail.img | grep -i key4.db
logins.json
logins.json
key4.db
key4.db
```
```
alien2709@BORKARS:~$ grep -abo "logins.json" tail.img | head
680907961:logins.json
680910425:logins.json
alien2709@BORKARS:~$ grep -abo "key4.db" tail.img | head
686500026:key4.db
686503077:key4.db
```
* 
