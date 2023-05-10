# TIPS FOR REVERSE-ENGINEERING MALICIOUS CODE

Cheat sheet for reversing malicious Windows
executables via static and dynamic code analysis.

|Overview of the Code Analysis Process|
|-------------------------------------|
|Examine static properties of the Windows
executable for initial assessment and triage.|
|Identify strings and API calls that highlight the
program’s suspicious or malicious capabilities.|
|Perform automated and manual behavioral
analysis to gather additional details.|
|Emulate code execution to identify
characteristics and areas for further analysis.|
|Use a disassembler and decompiler to statically
examine code related to risky strings and APIs.|
|Use a debugger for dynamic analysis to examine
how risky strings and API calls are used.|
|If appropriate, unpack the code and its artifacts.|
|As your understanding of the code increases, add
comments, labels; rename functions, variables.|
|Progress to examine the code that references or
depends upon the code you’ve already analyzed.|
|Repeat steps 5-9 above as necessary (the order
may vary) until analysis objectives are met.|
