# 1.m00nwalk
> Decode this (message) from the moon.
> message.wav

## Solution:
- At first , I thought it was morse code so I tried to decode it and obtain a flag either in the spectogram or the decoded message but that didn't succeed.
- Next , I used the hint given which said `How did the images of the moon get sent back to earth?` . I looked it up and saw SSTV - SLOW SCAN TELEVISION which converted audio to images. The option to choose was `Scottie2` since the second hint was `CMU mascot` which was Scotty the Scottish terrier.
- Using (https://sstv-decoder.mathieurenaud.fr) , I decoded the audio file into an image which gave me the flag.
- <img width="320" height="256" alt="decoded-image" src="https://github.com/user-attachments/assets/1d8e7511-47b3-4c09-ac0b-7547f9abd9ff" />

## Flag:
`picoCTF{beep_boop_im_in_space}`

## Resources:
- (https://sstv-decoder.mathieurenaud.fr) 

# 2.tunn3l v1s10n
> We found this file. Recover the flag.

## Solution:
- After running :
  ```
  $ file tunn3l_v1s10n
  tunn3l_v1s10n: data
  ```
  I obtained nothing much , only data.
- After running `exiftool tunn3l_v1s10n`, I obtained the file type as .bmp i.e A bitmap image.
  ```
  File Type                       : BMP
  File Type Extension             : bmp
  MIME Type                       : image/bmp
  ```
-  but I was unable to open it normally even after renaming it to `tunn3l_v1s10n.bmp` showing an error about headers.
- I found the readable characters of this file using ` strings -a tunn3l_v1s10n | grep 'pico'/'flag' ` but wasn't able to find anything worthwhile.
- Next, I tried converting .bmp to .jpg and .png online but it didnt work out.
- I used (https://www.ece.ualberta.ca/~elliott/ee552/studentAppNotes/2003_w/misc/bmp_file_format/bmp_file_format.htm) to know more about bitmap headers and the sizes.
  
  <img width="500" height="500" alt="bitmap" src="https://github.com/user-attachments/assets/6a36db55-8b8f-443f-93c6-8fdb520c7e0f" />
-  Using hexeditor , I opened the file and found the following:
  
  <img width="1920" height="960" alt="hexeditor" src="https://github.com/user-attachments/assets/8615fd81-9476-4ed9-9e06-5d8648887a55" />
  
- I compared this with the sizes of the info-header ( which starts after 14 bytes so `BA D0 00 00` specifies the size of the info-header and according to the info. given in the site and realised that the size should be 40 bytes while the size given is `BA D0 00 00` which is equivalent to `0xD0BA=53434 bytes` , thus , using the hexeditor ,I changed it to `28 00 00 00` which is `0x28 = 40 bytes` and saved it.
- when I opened it , this is what I saw:
  <img width="1200" height="355" alt="firstimage" src="https://github.com/user-attachments/assets/80c2866f-1c52-4685-b2f5-e07adb5df7c1" />
  
- This didn't give us the flag but maybe the image is extended and larger so I checked the normal bitmap again to check where height and width were in the info header.I found the height given by `32 01 00 00` which is `0x132=306 bytes ` , so I tried changing it a bit to ` 32 02 00 00` but the image I got after opening is :
  <img width="1151" height="576" alt="Screenshot (476)" src="https://github.com/user-attachments/assets/788919fc-3dbe-43fd-9bb3-b5ba7459e266" />
- So , I further changed it to `32 03 00 00` and finally saw the flag:
  <img width="1157" height="835" alt="Screenshot (478)" src="https://github.com/user-attachments/assets/62e3275a-7047-4336-a0a8-087e4b326a5d" />


  
  

  
  
  


   ## Flag:
  `picoCTF{qu1t3_a_v13w_2020}`
