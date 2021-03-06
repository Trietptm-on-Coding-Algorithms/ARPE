
# ARPE
**ARPE** is an **A**uto-**R**eplicant **P**olymorphic **E**ncrypted program written in x86-64 assembly language (NASM, Windows ABI).  
It contains a simple example of a polymorphic engine.  

## How it works

**ARPE** is composed by two parts:  
1. the decryption function  
2. the polymoprhic engine  

When **ARPE** is executed it decrypts the polymorphic engine and replicates itself. The program encrypts/decrypts 16 bytes at a time because the bytes to encrypt/decrypt are placed in the registers _eax_, _ecx_, _edx_ and _ebx_. When it replicates it generates a new encryption function randomly using only 12 invertible instructions applied to the four general pourpose registers. The decryption function is also generated by simply reversing the instructions of the encryption function (e.g. _add eax, ecx_ <=> _sub eax, ecx_). So the program encrypts the next generation copy of itself with the new encryption function and places the corresponding decryption function. Then it saves the executable file (in the same directory where it is placed).  

For example, here is a portion of a randomly generated encryption function ...  
```x86asm
0000000000401B0E | F7 D3                              | not ebx                                 |
0000000000401B10 | 81 EB 4E 34 00 61                  | sub ebx,6100344E                        |
0000000000401B16 | C1 CA 05                           | ror edx,5                               |
0000000000401B19 | 31 C1                              | xor ecx,eax                             |
0000000000401B1B | FF C2                              | inc edx                                 |
0000000000401B1D | C1 C3 15                           | rol ebx,15                              |
0000000000401B20 | 81 F3 03 93 D3 7D                  | xor ebx,7DD39303                        |
0000000000401B26 | 01 D9                              | add ecx,ebx                             |
0000000000401B28 | C1 C0 0D                           | rol eax,D                               |
0000000000401B2B | FF CB                              | dec ebx                                 |
0000000000401B2D | F7 D1                              | not ecx                                 |
0000000000401B2F | 29 C2                              | sub edx,eax                             |
0000000000401B31 | F7 D3                              | not ebx                                 |
0000000000401B33 | 81 F1 F3 42 CF 02                  | xor ecx,2CF42F3                         |
0000000000401B39 | 01 D0                              | add eax,edx                             |
0000000000401B3B | C1 C3 1D                           | rol ebx,1D                              |
```
... and here is the corresponding portion of the decryption function.  
```x86asm
00000000004016CC | C1 CB 1D                           | ror ebx,1D                              |
00000000004016CF | 29 D0                              | sub eax,edx                             |
00000000004016D1 | 81 F1 F3 42 CF 02                  | xor ecx,2CF42F3                         |
00000000004016D7 | F7 D3                              | not ebx                                 |
00000000004016D9 | 01 C2                              | add edx,eax                             |
00000000004016DB | F7 D1                              | not ecx                                 |
00000000004016DD | FF C3                              | inc ebx                                 |
00000000004016DF | C1 C8 0D                           | ror eax,D                               |
00000000004016E2 | 29 D9                              | sub ecx,ebx                             |
00000000004016E4 | 81 F3 03 93 D3 7D                  | xor ebx,7DD39303                        |
00000000004016EA | C1 CB 15                           | ror ebx,15                              |
00000000004016ED | FF CA                              | dec edx                                 |
00000000004016EF | 31 C1                              | xor ecx,eax                             |
00000000004016F1 | C1 C2 05                           | rol edx,5                               |
00000000004016F4 | 81 C3 4E 34 00 61                  | add ebx,6100344E                        |
00000000004016FA | F7 D3                              | not ebx                                 |
```

## License
This project is licensed under the **zlib** license.

