# TIPS FOR REVERSE-ENGINEERING MALICIOUS CODE

Cheat sheet for reversing malicious Windows
executables via static and dynamic code analysis.

|Overview of the Code Analysis Process|
|------------------------------------------|
|1. Examine static properties of the Windows
executable for initial assessment and triage.|
|2. Identify strings and API calls that highlight the
program’s suspicious or malicious capabilities.|
|3. Perform automated and manual behavioral
analysis to gather additional details.|
|4. Emulate code execution to identify
characteristics and areas for further analysis.|
|5. Use a disassembler and decompiler to statically
examine code related to risky strings and APIs.|
|6. Use a debugger for dynamic analysis to examine
how risky strings and API calls are used.|
|7. If appropriate, unpack the code and its artifacts.|
|8. As your understanding of the code increases, add
comments, labels; rename functions, variables.|
|9. Progress to examine the code that references or
depends upon the code you’ve already analyzed.|
|10. Repeat steps 5-9 above as necessary (the order
may vary) until analysis objectives are met.|


|Common 32-Bit Registers and Uses|
|------------------------------------------|
|EAX  Addition, multiplication, function results|
|ECX  Counter; used by LOOP and others|
|EBP  Baseline/frame pointer for referencing
function arguments (EBP+offset) and local
variables (EBP-offset)|
|ESP  Points to the current “top” of the stack;
changes via PUSH, POP, and others|
|EIP  Instruction pointer; points to the next
instruction; shellcode gets it via call/pop|
|EFLAGS Contains flags that store outcomes of
computations (e.g., Zero and Carry flags)|
