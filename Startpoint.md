About the Red source code.
==========================

Introduction to the Red source code. 
------------------------------------

This documentation is started to save time for newcomers to get knowledge about the sources needed by the Red language project.
This way more programmers will be able to get up to speed in contributing.

This part documents the Red sources from the top down.

In the various documents in the subdirectories the sources will be viewed from the bottom up.

How do the parts come together to form the Red Language?
---------------------------------------------------------

The start: script red.r (374 lines)
----------

The Red source code starts in red.r

At this moment Red is not selfhosted, but dependent on a Rebol/Core executable. The main Red sources are therefore Rebol scripts.

Every Red source file starts with a Red header, a Red/System file equally starts with a Red/System header. The sources to compile Red scripts are Rebol script so these have a REBOL header. 
We skip the headers and what’s in them and move on to the code.

Now first thing red.r does is check if encap-fs? has a value. If not, the encapper code in /system/utils/encap-fs.r was not executed before and will be executed. 

Because red.r is a Rebol script we work inside a Rebol Core instance/Terminal so a second or next time red.r runs the result of  the encap-fs.r script will be in the memory already and encap-fs? will be true.

Encapper: script encap-fs.r (94 lines)

What does the encapper code do that is important to us? It says in the header it does ‘Encap the virtual filesystem’.
In encap-fs.r the value for ‘encap? will be set:
set 'encap? to-logic select system/components 'decap
In my Rebol terminal this results in a value of false.

It defines do-cache as do load %file.r, load file.r into memory and run its contents.
... (more to be added)

Back to the mainline red.r
After the encap check red.r checks if the context called red is defined and if it is at least an object.
If not the compiler script compiler.r is run to create the red context.
... (more to be added)

Compiler: script compiler.r (2447 lines)
What does the compiler code do that is important to us?
Is loads and runs the system/compiler.r script (3215 lines) which is the Red/System compiler.
The compiler creates the red context
... (more to be added)

Back to the mainline red.r
After the encap check red.r checks if the context called red is defined and if it is at least an object.
If not the compiler script compiler.r is run to create the red context.

Next the red.r script creates the redc context. When a context is created the first executable code found is executed. 
In this case the function main within this context will be run.
... (more to be added)

As a last step red.r calls the second step in compilation, the compilation of the generated Red/System code.

###
