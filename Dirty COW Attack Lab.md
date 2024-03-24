# Task 1: Modify a Dummy Read-Only File

## Create a Dummy File

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/a58f3ed4-f6ec-490c-bfa7-4c3d717b2111)

## Launching the attack

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/6356a7c0-1a1d-4c52-85b0-b81f6a566b0b)

We can see the 222222 has been replaced with ******

# Task 2: Modify the Password File to Gain the Root Privilege

created a new user called uday using sudo adduser uday

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/bb6f3d72-6cda-455b-8f2c-4746f50b756d)

Next, we edit the cow_attack.c file to change the file to /etc/passwd and the user id from 1001 to 0000

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/27fda239-334e-4d06-878a-c317b8fc46ba)

We can now compile it and run.

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/c146aae6-f41c-4683-ac0a-90ecd82222b1)

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/85b682a0-0c8d-4cc3-ac06-0127a556edbb)

On another tab – we verify if we are able to run this new user as root. 

![image](https://github.com/udayk01/Secure_Coding/assets/52235763/e03ea15a-6b82-4e43-bcf5-94b39429a6f2)

We can see this new user running as root. 







