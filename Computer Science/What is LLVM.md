# What is LLVM?
---

LLVM is a backend compiler which can generate machine code. Nowadays most people use language specific frontend compilers (e.g. clang), to convert code into an intermediate representation (IR) for LLVM, and LLVM then compiles IR into machine code. The advantage is that if you have a new programming language, you only create your language specific compiler which would output LLVM compatible IR and not worry about x86/x64/arm/other platforms since LLVM does that. So sometimes you might hear people saying that some language (e.g. Rust) or its compiler are LLVM compatible - there they mean that the compiler of the language can transform the code to IR which could be fed to LLVM.
When compiling C/C++ code some people use GCC instead of clang to generate IR. But GCC can also be used to generate the final machine code for a specific architecture (e.g. x86)

http://www.aosabook.org/en/llvm.html
