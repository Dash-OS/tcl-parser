# tcl-parser

> An extension for Tcl, written in C, that lets Tcl scripts access Tcl's own parser via the parse command.

[http://wiki.tcl.tk/1660](http://wiki.tcl.tk/1660)

### Overview

This command parses a Tcl script into "commands, words" and tokens.

Each of the commands below takes a script to parse and a range
into the script.  The command parses the script from the first index up to and including the last index.  

The return of each command is a list of tuples indicating the ranges of each sub-element.  Use the returned indices as arguments to `[parse getString]` to extract the parsed string from the script.

### Example

```tcl
load {} parser

proc stringRange {string start step} {
  tailcall string range $string $start [expr {$start+$step-1}]
}

proc ss {script} {
  set restRange {0 end}
  while {1} {
    lassign [parse command $script $restRange] commentRange commandRange restRange tree
    set comment [stringRange $script {*}$commentRange]
    set command [stringRange $script {*}$commandRange]
    if {$command eq ""} { break }
    lappend result [list $comment $command]
  }
  return $result
}

set parsed [ss {
  # set foo first
  set foo [list \
    one \
    two \
    three
  ]
  # set bar and puts the result
  set bar $foo;puts $bar
  if {$foo eq $bar} {
    puts "$foo is $bar"
    ::http::geturl http://www.google.com \
      -command [list myProc]
  }
}]
```

### Useful Links & References

 - [wiki](http://wiki.tcl.tk/1660) Tcl Wiki article with examples and discussion.
 - [Tcl_ParseCommand](http://www.tcl.tk/man/tcl/TclLib/ParseCmd.htm) Tcl C Library API
 - [parser SourceForge](http://tclpro.cvs.sourceforge.net/tclpro/tclparser/) Original source repo
 - [aspect fossil](http://chiselapp.com/user/aspect/repository/tclparser/index) A mirror of the library on fossil.


###### Copyright (c) 1999-2000 Ajuba Solutions

This is the Tcl parser component used by the checker to
parse a Tcl script into commands, words and tokens.
