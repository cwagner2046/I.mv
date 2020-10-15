1. INTRODUCTION

    NOTE: This script is provided as-is. No maintenance will be carried out on
    it. If you surprisingly want to build on this code, fork it.

This utility for Linux and Unix extends the possibilities of the "mv" and "cp"
commands by allowing to use (standard and extended) regular expressions to
specify source file and destination files. The options belonging to "mv" and
"cp" are passed through.

When using the regular expressions, it is recommended to also use the --prompt
option. The tool then displays the source file name(s) and the computed
destination file name(s) and request confirmation of the action to be taken
before carrying it out. See the manual below.

This script is written in Bash. It was tested using Bash 3.2.57(1) running on
Mac OSX (10.10) Yosemite. I have therefore no direct evidence that this
utility works fine on Linux or Unixes other than Mac OSX.

This utility relies on a version of 'sed' accepting the '-E' option to
specify POSIX extended regular expressions.


1.2 Installation

To install the script, just copy it in a directory listed in your PATH
variable.


2. MANUAL

Here below is the manual, the output of the command "i.mv -h":

Syntax: 
  i.mv -h
  i.mv [mopts] src1 [src2 ...] dest
  i.mv -x [mopts | [-c [copts]]] src dest
  i.mv -x [--prompt] [mopts | [-c [copts]]] [-s srcdir] [-d destdir]
              [-g] {-r | -E} +'RE1' +'RE2'

where
  -h displays this information;
  -x calls for an execution of 'i.mv' in extended mode;
  --prompt causes 'i.mv' to ask confirmation of the requested action;
  -c calls for a copy of the source file, 'i.mv' running in extended mode;
  -g calls for RE2 to be globally substituted for RE1 in the file name(s);
     Default: only the 1st occurence of RE1 is replaced.
  -s specifies the source directory when using REs and the source file(s) are
     not to be found in the current directory;
  -d specifies the destination directory when using REs
  -r and -E call for using regular expressions (RE) instead of filenames;
     NOTE: the program then looks for matching files in the *current* directory
     or in the directory specified by the '-s' option;
  mopts are any legal 'mv' options (see the man pages);
  copts are any legal 'cp' options (see the man pages);
  RE1 is the regular expression specifiying the source file name(s);
  RE2 is the regular expression specifiying the destination file name(s).

Renames, or copies, 'src' to 'dest'; or moves the source files 'src1', 'src2',
etc, into the 'dest' directory.

When moving several sources ('src1', 'src2', etc), the extended mode is NOT
used, that is, 'i.mv' behaves as 'mv' would behave.

When using the -r or the -E flags, the file name specifications are sed
regular expressions (-r flag) or POSIX extended regular expressions (-E flag).
If the RE needs to include spaces, you should use extended RE and
the '[:space:]' character class. For example:

  $ ls
  a b.txt       c.pdf
  $ i.mv -xv -E '[[:space:]]' '_'
  i.mv: moving 'a b.txt' to 'a_b.txt'
  $ ls
  a_b.txt       c.pdf

One purpose of the extended mode is to avoid you having to provide the
file name extension of the destination. So rather than having to type in:

  mv a.pdf b.pdf

you can use this command:

  i.mv -x a.pdf b

which leads to the file 'a.pdf' renamed to 'b.pdf'.

A filename extension is understood to be the last sequence of characters after
the rightmost dot.

Another purpose of the extended mode is to allow renaming (or copying under
new names) a series of files identified by RE1. In this case, the resulting file
names are constructed by substituting RE2 into the original file names. In order
for this to be possible, RE2 has to refer to patterns identified in RE1. For
example:

  i.mv -r '\(a\)\(b\).\(\.pdf\)' '\2\1\3'

results in the file 'abc.pdf', in the current directory, renamed to 'ba.pdf'.
WARNING: This also means that, were the current directory to contain the files
'abc.pdf' and 'abD.pdf', only _one_ file would be renamed 'ba.pdf', the other
one would be lost, overwritten; hence the usefulness of the '--prompt' option.

