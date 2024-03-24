# Environment Setup

## Turning of Countermeasure

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/b9bf611f-2e5a-436c-9cb4-d2f8751d2d47)

## The Vulnerable Program

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/8cefc99d-e5bc-49ae-b516-9826f89ddca6)

## Container Setup and Commands

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/b3267183-9fa1-44fe-95ae-ed180a751dee)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/4958aa53-4a10-437c-a2d9-5b02d7cd11c3)

# Task 1: Crashing the Program

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/e106139b-5aa4-4cdf-900d-752edb3a63c6)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/96f102b9-c623-4851-a2ba-adf1260b4df2)

Since the server did not print **Returned properly**, we can understand the program has crashed.

There may be null pointers, addresses pointing to protected memory, or virtual addresses that are mapped to physical memory. When a program tries to get data from an invalid address, it will crash.

# Task 2: Printing Out the Server Program’s Memory

## Task 2.A: Stack Data

- How many %x format specifiers do you need so you can get the server program to print out the first four bytes of your input?

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/35beeeaf-c772-41fc-9c52-9f8613b1dbe9)

By trial and error, we observe that after putting 11 %x, the next %s is having the entered user input

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/1a5b18e5-df3e-434e-8f0a-39d4489dfd99)

After the overflow, we are able to see the name Uday printed. 

## Task 2.B: Heap Data

From the server printout, we get the address of the secret message string as 0x080b4008. The address is placed on the stack (the buffer input), with the least significant byte stored in the highest address. Then, we place **63** **%x** s and finally use the **%s** to print out the current position of the **va_list** pointer.

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/f72ca0f8-72a5-437e-b388-9c329cb13593)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/14341cb9-e020-40d1-a48b-87dfe647a227)

# Task 3: Modifying the Server Program’s Memory

## Task 3.A: Change the value to a different value.

From the server printout, we get the address of the target variable as 0x080e5086. Similar to the previous task we place this address in the intial position of the stack. Then instead of printing the value of the current position of the va_list pointer, we reaplace the %s with %n, so that the number of characters printed so far by the printf statement would be updated.
Changing the value to a different value.

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/8de23c5b-20ed-48e8-9ef3-1d21aeb23a2a)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/502d54b2-dbdb-46ad-8306-161f43603a69)



























