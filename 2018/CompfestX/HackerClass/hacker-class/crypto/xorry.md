# Xorry \(225\)

> Run the attached script with:
>
> ```
> python3 xorry.py
> ```
>
>
>
> Good luck! :\)



###### xorry.py

```py
#!/usr/bin/python3
import base64

secret_data = ("AAAAUgNLEkdTBh5FNzwvNhxJBH8EDjEHTwYdWSsDAx5/EDAWFQYRLQhGGEUsDj"
               "ICGwcNMQJOClIOERodTxowVBwEGDNZFhoqFwkLBjpFAUUSBBAK")
secret_password = ("\x45\x5a\x56\x46\x54\x15\x40\x14\x42\x5d\x5e\x51\x45\x5a"
                   "\x5a\x5a\x56\x12\x5a\x14\x46\x53\x5d\x40\x11\x46\x5c\x14"
                   "\x45\x57\x5f\x58\x11\x4b\x5c\x41")

def y(s1, s2):
    len1 = len(s1)
    len2 = len(s2)
    res = ""
    for i in range(len1):
        chr1 = ord(s1[i]) ^ ord(s2[i % len2])
        res += chr(chr1)
    return res

inp = input("Input your password to get the flag :")
wwhatisthisvariable = y(inp, '1234')
if wwhatisthisvariable == secret_password:
    d1, d2 = base64.b64decode(secret_data).decode('utf-8'), inp
    print("Horray, you've cracked the password!!")
    print(y(d1, d2))
```

##### POC

![](https://user-images.githubusercontent.com/13828056/42499969-50cbc640-845a-11e8-8fc5-6cde94b6e00f.png)

![](https://user-images.githubusercontent.com/13828056/42500072-85146b96-845a-11e8-8697-ca5b3017d22f.png)



