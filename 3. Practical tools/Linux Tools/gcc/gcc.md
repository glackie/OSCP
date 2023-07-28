GCC stands for GNU Compiler Collections which is used to compile mainly C and C++ language. It can also be used to compile Objective C and Objective C++.

The most important option required while compiling a source code file is the name of the source program, rest every argument is optional like a warning, debugging, linking libraries, object file etc. The different options of _gcc_ command allow the user to stop the compilation process at different stages.


gcc [-c|-S|-E] [-std=standard]

**Example:** This will compile the _source.c_ file and give the output file as _a.out_ file which is default name of output file given by gcc compiler, which can be executed using **./a.out**

gcc source.c

	**-lm :** This command link _math.h_ library to our source file, -l option is used for linking particular library, for math.h we use -lm.

	 -shared                  Create a shared library.





