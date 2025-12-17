# Nutrela Chunks:
>One of my favorite foods is soya chunks. But as I was enjoying some Nutrela today, I noticed a few chunks weren’t quite right. Seems like something’s off with their structure. Could you help me fix these broken chunks so I can enjoy my meal again?

## Solution:
* Firstly I checked the metadata of the .png file using exiftool :
```
alien2709@BORKARS:~$ exiftool nutrela2.png
ExifTool Version Number         : 12.40
File Name                       : nutrela2.png
Directory                       : .
File Size                       : 526 KiB
File Modification Date/Time     : 2025:12:17 19:50:54+05:30
File Access Date/Time           : 2025:12:17 19:50:54+05:30
File Inode Change Date/Time     : 2025:12:17 19:50:54+05:30
File Permissions                : -rwxr-xr-x
Error                           : File format error
```
* as observed , there is a file format error that prevents the file from opening.
* so I analyzed the hexdump using the hexeditor and compared it to a standard png file.
<img width="500" height="500" alt="hexdump" src="https://github.com/user-attachments/assets/4f6852f4-9ff0-4309-a560-d3d939cb4afc" />

* next I compared it to a standard png file and made the necessary changes to the header, IHDR ,IDAT and IEND chunks respectively.
<img width="500" height="500" alt="Screenshot (518)" src="https://github.com/user-attachments/assets/d637ae8d-63a6-4ed9-bec5-28dc7aaee638" />

<img width="500" height="500" alt="Screenshot (517)" src="https://github.com/user-attachments/assets/08e59cd4-3571-4618-9711-b3be77936bc4" />

* after saving the changes made and opening the file , I obtained the following image with the flag written on it.
<img width="500" height="500" alt="Screenshot (510)" src="https://github.com/user-attachments/assets/c75edb8c-d3ac-49c6-9caf-1d50fdf7f876" />

## Flag:
`nite{nOw_yOu_knOw_abOut_PNG_chunk5}`

## References:
* https://medium.com/@0xwan/png-structure-for-beginner-8363ce2a9f73
