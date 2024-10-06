# Ret2Me
## Generic Explotaion Method for 32bit(and mayb 64bit) that works every time! 
# 32bit 
so let's look on that code : [Code From The Linux Kernel Repo](https://github.com/torvalds/linux/blob/fc20a3e57247e21e1bd582f604b20bf898f7d111/tools/testing/selftests/x86/vdso_restorer.c#L66)
this code demonstrate how the signal mechainsem works on 32bit
and we have this comment : 
```
 * vdso_restorer.c - tests vDSO-based signal restore
 * Copyright (c) 2015 Andrew Lutomirski
 *
 * This makes sure that sa_restorer == NULL keeps working on 32-bit
 * configurations.  Modern glibc doesn't use it under any circumstances,
 * so it's easy to overlook breakage.
 *
 * 64-bit userspace has never supported sa_restorer == NULL, so this is
 * 32-bit only.
```
so the original `rt_sigreturn` could lay on the vdso defaultly (libc would override it anyway ... ) . 
so we can use this gadget(and guess the vdso address) 
and then do srop shit 
then we can put in eax - 26 (ptrace) and put in the request - ebx (PTRACE_TRACEME) and then wait for us to create unprivlleged process that 
will be the debugger of the voulntly debugge(the privlleged process) we have stack bof on!. 
and then we will jump again to the address of the int 0x80 we already found in the rt_sigreturn-trampoline gadget we found 

some images 
<img width="912" alt="image" src="https://github.com/user-attachments/assets/4ddf537f-ec22-464a-8fe0-70e43afcf375">
<img width="1151" alt="image" src="https://github.com/user-attachments/assets/d1389cf3-08a5-4aa2-b285-38f94cc0f75f">
<img width="1460" alt="image" src="https://github.com/user-attachments/assets/b1599776-375b-48cf-af93-db550e0b7bd9">
<img width="831" alt="image" src="https://github.com/user-attachments/assets/07c4aec6-f418-44ec-bdd4-71387264a933">

and what about guessing the vdso address ? 
is it that simple ? 
<img width="730" alt="image" src="https://github.com/user-attachments/assets/0ac6b3d2-f6ba-419e-9fd9-c70d4fb08ce2">
<img width="316" alt="image" src="https://github.com/user-attachments/assets/1f654989-d2f1-41bd-a9aa-ff96ccd1da97">
<img width="928" alt="image" src="https://github.com/user-attachments/assets/4ca27e6b-2b4c-4c29-80d1-96f2f775fb03">
<img width="637" alt="image" src="https://github.com/user-attachments/assets/ac3163be-0d48-462c-b16c-2c881754eee6">


# 64bit?
it might be super super diffcult and mayb not pissbole -> on my machine is fairly easy: 
## Gadgets 
```
0x0000000000000ad7: pop rbx; pop r12; pop rbp; ret;
0x0000000000000c7f: xor eax, eax; ret;
0x0000000000000d12: leave; ret;
0x00000000000006fe: mov eax, 0xffffffff; ret;
0x000000000000086f: pop rsi; pop r15; pop rbp; ret;
0x000000000000086a: pop r12; pop r13; pop r14; pop r15; pop rbp; ret;
0x000000000000073b: add rax, rsi; ret;
0x0000000000000c7b: syscall; ret;
```

so let's organzie the gadgets : 
```
[1] 0x000000000000086f: pop rsi; pop r15; pop rbp; ret;
[2] 0x00000000000006fe: mov eax, 0xffffffff; ret;
[3] 0x000000000000073b: add rax, rsi; ret; // we have in rax 0x0f
[4] 0x0000000000000c7b: syscall; ret;
// call to srop ...
```
a bit more complicated but we are able to call srop ... 

the randomiztion is also a bit tricky -> on x64 is pretty rough to guess it right -> we have 18bit random factor .

[CVE](https://bugzilla.kernel.org/show_bug.cgi?id=89591)

so it is a bit hard but possible .


## SROP : 
with srop we can control everything and we can find the syscall opcode in the vdso memory in any bitness(syscall/int 0x80)
so we can call it and win! just like kings! 
