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
<img width="1151" alt="image" src="https://github.com/user-attachments/assets/d1389cf3-08a5-4aa2-b285-38f94cc0f75f">
<img width="1460" alt="image" src="https://github.com/user-attachments/assets/b1599776-375b-48cf-af93-db550e0b7bd9">
<img width="831" alt="image" src="https://github.com/user-attachments/assets/07c4aec6-f418-44ec-bdd4-71387264a933">

and what about guessing the vdso address ? 
is it that simple ? 
<img width="730" alt="image" src="https://github.com/user-attachments/assets/0ac6b3d2-f6ba-419e-9fd9-c70d4fb08ce2">
<img width="316" alt="image" src="https://github.com/user-attachments/assets/1f654989-d2f1-41bd-a9aa-ff96ccd1da97">



# 64bit?
## Gadgets 
```
rbp,rbx,r12
0x0000000000000ad7: pop rbx; pop r12; pop rbp; ret;
0x0000000000000c7f: xor eax, eax; ret;
0x0000000000000d12: leave; ret;
0x000000000000086f: pop rsi; pop r15; pop rbp; ret;
0x000000000000086a: pop r12; pop r13; pop r14; pop r15; pop rbp; ret;
0x000000000000073b: add rax, rsi; ret;
0x0000000000000c7b: syscall; ret;
```