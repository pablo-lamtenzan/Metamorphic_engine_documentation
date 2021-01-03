# How to implement my metamorphic virus ?

## What my virus need:
A metamorphic virus must protect itself against static and dynamic analysis. That means the virus must resist signature-base analysis, disassambly, debbuging, emulation and (if i achive to develop it) anomally-based detection (Heuristic) and Hidden Markov Models-based detection. For achive that resistance, the resulting virus must have a hight degree of metamorphism. Metamorphism means that all the files infected by the same virus will be different. Avoiding patterns the virus avoid to be detected. This is how (the 02/01/2021) i think the virus must be develloped to achieve all of that.

// TO DO: Padding can be used to avoid HMM detection check in "METAMORPHIN WORM THAT CARRIES ITS OWN... " p 28 + p31 there are very interesting stuff

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

#### 2.2) Metamorphic ofuscation
After the study of metamorphic viruses and their engines i can conclude that (the 02/01/2021) there are 5 essentials methods used in metamorphism offuscation. I think is very reallistic to use the 5 in my future metamorphic virus implementation.

##### 2.2.1) Register Swap
This tecnique mutates the virus body in the simplest way: swapping the operand registers used by others that will remain the same. For example: 
```
pop ecx -----> pop ebx
```

##### 2.2.2) Subroutine Permutation
This tecnique consist in reorder the virus subroutines. If a virus has N subroutines it can achieve to N-fragtorial different combinations using this tecnique. This tecnique is useful combinated with other tecniques, using a simple short-signature matcher is easy to detect a virus that only perform this offuscation method.

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

##### 2.2.3) Garbage Instruction Insertion
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

##### 2.2.4) Instruction Substitution
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
                     
##### 2.2.5) Transposition
This tecnique consist to transpose instructions, that means that the instructions that not depend from the other instructions or their order to be executed  can be re-ordered. Example:
```
add op1, op2 --------> add op3, op4
add op2, op4 --------> add op1, op2

------------------------------------

MOV %EAX, 0F ---------> ADD ESI, EBX
PUSH %ECX ------------> PUSH %ECX
ADD %ESI, %EBX -------> MOV %EAX, 0F
```

##### More tecniques
- Recursion iteration (using recursive solutions over iterating)
- Morphing of instruction in pipeline (changing instructions already fetched)
- Execute flow altering (change the execute flow (ex: executing in the heap))
- Opcode shifting (calling/jumping into midle instruction)

##### 2.2.6) Formal Grammar Mutation
The formalization of many existing tecniques is named Formal Grammar Mutation where any instruction can be followed by any other instruction but appying some grammar rules it can perform hight degree of metamorphism.

##### 2.2.7) Morph points

A morph point is a locate space in the code that has been designed to morphed. There are conceptually two types of metamorphic engines: soldiers and scouts. Soldier takes direct orders and the program must inform them of the exact location (via relative/absolute addr) of the modification targets. Scouts on the other hand, find the morph points using a search algorithm. Both are useful and have their unique advantages over the other. Soldiers are capable to precise modifications, such as reversing the conditions of a single branch stayment while leaving the others alone. A scout implementation requires less work to integrate because the developper does not have to inform to the metamorphic engine of new porth point location if the compiler relocates them after each compilation. But using a scout could be more dificult (cause it can damage protected statments != morph points)

There are several types of morph points for opcode shifting, the stealth is a way to clasify them, a stealty morph point for opcode shift absorbs one or more subsequent instructions without causing riple effects in the disassembly. And example of a stealthy morph point would be a one byte opcode shift that comsumes a one-byte ```PUSH``` instruction. A semi-stealty morph point does not absorb instructions, the opcode shift does not align perfectly the instruction boundary of one of the original instructions. Another aproach is clasify by morph point address consitency. In a homogenious group all the morph points have to same implementation. A developper likely does not want identical morph points protecting a program, because the atacker can devellop a tool that automatically find and destroy them. To avoid this consistency, a developer can choose a heterogenious group of form points. The metamorphic engine could also randomly alter the morph points implementation durring runtime.

To shift opcodes, the program must meet two criteria.
- The morph point construct never allow the false opcode to execute (program crashes if it happends, the morph point is exposed
- The morph point must be beliveble to the disassambler

``` 
// Taken from a447014.pdf p 53 and recommented to read !

Morph points execution for 1000M iterations

Morph point implementation    |     Average execution time
                              |
JUMP ro Label                 |     7.003ns
Opaque Branch                 |     20.053ns
Jump address calculation      |     23.053ns
Function Call                 |     20,547ns
```

MUST READ a447014.pdf starting at p52 to end this section!!!

#### 3) More offuscation tecniques

Metamorphism is a very powerfull ofuscation tool but need to have more offuscation tools to be useful like anti-disasembly or anti-debugging for protect the virus from static and dynamic threats.

##### 3.1) Anti-Assembly

Anti-assembly tecniques defend software against static analysis by an attacker, the defender can apply a variety number of tecniques to protect itself. Some methods used are unique like encryptation or offuscation, encrytation make a program unreadable until is not decrypted and offuscation the unencrypted program virtally unreadable by dramatically extend its complexity.

Dissamblers operate in various ways to provide a correct program disassembly. Linear Sweep disassembler sequencially disassembles code instructions (SoftICE, Windgb) on ther other hand, Recursive Traversal disassembler actually analalyse the instructions themselves to determine the flow control and where disassembly should resume (OllyDgb, Data Rescue IdaPro). 

Lets see with one exemple the difference between both:

The exemple consist on inline assembly and a print stayment, the program perform an opcode shift (code shift introduces data bytes into the work flow and include that program logic to ensure the processor never executes the data bytes). The inserted data bytes serves as false opcode prefixes. The disassembler mistakenly these data-bytes are legitimate prefix for another instruction. Any remaning bytes needed for the false instruction are shifted from the real instruction. This confuse the disassembler, which causes them to a series of mangled instructions. The inline assembly (```_asm```) block both the logic and the data byte.
```
{
  _asm
  {
    jmp L1    ; Logic to "skip" data byte
    _emit 0x0 ; Inserted data byte
    L1:
  }
  printf("%s\n", "Hello world!");
  return (0);
 }

 // The inline assembly code instructs the processor to jump over a data byte the (0x0 generated by _emit) to the label L1
 // since nothing follows the label, the control flow executes the C code and executes printf instruction
 ```
 
 Both types of disassembler displays results drastically differents.
 
 ```
 Linear Sweep (Windgb) :
 00401000  EB 01           jmp 00401003
 00401002  00 68 D8        add byte ptr [eax-28h], ch
 00401005  70 40           jo 00401047
 00401007  00 E8           add al, ch
 00401009  06              push es
 0040100A  00 00           add byte ptr [eax], al
 0040100C  0083 c40433C0   add byte ptr [ebx-3FCCFB3CH], al
 00401012  C3              ret
 
 // As we can see Windgb misses 4 instructions and sincronize in the return instruction
 
 Recursive Traversal (OllyDgb):
 00401000  EB 01          jmp short 00401003    ; Logic
 00401002  00             db 00                 ; data-byte
 00401003  68 D8704000    push 004070D8         ; (printf-
 00401008  E8 06000000    call 00401013         ; instr.)
 0040100D  83C4 04        add, esp 4
 00401010  33C0           xor eax, eax
 00401012  C3             retn
 
 // Is possible to fool recursive traversal disassemblys ussing two equaly viable options or an abnormal program execution flow
 ```
 
 ##### 3.2) Code offuscation
 
 The quality of the offuscations tactics can be mesurated by potency, resistence (against automated desoffuscator) , stealth (how is difficult to separe offuscate code form not offuscated) and cost (in the execution time). There are 4 categories of offuscation transformation: layout, data, control, preventive having each one their potency, resistance, stealth and cost. The goal of this categories are to mesure the level of offuscation. 
 
 This is interesting but i've writting this for educationals purposes and i need others humans to understand my work ...

##### 3.3) Anti-debuging

The goal of anti-debuging is to prevent reverse-engering software throught dynamic analysis. Degugger execute code within the debugger's controlled environment. Two basic features that debuggers offer are the ability to set a breakpoint where the execution program is interrumpted and where the dubugger regain control and the hability to step throught the program one instruction at a time.

There are 2 types of breakpoints: software and hardware, when setting a software breakpoint the debugger usally replaces the first byte of an instruction in memory with a breakpoint interrumpt (INT 3) ```0xCC``` on a Intel procesor. When the process sees the ```0xCC``` it generates an interrumpt that the debugger catches, once received the debugger replaces the ```0xCC``` byte by the original byte and pausses the program execution for the user. For hardware breakpoints, the processor itself will hanlde them via the ```debug registers```. Since the processor manages the breakpoint the bedugger has not to set breakpoint interrumpt in the process memory space. However the process can manage a limited number of hardware breakpoints due limitations (limited number of debug registers).

The debugger provides the fuctionality to step throught the program by enabling the processor trap flag (makes the processor to generate a single-step interrumpt after executing each instruction). The debugger can catch these instructions and regain the control allowing the user analyse the state of the debugge program. 

Many anti-debuggers protections try to a cause the debugger to ```lose state```. As a debugger executes a program it must keep track of the program state (variables, registers values, stack content, ect...). However the debugger uses these ressources too, because the operating systhem shares this ressources among several tasks (multitasking). Since the debugger can not query the the systhem state while the target process (of the dbg) executes, it must rely on the state information that is has gathered. Anti-debug tecniques include any methods that debugger to lose or change any of its state information.

###### 3.3.1) Debugger interrump manipulation

Malware application commonly hooks interrumpt causing debugers to lose execution code's context. They achive that by loading themselves into memory and modifiying the interrumpt vector table (IVT) to point themselves instead of the normal interrumpt handler. This places the virus at the begining interrumpt call chain for that particular interrumpt. Viruses commonly hooks the single-step (INT 1) and breakpoint (INT 3) interrumpt. Other viruses overwrite the interrumpt handlers that debuggers uses normally for interrumpt return (IRET) instructions ultimately causing the debugger to lose state. Another defence could be desable the keyborad or store ressources in the keyborad buffer ...

###### 3.3.2) Guardian againt debugger breakpoint

Other malware application uses checksum to to verify that the code executing in memory remain unchanged. The program calculate the a checksum of malware code and store it. Running the code in a debugger changes the code by inserting inserting software breakpoints (INT 3) ```0xCC``` of the first byte of assembly opcodes. he debugger must keep track of the replaced byte to continue execution correctly. Even thought replaced it replaced it replaces a byte of a instruction opcode, the debugger displays the correct byte to the user to readibilaty purposes. This additional byte changes the checksum in memory when the malware application attemps to verify its integrity. Viruses can use hardware debug registers (```DR0```-```DR7```) on Intel architectures to cause problems to some debugers. Indentically some virus are self-annealling, witch means the can detect and correct small errors (correct or disable breakpoint and thereby exibit anti-debugging characteristics (see Yankee Doodle virus)

###### 3.2.3) Observing and using debuger ressources

Another trick is to simply observe the top of the stack. Debuggers often push tracee information into the stack dutin their execution process. If a virus detect debugger information on the stack it may council itself by letting the infected program function normally. In addition of obserting the stack, some virus uses the stack to build their decryptation key or decrypt their program. If the debugger manipulates the stack the virus can no decrypt itsef so not exposed.

###### 3.2.4) Debugger detection

A dirrect aproach to invoque an operating systhem application fuction (hwo definition depends of architecture) to return a boolean indicatign wether the current program is running into a debugger. Althought to implement, this strategie is easy to detect by searching for key string. However using checksums of API functions instead of the fuction name itself, the malware program can be offustaced and avoid key string searches. Malware can also scan trough the registry for debugger keys and if find one act in a different manner like not executing all its code (to confuse).

###### 3.2.5) Debugger offuscation

Other anti-debug tecniques don't use hooking, detection or ressource consumption. Many debugger can not follow a program during exeption handler execution, which is another state where debugger lose information and fail. Obfuscating the file format or the entry point can confuse debuggers that work only with standard formats. In short any tecnique that causes to tracee the wrong execution (or not follow the) path sould result in the debugger losing state and failing.

##### 3.3) Anti-emulation

Emulation mimics the program execution. All modeling is necessarily incomplete, but an emulation is a low fidelity representation that focuses on primaly on modeling program behavior (not functionality). Simulation still imperfect, are hight fidelity representation of program execution on another plateform. Since is an incomplete model of program fuctionality, many oportunities exits to fool emulator.

Anti-emulation tactics commonly uses "obscure functions" Many emulators do not model such fuctions and some even omit them entereky duting execution. An example could be coporcessor , MMX and undodumented CPU instructions. Another way do prevent emulators is to use denial-of-service (DOS) attacks emulator. Just need to use so many resources (like a backtraking decrytion, complex loops for build the key, etc ...) 

##### 3.5) Anti-Heuristics

Anti-virus developers developed heuristics scaners to detect new viruses without new virus signatures. As with intrussion detection systhems, the developpers chose a sensitivity low level enought to detect new viruses but hight enoght to minimize false positives. Commertial anti-virus products commonly uses heuristics such as the file infection area, because many viruses tend to infect the begining or the end of the files. However a scaner can not follow other infection startegies such as ```cavity``` or ```overwriting infections```.

##### Anti-Goat (Anti-Bait)

A goat file is file containg only ```NOP``` (No-Operate) instruction, is used to trick the virus to infect it. Since file is infected is possible to analyse the virus infection routine. To develop the anti-goat tecniques the virus need an heuristic analyser to select which file save to infect. 
