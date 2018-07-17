# Rev1 \(472\)

> Crack this flag checker!

###### POC

Diberikan sebuah file ELF 64-bit, setelah itu kami buka dengan IDA Pro.

```c
int __cdecl main(int argc, const char **argv, const char **envp)
{
  int result; // eax@4
  __int64 v4; // rdx@4
  char v5; // [sp+0h] [bp-50h]@1
  __int64 v6; // [sp+48h] [bp-8h]@1

  v6 = *MK_FP(__FS__, 40LL);
  printf("Flag: ", argv, envp);
  __isoc99_scanf("%s", &v5);
  if ( (unsigned int)check((__int64)&v5) )
    printf("Congrats! you got the flag right. The correct flag:\n%s", &v5);
  else
    printf("Wrong flag!", &v5);
  result = 0;
  v4 = *MK_FP(__FS__, 40LL) ^ v6;
  return result;
}
```

Alur dari fungsi **main** diatas, program meminta inputan yang kemudian disimpan di variable **v5**

```c
signed __int64 __fastcall check(__int64 a1)
{
  signed __int64 result; // rax@2

  if ( *(_BYTE *)a1 == 67 )
  {
    putchar(49);
    if ( *(_BYTE *)(a1 + 1) == 84 )
    {
      putchar(50);
      if ( *(_BYTE *)(a1 + 2) == *(_BYTE *)a1 + 3 )
      {
        putchar(51);
        if ( *(_BYTE *)(a1 + 3) == 88 )
        // .......
```

Pada fungsi **check** terdapat banyak sekalipengecekan, akhirnya kami membuatkan parser untuk mengambil variable" yang dibutuhkan saja dan karna kami akan menggunakan package **z3 **untuk auto-solve, maka sekalian saya buatkan payloadnya dalam bentuk kodingan python.

```php
<?php 
$datas  = explode("\n", file_get_contents("check"));
$result = "";

foreach ($datas as $index => $data) {
    $data = trim($data);
    if (preg_match("/if/", $data)) {
        $data = str_replace([
            "if ( *(_BYTE *)(", 
            ") =", 
            "*(_BYTE *)",
            "if ( ",
            "(", ")"
        ], ["", " =", "", "", "", ""], $data);

        list($x, $y) = explode("==", $data);

        if (preg_match("/[+]/", $x)) {
            $x = explode("+", $x);
            $result .= sprintf("z.add(%s[%s] == ", trim($x[0]), trim($x[1]));
        } else $result .= "z.add(a1[0] == ";    

        if (preg_match("/[+]/", $y)) {
            $y = explode("+", $y);
            if (count($y) > 2) {
                $result .= sprintf("%s)" . PHP_EOL, (int) explode(")", explode(sprintf("z.add(a1[4] == ", trim($y[1])), $result)[1])[0] + trim($y[2]));
            } else {
                $result .= sprintf("%s[%s])" . PHP_EOL, trim($y[0]), trim($y[1]));
            }
        } else {
            if (preg_match("/a1/", $y)) {
                $result .= "a1[0])" . PHP_EOL;
            } else {
                $result .= sprintf("%s)" . PHP_EOL, trim($y));
            }
        }
    }
}

$result  = str_replace("a1[3])", sprintf("%s)", 67+3), $result); # seharusnya a1 + 3, maka 67+3=70, chr(70) == 'F'
$length  = count(explode("\n", $result))-1;
$payload = <<<EOD
from z3 import *

a1 = [BitVec("x{}".format(i), 32) for i in range({$length})]
z = Solver()

{$result}
z.check()

print "".join([ chr(z.model()[i].as_long()) for i in a1 ])
EOD;

print_r($payload);
```

```bash
>> php parser.php >> crackme.py && python crackme.py
```



