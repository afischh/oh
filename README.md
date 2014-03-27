﻿oh
==

Oh is a Unix shell written in Go.  The following commands behave as expected:

```
date
cat /usr/share/dict/words
who >user.names
who >>user.names
wc <file
echo [a-f]*.c
who | wc
who; date
cc *.c &
mkdir junk && cd junk
cd ..
rm -r junk || echo "rm failed!"
```

Oh uses the same syntax for code and data. This enables it to be easily
extended:

```
# The short-circuit and operator is defined using the syntax command.
define and: syntax e (: lst) as {
    define r False
    while (not: is-null: car lst) {
        set r: e::eval: car lst
        if (not r): return r
        set lst: cdr lst
    }
    return r
}
```

Oh is properly tail-recursive and exposes continuations as first-class values:

```
define label: method () as: return return
define continue: method (label) as: label label

# Print 1 to 100 using the continuation (return) returned by label.
define count: integer 0
define loop: label
if (lt count (integer 100)) {
    set count: add count 1
    write count
    continue loop
}
```

Oh exposes pipes, which are implicit in other shells, as first-class
values:

```
define p: pipe

spawn {
    # Save code to create a continuation-based while command. 
    define code '(syntax e (condition: body) as {
        define label: method () as: return return
        define continue: method (label) as: label label

        set body: cons 'block body
        define loop: label
        if (not (e::eval condition)): return '()
        e::eval body
        continue loop
    })

    # Now send this code through the pipe.
    p::write @code
}

# Create the new command by evaluating what was sent through the pipe.
define while2: eval: p::read

# Now use the new 'while2' command.
define count: integer 0
while2 (lt count (integer 100)) {
    set count: add count 1
    write count
}
```

Oh's environments are first-class and form the basis for its prototype-based
object system:

```
# Create a point prototype.
define point: object {
    # Private members are created with the define command.
    define x: integer 0
    define y: integer 0

    # Public members are created with the public command.
    public move: method self (a b) as {
        set self::x: add self::x a
        set self::y: add self::y b
    }

    public show: method self () as {
        echo self::x self::y
    }
}

# Create a new point by cloning the point prototype:
define o: point::clone
```

To compile and run oh you will need to install the C libraries ncurses and
libtecla. On Ubuntu 12.10, for example, do:

    sudo apt-get install libncurses5-dev
    sudo apt-get install libtecla1-dev

Then go get oh:

    go get github.com/michaelmacinnis/oh

Oh is released under an MIT-style license.


Background
----------

Despite multiple attempts to improve the Unix shell, its essential character
has remained largely unchanged.  Popular Unix shells have retained the look
and feel established by the Bourne shell and so share a strong family
resemblance.  The most successful shells in this family (bash, ksh, zsh) are
backward compatible with the Bourne shell.  Unfortunately, this backward
compatibility results in shells that are "inconsistent and confusing command
languages."[[18](#18)]

If you squint hard enough, the Unix shell looks very similar to other existing
languages.  So similar that many have attempted embedding the Unix shell in
an existing language.  The problem with this approach is that the result is
either "an uglier, and confusing, language"[[21](#21)] or a language that is
more cumbersome when used as an interactive shell.[[23](#23)]

Like es, fish and rc, oh retains the look and feel of the Unix shell but does
not aim for strict backward compatibility.  Oh makes substantial improvements
to the programming language features of the Unix shell by borrowing heavily
from the Scheme dialect of Lisp. Rather than attempting to embed a Unix shell
in scheme, oh was designed from scratch.


References
----------

Fexprs:

<a name="1">1. [Fexprs as the Basis of Lisp Function Application or $vau : The Ultimate Abstraction](https://www.wpi.edu/Pubs/ETD/Available/etd-090110-124904/unrestricted/jshutt.pdf)</a>

<br>

First-class Environments:

<a name="2">2. [First-class environments. Discuss.  ;)](http://lambda-the-ultimate.org/node/3861)</a>

<br>

Unix Shells (Bourne Shell Family):

<a name="3">3. [The Bourne Shell](http://partmaps.org/era/unix/shell.html)</a>

<a name="4">4. [Bash](http://www.gnu.org/software/bash/bash.html)</a>

<a name="5">5. [The Korn Shell](http://www.kornshell.com/)</a>

<a name="6">6. [Zsh](http://www.zsh.org/)</a>

<br>

Unix Shells (C Shell Family):

<a name="7">7. [An Introduction to the C shell](http://www.kitebird.com/csh-tcsh-book/csh-intro.pdf)</a>

<a name="8">8. [Tcsh](http://www.tcsh.org/Welcome)</a>

<br>

Unix Shells (Other):

<a name="9">9. [Es: A shell with higher-order functions](http://stuff.mit.edu/afs/sipb/user/yandros/doc/es-usenix-winter93.html)</a>

<a name="10">10. [The Fish Shell](http://fishshell.com/)</a>

<a name="11">11. [Rc - The Plan 9 Shell](http://plan9.bell-labs.com/sys/doc/rc.html)</a>

<br>

Alternative Shells:

<a name="12">12. [A High-Level Programming and Command Language](http://www.researchgate.net/publication/234805805_A_high-level_programming_and_command_language/file/60b7d51645d5d1022a.pdf)</a> 

<p name="13">13. Chris S. McDonald. fsh - A Functional UNIX Command Interpreter. Software - Practice & Experience 17(10): 685-700, 1987</p>

<br>

Embedding the Unix Shell in an Existing Language:

<p name="14">14. L. M. Campbell and M. D. Campbell. An Overview of the Ada Shell. In USENIX Winter: 302-313, 1986</p>

<a name="15">15. [esh, the easy shell](http://web.mit.edu/jhawk/mnt/ss.b/esh-0.5/doc/esh.html)</a>

<a name="16">16. [Hell: A Haskell Shell](https://github.com/chrisdone/hell)</a>

<p name="17">17. J. R. Ellis. A Lisp Shell. SIGPLAN Notices, 15(5):24-34, 1980</p>

<a name="18">18. [Using ML as a Command Language](http://www.hpdc.syr.edu/~chapin/papers/pdf/MLShell.pdf)</a>

<a name="19">19. [Zoidberg - A Modular Perl Shell](https://github.com/jberger/Zoidberg)</a>

<a name="20">20. [The Perl Shell](https://github.com/gnp/psh)</a>

<a name="21">21. [Pysh: A Python Shell](http://pysh.sourceforge.net/)</a>

<a name="22">22. [Rush](https://github.com/adamwiggins/rush)</a>

<a name="23">23. [The Scheme Shell](http://scsh.net/)</a>

<br>

Shell History:

<a name="24">24. [Shell History](http://www.in-ulm.de/~mascheck/bourne/n.u-w.mashey.html)</a>

<a name="25">25. [The Thompson Shell](http://v6shell.org/)</a>

