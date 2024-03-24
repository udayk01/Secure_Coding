# Environment setup
##  1- Address Space Randomization.

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/efeb3bb8-1de4-4619-84c3-ca1c70b6c7b8)

##  2- The StackGuard Protection Scheme. 

The GCC compiler implements a security mechanism called "Stack Guard" to prevent buffer overflows. In the presence of this protection, buffer overflow will not work. You can disable this protection if you compile the program using the -fno-stack-protector switch. For example, to compile a program example.c with Stack Guard disabled, you may use the following command

```
gcc -fno-stack-protector example.c
```

## 3- Non-Executable Stack.

Ubuntu used to allow executable stacks, but this has now changed: the binary images of programs (and shared libraries) must declare whether they require executable stacks or not, i.e., they need to mark a field in the program header. Kernel or dynamic linker uses this marking to decide whether to make the stack of this running program executable or non-executable. This marking is done automatically by the recent versions of gcc, and by default, the stack is set to be non-executable. To change that, use the following option when compiling programs:

```
gcc -z execstack -o test test.c

gcc -z noexecstack -o test test.c
```

# TASK 1 - Finding out the Addresses of libc Functions

Extract the zip file and run make

we can see retlib.c copiled to root owned Set-UID program retlib

We create a badfile using **touch badfile** command, and start the GDB in quiet mode

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/062b5bc6-89e3-4a35-80a1-841a29f14a86)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/2822e2b2-86e3-4718-ad86-635af0404e02)

# TASK 2 -  Putting the shell string in the memory

We create an environment variable MYSHELL

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/c1c0cecf-60e1-4fbe-8c9c-0b926d12c8b0)

```
void main(){ 
  char* shell = (char *) getenv("MYSHELL"); 
  if (shell) 
    printf("%x\n", (unsigned int)shell); 
}
```
On compiling and running the above program, we get the address of /bin/sh.

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/7f31c530-a80b-4f99-8c88-376940f6ac4a)

We create printenv.c and compile using  **gcc -32 -g printenv.c -o prtenv**

-m32 because vulnerable program **retlib** is a 32-bit program, hence the address of **MYSHELL** env variable should also correspond to a 32-bit program.

-g used to en able the debugging symbols for GDB

Since we disabled Address Space Randomization executing the same program multiple times gives the same result

# TASK 3 - Launching the attack

system - 0xf7e0b370

exit - 0xf7dfded0

env var address - 0xffffd3ba

We open gdb using command **gdb -wq retlib_dbg**

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/b5e913b2-3681-40df-a8e8-546192b4a5f7)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/6ffc7b1b-1ef8-4d2b-b475-3ec8adf2522f)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/5a5a4352-aa30-4ca7-9207-178d0b9c45f5)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/16c74024-2308-4b4a-96ca-bd79bde56127)

Position of EBP pointer is obtained. We use this as the start of the attacker code, and increment 4 bytes to place each of the required system calls sand the appropriate arguments.

``` 
gedit exploit.py 
```

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/55a03953-87d3-462f-8c4e-3adacd3ee5d9)

After editing the **exploit.py**, we construct the badfile and execute the **./retlib** program.

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/3abdc41a-fbd5-4acb-acea-930b06243053)

## Attack variation 1

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/18c243de-04f2-4704-9947-a6981d75e09c)

When the address of the **exit** function is commented from the badfile, we can observe that when the program is terminating, a segmentation fault happened, because the Return Address of the next program was not available

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/3294d65b-a6cd-43f7-84c1-33752981d58b)

## Attack variation 2

When length of the program name is changed the offsets for the **/bin/sh** calculated and constructed in the **badfile** gets changed and hence when the program tries to move to the particular instruction, it shows command not found message.

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/62062ae6-de99-481b-ba52-e96d5c4fb3a7)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/ff435054-6090-4b82-a985-e0d99b45d516)







