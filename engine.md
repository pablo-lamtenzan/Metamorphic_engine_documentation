# How to implement my metamorphic virus ?

## What my virus need:
A metamorphic virus must protect itself against static and dinamic analysis. That means the virus must resist signature-base analysis, disassambly, debbuging, emulation and (if i achive to develop it) anomally-based detection (Heuristic) and Hidden Markov Models-based detection. For achive that resistance, the resulting virus must have a hight degree of metamorphism. Metamorphism means that all the files infected by the same virus will be different. Avoiding patterns the virus avoid to be detected. This is how (the 02/01/2021) i think the virus must be develloped to achieve all of that.

// TO DO: Padding can be used to avoid HMM detection check in "METAMORPHIN WORN THAT CARRIES ITS OWN... " p 28 + p31 there are very interesting stuff

### 1) Fragmentated infection:
That means before infecting a file the virus will split the .text file section in some chunks and then cut itself in some chunks that fit with the previous file        chunks. It will insert itself ramdonlly between the file chunks without changing the behaviour of the infected file. Using fragmentated infection with a matamorphic virus means that the virus will first make the chunks of the file content and then calculate the limitations of the chunks he will make with itself. Before create the chunks of itself, first, the metamorphing engine must be called and perform matamorphism.

### 2) Metamorphic Engine:
The goal of a metamorphic engine is to never have 2 times the same signature using the same virus to infect a group of files. Using a complex metaphormic engine with a fragmentated infection can help to achieve a very hight degree of metamorphism. The global idea is:
- 1) Disassembly itself (the virus body)
- 2) Erase the the previous metamorphic ofuscation from the virus body
- 3) Recreate new metaphormic ofuscation
- 4) Re-Assembly the body
- 5) Inject itself

I've skkiped a lot of details that will be explained further.

#### 2.1) Disassembly / Re-Assembly
// to do

#### 2.2 Metamorphic ofuscation
After the study of metamorphic viruses and their engines i can conclude that (the 02/01/2021) there are 5 essentials methods used in metamorphism offuscation. I think is very reallistic to use the 5 in my future metamorphic virus implementation.

Here's an example taken from a study from the virus Zperm:
```
Instruction 4                         Instruction 2                                 Instruction 2
Instruction 5                         jmp                                           Instruction 4
jmp                                   garbage                                       jmp
gargage         -----------|\         Instruction 3     -------------|\             garbage
start:                        \       jmp                               \           Instruction 5
Instruction 1                 /       garbage                           /           jmp
Instruction 2   -----------|/         Instruction 5     -------------|/             start:
jmp                                   jmp                                           Instruction 1
garbage                               start:                                        jmp 
Instruction 3                         Instruction 1                                 garbage
jmp                                   jmp                                           Instruction 2
garbage                               Instruction 4                                 jmp
                                      jmp                                           grabage
```

##### 2.2.1 Register Swap
This tecnique mutates the virus body in the simplest way: swapping the operand registers used by others that will remain the same. For example: 
```
pop ecx -----> pop ebx
```

##### 2.2.2 Subroutine Permutation
This tecnique consist in reorder the virus subroutines. If a virus has N subroutines it can achieve to N-fragtorial different combinations using this tecnique. This tecnique is useful combinated with other tecniques, using a simple short-signature matcher is easy to detect a virus that only perform this offuscation method.

##### 2.2.3 Garbage Instruction Insertion
This one is the most powerfull. There are 2 types of garbage instructions: the instructions who will be executed and those who will be skipped. Combining both the virus can achieve to have unlimited possible versions of itself. Here an example:
```
Instruction       |     Operation

ADD %REG, 0       |     REG + 0
SUB %REG, 0       |     REG - 0
MOV %REG, %REG    |     REG = REG
OR %REG, 1        |     REG | 1
AND %REG -1       |     REG & -1
...               |     ...

-----------------------------------------

PUSH CX
... // code
POP CX

INC AX
... // code
DEC AX
```

Garbage instructions purpose is to not change any data or contents used by general purpose registers, their purpose is to increase the diversity of instructions.
While using this tecnique, the effect of the garbage instruction on the ```RFLAGS``` registers must be considereted! Control flow uses bits located in the ```RFLAGS``` registers to choose the which code path to take. A bad use of garbage instruction can affect the behaviour of the execution of the next control flow instruction.
Here's an example of simple dead instructions:
```
// just more example about dead code / gargabe / junk injection

1) ADD $0, %RAX
2) ADD $0, %RBX
3) ADD $0, %RCX
4) ADD $0, %RDX
5) SUB $0, %RAX
6) SUB $0, %RBX
7) SUB $0, %RCX
8) SUB $0, %RDX
9) XOR $0, %RAX
10) XOR $0, %RBX
11) XOR $0, %RCX
12) XOR $0, %RDX
13) AND %RAX, %RAX
14) AND %RBX, %RBX
15) AND %RCX, %RCX
16) AND $%RDX, %RDX
```

##### 2.2.4 Instruction Substitution
This tecnique consist to substitute a single instruction or a group of instructions by another instruction or group of intructions having the same functionality. This is one of the more complicated to implement cause the virus needs to find human synonyms. An example:
```
mov r1, r2 ------> push r1 [AND] pop r2
```
The instructions ```mov``` and  ```xor``` are perfect to be substituated, this is why the apear in abundace in the binaries. The substitution can be performed by using a substitution table:
```
Instruction       |   Equivalent                        | Action
                  |                                     |
MOV IMG, %REG     |   XOR %REG, %REG ADD IMG, %REG      | null
                  |                or                   |
                  |   XOR %REG, %REG SUB -IMG, %REG     | null
MOV %REG1, %REG2  |   XOR %REG2, %REG2 ADD %REG1, %REG2 | null
MOV %REG1, (%REG2)|   MOV $0, (%REG2) ADD %REG1, (%REG2)| null
MOV IMG, (%REG)   |   MOV $0, (%REG2) ADD IMG, (%REG2)  | null
XOR %REG, %REG    |   MOV $0, %REG
```
                     
##### 2.2.5 Transposition
This tecnique consist to transpose instructions, that means that the instructions that not depend from the other instructions or their order to be executed  can be re-ordered. Example:
```
add op1, op2 --------> add op3, op4
add op2, op4 --------> add op1, op2

------------------------------------

MOV %EAX, 0F ---------> ADD ESI, EBX
PUSH %ECX ------------> PUSH %ECX
ADD %ESI, %EBX -------> MOV %EAX, 0F
```

##### 2.2.6 Formal Grammar Mutation
The formalization of many existing tecniques is named Formal Grammar Mutation where any instruction can be followed by any other instruction but appying some grammar rules it can perform hight degree of metamorphism.


