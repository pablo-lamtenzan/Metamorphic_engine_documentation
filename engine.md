# How to implement my metamorphic virus ?

## What my virus need:
A metamorphic virus must protect itself against static and dinamic analysis. That means the virus must resist signature-base analysis, disassambly, debbuging, emulation and (if i achive to develop it) anomally-based detection (Heuristic) and Hidden Markov Models-based detection. For achive that resistance, the resulting virus must have a hight degree of metamorphism. Metamorphism means that all the files infected by the same virus will be different. Avoiding patterns the virus avoid to be detected. This is how (the 02/01/2021) i think the virus must be develloped to achieve all of that.

### 1) Fragmentated infection:
That means before infecting a file the virus will split the .text file section in some chunks and then cut itself in some chunks that fit with the previous file        chunks. It will insert itself ramdonlly between the file chunks without changing the behaviour of the infected file. Using fragmentated infection with a matamorphic virus means that the virus will first make the chunks of the file content and then calculate the limitations of the chunks he will make with itself. Before create the chunks of itself, first, the metamorphing engine must be called and perform matamorphism.

### 2) Metamorphic Engine:
The goal of a metamorphic engine is to never have 2 times the same signature using the same virus to infect a group of files. Using a complex metaphormic engine with a fragmentated infection can help to achieve a very hight degree of metamorphism. The global idea is:
- 1) Disassembly itself (the virus body)
- 2) Erase the the previous metamorphic ofuscation from the virus body
- 3) Recreate new metaphormic ofuscation
- 4) Assembly the ifself
- 5) Inject itself
I've skkiped a lot of details that will be explained further.
