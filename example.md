# tiny easy 
<img width="896" alt="image" src="https://github.com/user-attachments/assets/76db0c8d-024e-4fe7-beff-5b9f0d8eb753">

```
readelf -a tiny_easy
ELF Header:
  Magic:   7f 45 4c 46 01 01 01 00 00 00 00 00 00 00 00 00
  Class:                             ELF32
  Data:                              2's complement, little endian
  Version:                           1 (current)
  OS/ABI:                            UNIX - System V
  ABI Version:                       0
  Type:                              EXEC (Executable file)
  Machine:                           Intel 80386
  Version:                           0x1
  Entry point address:               0x8048054
  Start of program headers:          52 (bytes into file)
  Start of section headers:          0 (bytes into file)
  Flags:                             0x0
  Size of this header:               52 (bytes)
  Size of program headers:           32 (bytes)
  Number of program headers:         1
  Size of section headers:           0 (bytes)
  Number of section headers:         0
  Section header string table index: 0
```

```
checksec --file=./tiny_easy
RELRO           STACK CANARY      NX            PIE             RPATH      RUNPATH	Symbols		FORTIFY	Fortified	Fortifiable	FILE
No RELRO        No canary found   NX disabled   No PIE          No RPATH   No RUNPATH   No Symbols	  No	0		0		./tiny_easy
```

```
 0x8048000  0x8049000 r-xp     1000      0 /home/warp/googlectf/tiny_easy_folder/tiny_easy
 0xf7ff8000 0xf7ffc000 r--p     4000      0 [vvar]
 0xf7ffc000 0xf7ffe000 r-xp     2000      0 [vdso]
 0xfffdd000 0xffffe000 rwxp    21000      0 [stack]
```

```
 ► 0x8048054    pop    eax
   0x8048055    pop    edx
   0x8048056    mov    edx, dword ptr [edx]
   0x8048058    call   edx
```

```
  pwndbg> info functions rt
  All functions matching regular expression "rt":

  Non-debugging symbols:
    0xf7ffc570  __kernel_rt_sigreturn
```
