## Where This Photo Taken? \(50\)

> The Flag is MD5 of city where this photo taken
>
> [https://ctf.compfest.web.id/files/c9b3b0d5907fdab9ec3930d981f90706/kucing\_unyu.jpg](https://ctf.compfest.web.id/files/c9b3b0d5907fdab9ec3930d981f90706/kucing_unyu.jpg "kucing\_unyu.jpg")

##### POC

* Mencari informasi "city" sesuai arahan dari soal

```
>> exiftool kucing_unyu.jpg | grep City | cut -d':' -f2 | xargs
>> Krung Thep Mahanakhon Amon Ratta
```

* Awalnya saya mengira md5 dari city name pada gambar merupakan flag, ternyata tertypu.. 
* Setelah mencari di nama kota tersebut digoogle, kami menemukan nama lengkap dari kota tersebut.

> https://www.quora.com/topic/Krung-Thep-Mahanakhon-Amon-Rattanakosin-Mahinthara-Ayuthaya-Mahadilok-Phop-Noppharat-Ratchathani-Burirom-Udomratchaniwet-Mahasathan-Amon-Piman-Awatan-Sathit-Sakkathattiya-Witsanukam-Prasit

![](https://user-images.githubusercontent.com/13828056/42501559-296dcbe4-845e-11e8-8a56-5894208ed6c5.jpg)



