# 1.m00nwalk
> Decode this (message) from the moon.
> message.wav

## Solution:
- At first , I thought it was morse code so I tried to decode it and obtain a flag either in the spectogram or the decoded message but that didn't succeed.
- Next , I used the hint given which said `How did the images of the moon get sent back to earth?` . I looked it up and saw SSTV - SLOW SCAN TELEVISION which converted audio to images. The option to choose was `Scottie2` since the second hint was `CMU mascot` which was Scotty the Scottish terrier.
- Using (https://sstv-decoder.mathieurenaud.fr) , I decoded the audio file into an image which gave me the flag.

<img width="320" height="256" alt="decoded-image" src="https://github.com/user-attachments/assets/1d8e7511-47b3-4c09-ac0b-7547f9abd9ff" />

## Flag:
`picoCTF{beep_boop_im_in_space}`

## Resources:
- (https://sstv-decoder.mathieurenaud.fr) 

# 2.tunn3l v1s10n
> We found this file. Recover the flag.

## Solution:
- The file starts with `BM` indicating it is a bitmap image , but I was unable to open it normally even after renaming it to `tunn3l_v1s10n.bmp` .
- I found the readable characters of this file using ` strings -a tunn3l_v1s10n | grep 'pico'/'flag' ` but wasn't able to find anything worthwhile.
- Thus, I thought of converting it to a .png or .jpeg file.
