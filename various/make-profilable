About the make-profilable function

In the ALTME R4 world Nenad announced this function.

"

I've been very busy since yesterday on a new tool for Red: I've built 
a proper REBOL code profiler! (I wonder why I haven't done that since 
a long time...). I went through the profiler scripts on rebol.org 
and couldn't one suitable for my needs or that works with complex 
code, so I wrote one. It is able to deal with complex code, all datatypes, 
recursive calls and it's very simple to use.


Here's a demo profiling Red compiler (output is properly aligned 
when monospace font is used):

-= Red Compiler =-
Compiling red/tests/test.red ...

...compilation time:     40 ms

Compiling to native code...

...compilation time:     10189 ms
...linking time:         60 ms
...output file size:     37888 bytes
>> profiler/report/time


Function                       Count      Elapsed Time         % 
of ET

------------------------------------------------------------------------

compile                        1          0:00:10.249          100.0

comp-dialect                   205        0:00:09.659          94.24

fetch-expression               7505       0:00:09.628          93.94

comp-word                      5668       0:00:08.209          80.09

fetch-into                     427        0:00:07.519          73.36

comp-assignment                597        0:00:07.049          68.77

run                            3          0:00:06.492          63.34

comp-context                   21         0:00:06.398          62.42

comp-with                      1          0:00:05.565          54.29

comp-expression                3172       0:00:04.479          43.70

ns-find-with                   24277      0:00:03.962          38.65

finalize                       1          0:00:03.327          32.46

comp-natives                   1          0:00:03.274          31.94

comp-func-body                 180        0:00:03.271          31.91

comp-call                      2775       0:00:02.732          26.65

comp-func-args                 2861       0:00:01.862          18.16

find-aliased                   9650       0:00:01.86           18.14

resolve-type                   8032       0:00:01.799          17.55

get-type                       10758      0:00:01.546          15.08

ns-prefix                      21765      0:00:01.518          14.81

check-enum-symbol              7509       0:00:01.241          12.10

comp-block                     283        0:00:01.05           10.24

comp-variable-assign           417        0:00:01.034          10.08


The overhead of the current implementation is about 5-6 times the 
program execution time, so it's very usable. I will publish it today, 
and I hope someone will pick it up to improve it and add features 
(like a GUI front-end for the reports, with sortable columns).

BTW, as I was thinking, the above compiler profiling confirms that 
aliases and namespaces support are quite costly currently (ns-* functions). 
So now I know where to improve the code for better performances (not 
that it matters for the users currently, but it matters to me as 
I need to run the whole test suite dozens of time each day, and with 
the addition of now Red compiler, it (and will) adds up to a lot 
of waiting time at the end of the day).

"

Why is the make-profilable function being defined as
set 'make-profilable func [][] 
instead of as
make-profilable: func [][] 


Pekr explains: "
'set example is one trick you can use in REBOL. Normally, 
when you enclose the function in the object/context, it is accessible 
via a path notation, e.g. context/make-profilable. When you use 'set 
aproach, it lives inside the context, but is exposed to the global 
context, so you can call it directly, or something like that :-)
"

  universe: context [
    set 'spees func [num][num * 10]
    cake: func [num][num * 10]
  ]
  spees 3

but cake does not work, cake has to be called like
  
  universe/cake 3

Conclusion: it is a tool for tracking and timing program performance
AND
It still needs to be improved, notice the remark by Nenad.


