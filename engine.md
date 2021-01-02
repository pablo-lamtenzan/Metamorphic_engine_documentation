# How to implement my metamorphic virus ?

## What my virus need:
A metamorphic virus must protect itself against static and dinamic analysis. That means the virus must resist signature-base analysis, disassambly, debbuging, emulation and (if i achive to develop it) anomally-based detection (Heuristic) and Hidden Markov Models-based detection. For achive that resistance, the resulting virus must have a hight degree of metamorphism. Metamorphism means that all the files infected by the same virus will be different. Avoiding patterns the virus avoid to be detected. This is how (the 02/01/2021) i think the virus must be develloped to achieve all of that.

// TO DO: Padding can be used to avoid HMM detection check in "METAMORPHIN WORN THAT CARRIES ITS OWN... " p 28

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
to do

#### 2.2 Metamorphic ofuscation
After the study of metamorphic viruses and their engines i can conclude that (the 02/01/2021) there are 5 essentials methods used in metamorphism offuscation. I think is very reallistic to use the 5 in my future metamorphic virus implementation.

##### 2.2.1 Register Swap
This tecnique mutates the virus body in the simplest way: swapping the operand registers used by others that will remain the same. For example: 
```
pop ecx -----> pop ebx
```

##### 2.2.2 Subroutine Permutation
This tecnique consist in reorder the virus subroutines. If a virus has N subroutines it can achieve to N-fragtorial different combinations using this tecnique. This tecnique is useful combinated with other tecniques, using a simple short-signature matcher is easy to detect a virus that only perform this offuscation method.

##### 2.2.3 Garbage Instruction Insertion
This one is the most powerfull. There are 2 types of garbage instructions: the instructions who will be executed and those who will be skipped. Combining both the virus can achieve to have unlimited possible versions of itself.

##### 2.2.4 Instruction Substitution
This tecnique consist to substitute a single instruction or a group of instructions by another instruction or group of intructions having the same functionality. This is one of the more complicated to implement cause the virus needs to find human synonyms. An example:
```
mov r1, r2 ------> push r1 [AND] pop r2
```

##### 2.2.5 Transposition
This tecnique consist to transpose instructions, that means that the instructions that not depend from the other instructions or their order to be executed  can be re-ordered. Example:
```
add op1, op2 --------> add op3, op4
add op2, op4 --------> add op1, op2
```

##### 2.2.6 Formal Grammar Mutation
The formalization of many existing tecniques is named Formal Grammar Mutation where any instruction can be followed by any other instruction but appying some grammar rules it can perform hight degree of metamorphism.


