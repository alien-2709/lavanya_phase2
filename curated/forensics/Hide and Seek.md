# Hide and Seek :
> Sakamoto’s at it again with a game of hide and seek, but this time, it’s not with Shin or his daughter. An old friend hid some secret data in this image. Can you find it before the others do?
Hint:
Even in retirement, Sakamoto never loses at hide and seek. Maybe stegseek can help you keep up

## Solution:
* Using `stegseek  sakamoto.jpg` , I first obtained the passphrase needed for conducting steghide operation.
 ```
alien2709@BORKARS:~$ stegseek sakamoto.jpg rockyou.txt
StegSeek 0.6 - https://github.com/RickdeJager/StegSeek

[i] Found passphrase: "iloveyou1"
[i] Original filename: "flag.txt".
[i] Extracting to "sakamoto.jpg.out".
```
* Next , extracting data using steghide using passphrase `iloveyou1` and putting it into flag.txt and subsequently catting out flag.txt.
```
alien2709@BORKARS:~$ steghide extract -sf sakamoto.jpg
Enter passphrase:
wrote extracted data to "flag.txt".
alien2709@BORKARS:~$ cat flag.txt
nite{h1d3_4nd_s33k_but_w1th_st3g_sdfu9s8}
```
## Flag:
`nite{h1d3_4nd_s33k_but_w1th_st3g_sdfu9s8}`

## What I learnt:
* I learnt how to use stegseek and how StegSeek is a fast steganography-cracking tool used to detect and extract hidden data embedded with steghide inside image or audio files.
* Detects whether a file contains steghide-embedded data ,brute-forces the steghide passphrase using a wordlist
and automatically extracts the hidden file if the password is found.
* I was stuck at this part as I wasnt able to access the wordlists but eventually was able to get it.
* syntax: `stegseek <cover_file> <wordlist>`
* to check if steghide data exists (no cracking): `stegseek --seed sakamoto.jpg`.
  


