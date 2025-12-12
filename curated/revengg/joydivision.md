## flag.txt:
`œ–˜6Š>¶˜:=¿¼‘œ10ºžó=³±º—’œ<–2±‘Å09³°‘˜7œ4½Ÿ§3¿µ¼”ž2‚6¼š©65·ºœ”=È8°K9»¹¸šš”8Î:·“m<1»¼3¤<³‘?·½¶œ–˜>º>²œ12=¿»Ÿœ9À0¶’³5³±¼š’œ4Æ2½•å89³¼›˜?¼4¹“—;¿µ¶›ž:¢6¸ž©>5·±œ”5È8¼”[1»¹¶š”0®:³—}41»¼;´<¿•7·½¸œ–˜6º>¾Q:=¿¹‘œ1°0²–ó=³±´Ÿ’œ<ö2¹`
## sol.py:
```


def collapse_round(data):

    out = bytearray()
    for i in range(len(data) // 2):
        a = data[2*i]
        b = data[2*i + 1]

        if i % 2 == 0:
            # even index: take low nibble of a + high nibble of b
            out.append((a & 0x0F) | (b & 0xF0))
        else:
            # odd index: take high nibble of a + low nibble of b
            out.append((a & 0xF0) | (b & 0x0F))
    return out


def decode_flag(filename="flag.txt"):
    
    data = bytearray(open(filename, "rb").read())

    
    for _ in range(3):
        data = collapse_round(data)

  
    key = 0x69
    for i in range(len(data)):
        if i % 2 == 0:
            data[i] = (~data[i]) & 0xFF      
        else:
            data[i] ^= key                  
            key = (key + 32) & 0xFF         

    
    return data.decode(errors="replace")


if __name__ == "__main__":
    print(decode_flag("flag (1).txt"))
```
flag: `sunshine{C3A5ER_CR055ED_TH3_RUB1C0N}`
