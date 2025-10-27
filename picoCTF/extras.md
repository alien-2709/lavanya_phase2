# 1.Riddle Registry:
> Hi, intrepid investigator! 📄🔍 You've stumbled upon a peculiar PDF filled with what seems like nothing more than garbled nonsense. But beware! Not everything is as it appears. Amidst the chaos lies a hidden treasure—an elusive flag waiting to be uncovered.
> Find the PDF file here Hidden Confidential Document and uncover the flag within the metadata.

## Solutions:
- I first opened the pdf to look for clues but didnt see anything useful.
- <img width="500" height="757" alt="Screenshot (482)" src="https://github.com/user-attachments/assets/607a4e20-6378-4db2-ba32-a7ace2699637" />
- Next , I used `exiftool confidential.pdf` to look for clues in the metadata:
```
$ exiftool confidential.pdf
ExifTool Version Number         : 12.40
File Name                       : confidential.pdf
Directory                       : .
File Size                       : 178 KiB
File Modification Date/Time     : 2025:10:22 22:54:17+05:30
File Access Date/Time           : 2025:10:26 18:41:06+05:30
File Inode Change Date/Time     : 2025:10:22 22:54:17+05:30
File Permissions                : -rwxrwxrwx
File Type                       : PDF
File Type Extension             : pdf
MIME Type                       : application/pdf
PDF Version                     : 1.7
Linearized                      : No
Page Count                      : 1
Producer                        : PyPDF2
Author                          : cGljb0NURntwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV8wZTJkZTVhMX0=
```
- The author data looked interesting so I decoded it using base64 and found the flag.
```
$ echo 'cGljb0NURntwdXp6bDNkX20zdGFkYXRhX2YwdW5kIV8wZTJkZTVhMX0='|base64 --decode
picoCTF{puzzl3d_m3tadata_f0und!_0e2de5a1
```
## Flag:
`picoCTF{puzzl3d_m3tadata_f0und!_0e2de5a1}`

# 2.Hidden in plainsight:
> You’re given a seemingly ordinary JPG image. Something is tucked away out of sight inside the file. Your task is to discover the hidden payload and extract the flag.
Download the jpg image here.

## Solutions:
- I first opened the image in my gallery and didn't find anything useful.
  ![img (2)](https://github.com/user-attachments/assets/d99322b8-db06-4b7f-9505-3b8d27663d7e)
- So , I used `exiftool img.jpg` to find the metadata and see if there is anything useful:
 ```
$ exiftool img.jpg
ExifTool Version Number         : 12.40
File Name                       : img.jpg
Directory                       : .
File Size                       : 72 KiB
File Modification Date/Time     : 2025:10:26 17:07:50+05:30
File Access Date/Time           : 2025:10:26 18:40:19+05:30
File Inode Change Date/Time     : 2025:10:26 17:07:50+05:30
File Permissions                : -rwxrwxrwx
File Type                       : JPEG
File Type Extension             : jpg
MIME Type                       : image/jpeg
JFIF Version                    : 1.01
Resolution Unit                 : None
X Resolution                    : 1
Y Resolution                    : 1
Comment                         : c3RlZ2hpZGU6Y0VGNmVuZHZjbVE9
Image Width                     : 640
Image Height                    : 640
Encoding Process                : Baseline DCT, Huffman coding
Bits Per Sample                 : 8
Color Components                : 3
Y Cb Cr Sub Sampling            : YCbCr4:2:0 (2 2)
Image Size                      : 640x640
Megapixels                      : 0.410
```
- The comment was encoded is base64 so I decoded it to find the following:
```
s$ echo 'c3RlZ2hpZGU6Y0VGNmVuZHZjbVE9'|base64 --decode
steghide:cEF6endvcmQ=
```
- This indicated that steghide is used to extract data from the image and the password seemed to be base64 encoded too.
```
$ echo 'cEF6endvcmQ=a'|base64 --decode
pAzzword
```
- Next , I used `steghide extract -sf img.jpg -p pAzzword` to extract the flag.
```
$ steghide extract -sf img.jpg -p pAzzword
the file "flag.txt" does already exist. overwrite ? (y/n) y
wrote extracted data to "flag.txt".
$ cat flag.txt
picoCTF{h1dd3n_1n_1m4g3_92f08d7c}
```

## Flag:
`picoCTF{h1dd3n_1n_1m4g3_92f08d7c}`
