# 自作エミュレータで学ぶx86アーキテクチャ-コンピュータが動く仕組みを徹底理解!

![自作エミュレータで学ぶx86アーキテクチャ-コンピュータが動く仕組みを徹底理解!](image.png)

## Runtime Environment

I check my codes in Ubuntu on WSL.

```
x86_emulator-book-ubuntu …
➜ neofetch                   
            .-/+oossssoo+/-.               awawa@DESKTOP-T1C0N93 
        `:+ssssssssssssssssss+:`           --------------------- 
      -+ssssssssssssssssssyyssss+-         OS: Ubuntu 22.04.4 LTS on Windows 10 x86_64 
    .ossssssssssssssssssdMMMNysssso.       Kernel: 5.15.167.4-microsoft-standard-WSL2 
   /ssssssssssshdmmNNmmyNMMMMhssssss/      Uptime: 10 hours, 15 mins 
  +ssssssssshmydMMMMMMMNddddyssssssss+     Packages: 1039 (dpkg), 6 (snap) 
 /sssssssshNMMMyhhyyyyhmNMMMNhssssssss/    Shell: zsh 5.8.1 
.ssssssssdMMMNhsssssssssshNMMMdssssssss.   Theme: Adwaita [GTK3] 
+sssshhhyNMMNyssssssssssssyNMMMysssssss+   Icons: Adwaita [GTK3] 
ossyNMMMNyMMhsssssssssssssshmmmhssssssso   Terminal: Windows Terminal 
ossyNMMMNyMMhsssssssssssssshmmmhssssssso   CPU: Intel i7-14700KF (28) @ 3.417GHz 
+sssshhhyNMMNyssssssssssssyNMMMysssssss+   GPU: c98a:00:00.0 Microsoft Corporation Device 008e 
.ssssssssdMMMNhsssssssssshNMMMdssssssss.   Memory: 1497MiB / 15906MiB 
 /sssssssshNMMMyhhyyyyhdNMMMNhssssssss/
  +sssssssssdmydMMMMMMMMddddyssssssss+                             
   /ssssssssssshdmNNNNmyNMMMMhssssss/                              
    .ossssssssssssssssssdMMMNysssso.
      -+sssssssssssssssssyyyssss+-
        `:+ssssssssssssssssss+:`
            .-/+oossssoo+/-.

```

```
➜ gcc --version                  
gcc (Ubuntu 6.4.0-17ubuntu1) 6.4.0 20180424
Copyright (C) 2017 Free Software Foundation, Inc.
This is free software; see the source for copying conditions.  There is NO
warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
```

## How to execute my codes

The Makefile is created by refering to this github repository: [Kenta11/x86-emulator-book-Manjaro: 『自作エミュレータで学ぶx86アーキテクチャ』の Manjaro 向けサンプルコード](https://github.com/Kenta11/x86-emulator-book-Manjaro/tree/master)

### ch 3.2

#### command

`make run-helloworld2`

#### output

```
nasm -f bin -o helloworld2.bin helloworld2.asm
./px86 helloworld2.bin
EIP = 7C00, Code = B8
EIP = 7C05, Code = E9


end of program.

EAX = 00000029
ECX = 00000000
EDX = 00000000
EBX = 00000000
ESP = 00007c00
EBP = 00000000
ESI = 00000000
EDI = 00000000
EIP = 00000000
```

### ch 3.4

#### command

` make run-modrm`

#### output

```
nasm -f bin -o modrm-test.bin modrm-test.asm
./px86 modrm-test.bin
EIP = 7C00, Code = 83
EIP = 7C03, Code = 89
EIP = 7C05, Code = B8
EIP = 7C0A, Code = C7
EIP = 7C11, Code = 01
EIP = 7C14, Code = 8B
EIP = 7C17, Code = FF
EIP = 7C1A, Code = 8B
EIP = 7C1D, Code = E9


end of program.

EAX = 00000002
ECX = 00000000
EDX = 00000000
EBX = 00000000
ESP = 00007bf0
EBP = 00007bf0
ESI = 00000007
EDI = 00000008
EIP = 00000000
```

### ch 3.7-1(LEAVE order)

#### command

`  make run-crt `

#### output

```
nasm -f elf crt0.asm
gcc -O0 -march=i386 -m32 -nostdlib -fno-asynchronous-unwind-tables -g -fno-stack-protector -fno-pie -c -o leave-test.o leave-test.c
ld -m elf_i386 --entry=start --oformat=binary -Ttext 0x7c00 -o leave-test.bin crt0.o leave-test.o
./px86 leave-test.bin
EIP = 7C00, Code = E8
EIP = 7C0A, Code = 55
EIP = 7C0B, Code = 89
EIP = 7C0D, Code = 83
EIP = 7C10, Code = C7
EIP = 7C17, Code = FF
EIP = 7C1A, Code = 8B
EIP = 7C1D, Code = C9
EIP = 7C1E, Code = C3
EIP = 7C05, Code = E9


end of program.

EAX = 00000029
ECX = 00000000
EDX = 00000000
EBX = 00000000
ESP = 00007c00
EBP = 00000000
ESI = 00000000
EDI = 00000000
EIP = 00000000
```

### ch 3.7-2(CALL order)

#### command

`  make run-crt2 `

#### output

```
gcc -O0 -march=i386 -m32 -nostdlib -fno-asynchronous-unwind-tables -g -fno-stack-protector -fno-pie -c -o leave-test2.o leave-test2.c
ld -m elf_i386 --entry=start --oformat=binary -Ttext 0x7c00 -o leave-test2.bin crt0.o leave-test2.o
./px86 leave-test2.bin
EIP = 7C00, Code = E8
EIP = 7C17, Code = 55
EIP = 7C18, Code = 89
EIP = 7C1A, Code = 6A
EIP = 7C1C, Code = 6A
EIP = 7C1E, Code = E8
EIP = 7C0A, Code = 55
EIP = 7C0B, Code = 89
EIP = 7C0D, Code = 8B
EIP = 7C10, Code = 8B
EIP = 7C13, Code = 01
EIP = 7C15, Code = 5D
EIP = 7C16, Code = C3
EIP = 7C23, Code = 83
EIP = 7C26, Code = C9
EIP = 7C27, Code = C3
EIP = 7C05, Code = E9


end of program.

EAX = 00000007
ECX = 00000000
EDX = 00000002
EBX = 00000000
ESP = 00007c00
EBP = 00000000
ESI = 00000000
EDI = 00000000
EIP = 00000000
```

### ch 3.10(IF order)

#### command

`  make run-if `

#### output

```
gcc -O0 -march=i386 -m32 -nostdlib -fno-asynchronous-unwind-tables -g -fno-stack-protector -fno-pie -c -o if-test.o if-test.c
ld -m elf_i386 --entry=start --oformat=binary -Ttext 0x7c00 -o if-test.bin crt0.o if-test.o
./px86 if-test.bin
EIP = 7C00, Code = E8
EIP = 7C1F, Code = 55
EIP = 7C20, Code = 89
EIP = 7C22, Code = B8
EIP = 7C27, Code = 5D
EIP = 7C28, Code = C3
EIP = 7C05, Code = E9


end of program.

EAX = 00000003
ECX = 00000000
EDX = 00000000
EBX = 00000000
ESP = 00007c00
EBP = 00000000
ESI = 00000000
EDI = 00000000
EIP = 00000000
```


### ch 3.12-1(input)

#### preparetion

`  make run-io `

#### command

``` 
./px86 -q in.bin 
a
```

#### output

```


end of program.

EAX = 00000061
ECX = 00000000
EDX = 000003f8
EBX = 00000000
ESP = 00007c00
EBP = 00000000
ESI = 00000000
EDI = 00000000
EIP = 00000000
```


### ch 3.12-2(output)

#### preparetion

`  make run-io `

#### command

``` 
./px86 -q out.bin 
```

#### output

```
A

end of program.

EAX = 00000041
ECX = 00000000
EDX = 000003f8
EBX = 00000000
ESP = 00007c00
EBP = 00000000
ESI = 00000000
EDI = 00000000
EIP = 00000000
```

### ch 3.12-3(select)

#### preparetion

`  make run-io `

#### command

``` 
./px86 -q select.bin 
h
w
q
```

#### output

```
>h
hello
>w
world
>q


end of program.

EAX = 00000071
ECX = 00000000
EDX = 000003f8
EBX = 00000000
ESP = 00007c00
EBP = 00000000
ESI = 00007c4f
EDI = 00000000
EIP = 00000000
```


### ch 4.2(bios)

#### preparetion

`  make run-bios `

#### command

```
 ./px86 -q subroutine32.bin
```

#### output

```
hello, world


end of program.

EAX = 00000e00
ECX = 00000000
EDX = 00000000
EBX = 0000000a
ESP = 00007c00
EBP = 00000000
ESI = 00007c31
EDI = 00000000
EIP = 00000000
```

#### Supplemental comment

You will see the sentence "hello, world" is colored in green.

### Cleaning

#### command

` make clean `
