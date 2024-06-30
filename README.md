# 22110064, Nguyễn Minh Phú Quang
# Lab Exercise: Buffer Overflow Attacking Techniques
## 1. Bof1.c
First I installed the program bof1.c into nano 
<img src='https://github.com/quangg1/L_a_b1/assets/89339772/f1fa3e93-c1ec-442a-855d-a7b81a8cbfa3' alt='Installed' >

Then I run the command line "gcc -g bof1.c -o bof1.out -fno-stack-protector
-mpreferred-stack-boundary=2" to compile the program and as shown in the picture there is no error
<img src='https://github.com/quangg1/L_a_b1/assets/89339772/d0454ef3-6487-426d-adcf-f14f46ab9e02' alt='Installed' >
As we can see that gets() reads input from stdin and stores it in the provided buffer without any bounds checking. This can lead to buffer overflow if the input exceeds the size of the buffer, potentially causing crashes or allowing malicious code execution. So how did i fix it?
<img src='https://github.com/quangg1/L_a_b1/assets/89339772/0ae20fa5-ab1d-49db-a50a-232773fdc41c' alt='Re-code' >

Now there is no error in the code (***fgets() is safer because it allows you to specify the maximum number of characters to read, preventing buffer overflow***).

Next, in order for the program to print the result "Congratulation!", we
must find the address of the "**secretFunc**" function, so I go to gdb find the address of the function, and this is the result:

<img src='https://github.com/quangg1/L_a_b1/assets/89339772/b8095b61-eb9b-4cad-8c33-48ffd5d54d37' alt='Re-code' >

We see the function "**secretFunc**" has the address:0x080484fb

Then I exited gdb and entered the command "./bof1.out $(python -c “print('a'*210+'\xfb\x84\x04\x08')”)" to see the output of the program
<img src='https://github.com/quangg1/L_a_b1/assets/89339772/3b6b2592-8dc9-40af-adba-6cd7ee5ac649' alt='Re-code' >

And the result is:


<img src='https://github.com/quangg1/L_a_b1/assets/89339772/ee3bde4f-35ef-45b5-a66b-a06ae1726aef' alt='Result' >

Command explanation:
- “ python -c “print ‘a’*204 : In this statement I assign the buffer
memory area with a value consisting of 204 letters 'a'
- “+’\xfb\x84\x04\x08’” : In this statement, I add the address of the
change function but write the opposite because in the memory area, the low
byte is first, the high byte is after. (*little Edian rule*).

Printing 204 characters a: Because the Buffer occupies 200 memory cells in the memory area + 4 memory cells of the frame pointer.
## 2.Bof3.c
First I installed the program bof3.c into nano.
<img src='https://github.com/quangg1/L_a_b1/assets/89339772/23f0571c-bca0-4e29-8619-5b9e8bb9e1e4' alt='Install' >

Then I run the command line "gcc -g bof3.c -o bof3.out -fno-stack-protector -mpreferred-stack-boundary=2" to compile the program and as shown in the picture there is no error

<img src='https://github.com/quangg1/L_a_b1/assets/89339772/d913ab9d-ca96-441a-832d-f35d9cb6b6ef' alt='Check' >

In order for the program to print the result "Congrat", we must find the address of the "sup" function, so I go to gdb and use the "disas sup" command to find the address of the function.

<img src='https://github.com/quangg1/L_a_b1/assets/89339772/75ef7baf-1a40-408f-b025-ce33bbf9ea94' alt='Check' >

As we can see, after the terminal runs, the address of sup on stack is 0x0804846e.
Then I exited gdb and entered the command "./bof3.out $(python -c “print(‘a’*136+’\x6e\x84\x04\x08’)”)" to see the output of the program

<img src='https://github.com/quangg1/L_a_b1/assets/89339772/1db28373-be37-4991-8db8-3188b849d468' alt='Result' >

Command explanation:
- “ $(python -c “print(‘a’*136 “ : In this statement I assign the buffer memory area with a value consisting of 136 letters 'a'.
- “+’\x6e\x84\x04\x08’” : In this statement, I add the address of the '*sup*' function but write the opposite because in the memory area, the low
byte is first, the high byte is after. (*little Edian rule*).

<img src='https://github.com/quangg1/L_a_b1/assets/89339772/568bc21c-4e27-4463-b353-fe6ea0c28b1f' alt='STack frame' >

Explanation of printing 136 characters a: Because the Buf occupies 128
memory cells in the memory area + 4 memory cells of the var variable + 4
memory cells of the frame pointer. 

And the last result of bof3 terminal is "Congrat".
## 3.Bof2.c
First I installed the program bof2.c into nano 

<img src='https://github.com/quangg1/L_a_b1/assets/89339772/0a0186df-e110-43d2-aef9-50b8330dff1f' alt='Install' >

Then I run the command line "gcc -g bof2.c -o bof2.out -fno-stack-protector -mpreferred-stack-boundary=2" to compile the program and as shown in the picture there is no error

<img src='https://github.com/quangg1/L_a_b1/assets/89339772/fb9bcecc-d2a9-418f-a803-969e8355cefa' alt='No error' >

In this terminal we have 2 options:

*-Case1 : print "You are on the right way!"*
In order for the program to print the result, I genarate a
buffer overflow supported by fgets with 40byte and 4 random bytes. I entered the command “python -c print ‘a’*40 + \x20\x84\x04\x08’”
| ./bof2.out”

<img src='https://github.com/quangg1/L_a_b1/assets/89339772/d2d9b0e3-fe31-40f3-887e-5c0548386ed3' alt='on the right way' >

Command explanation:
- “ python -c “print ‘a’*40 : In this statement I assign the buffer memory area with a value consisting of 40 letters 'a'.
- “+’\x20\x84\x04\x08’” : In this statement, I added random bytes tochange the value of the check variable, thus satisfying the condition of the first if line.

*-Case2 : print "Yeah!You win!"*
In order for the program to print the result, I generate a
buffer overflow supported by fgets with 40byte and 4 bytes with pattern 
“deadbeef”
I entered the command “python -c print ‘a’*40 + \xef\xbe\xad\xde’” | ./bof2.out”

<img src='https://github.com/quangg1/L_a_b1/assets/89339772/f811e571-4bfb-4ff0-be3f-2425c585bf1b' alt='You win' >

Command explanation:
- “ python -c “print ‘a’*40 : In this statement I assign the buffer
memory area with a value consisting of 40 letters 'a'.
- “+’\xef\xbe\xad\xde’” : In this statement, I added 4 bytes to change the value of the check variable so that its value will always be "deadbeef", and in the memory, it should be written in *little Edian rule*(, the low byte is first, the high byte is after).
