# NAME

mathgrep - grep through a LaTeX document looking only in math sections

# SYNOPSIS

    mathgrep [OPTION]... CODE [FILE]...
    Apply CODE to mathematics in each FILE or standard input.
    Example: mathgrep '/\\hbar/' quantum.tex classical.tex

    Options:
      -e, --code CODE  Use CODE
      -i, --[no]inline  Apply CODE to inline math
      -d, --[no]display  Apply CODE to display math
      -E, --environments ENVS  Apply CODE to ENVS
      -c, --count  Only print a count of matching lines per FILE
      -l, --files-with-matches  Only print FILE names containing matches
      -L, --files-without-matches  Only print FILE names containing no match
      -n, --line-number  Print line number with output lines
      -R, --preamble  Skip preamble (before \begin{document})
      -O, --postamble  Skip postamble (after \end{document})
      -r, --replace [EXT] Print all lines; if EXT is specified then backup
                         FILE to FILEEXT and redirect output to FILE
      -S, --start NUM  Start applying CODE at line NUM
      -F, --finish NUM  Stop applying CODE at line NUM
      -U, --colo[u]r [COLOUR]  Highlight matches in COLOUR (default: red)
      -P, --[no]separate  Print a separator between matches
      -C, --context NUM  Print NUM lines of output context
      -B, --before-context NUM  Print NUM lines of leading context
      -A, --after-context NUM  Print NUM lines of trailing context
      -h, -?, --help  Print help message
      --man  Print man page
      --gpl  Print GPL
      -v, --verbose  Increase verbosity
      -s, --quiet  Suppress verbosity
      -V, --version  Print version and exit

# DESCRIPTION

**mathgrep** goes through the specified input FILEs (or standard input
if no FILEs are given) applying CODE to any mathematics it
finds.  By default, **mathgrep** prints any lines containing
mathematics on which CODE evaluated successfully.

The CODE is a bit of perl code which does something to `$_`.
For grep-like performace try `m/regexp/`.  **mathgrep** can also do
search-and-replace.

The CODE is evaluated on the whole of a section of mathematics in one
go, even if the mathematics splits across several lines.  All the
lines containing the section of mathematics are printed if CODE
evaluated successfully.

The options and their behaviour are as like to those of grep as I could
make them.

# OPTIONS

- **-A NUM, --after-context NUM**

    Print NUM lines of trailing context after a line on which CODE
    executes successfully.

- **-B, --before-context NUM**

    Print NUM lines of leading context after a line on which CODE executes
    successfully.

- **-C, --context NUM**

    Print NUM lines of context either side of a line on which CODE
    executes successfully.

- **-c, --count**

    Suppress normal output, instead print a count of the number of
    sections of mathematics on which CODE evaluated successfully.  Note:
    this may not be the exact number of matches as CODE is applied once to
    each section of mathematics.

- **-d, --\[no\]display**

    Toggles whether to look in display math environments.

- **-E, --environments ENV\[,ENV\]...**

    Allows the user to specify a list of extra environments to look in.
    These are always checked.  Multiple instances of this option are
    allowed, as are comma-separated lists.

- **-e, --code CODE**

    Specifies the CODE to apply.  This gives a way to ensure that the code
    is not swallowed up in another option.  Another way is to finish the
    list of options with **--** before specifying the CODE.

- **-F, --finish NUM**

    Finish checking the input after NUM lines.  Subsequent lines may still
    be printed depending on the status of the **--after-context**,
    **--context**, or **--replace** options.

- **-h, -?, --help**

    Print a summary of all the options and exit.

- **-i, --\[no\]inline**

    Toggles whether to look in inline math environments.

- **-l, --files-with-matches**

    Suppress normal output, instead just print the FILE name of a FILE on
    which CODE evaluated successfully at least once.  Can be meaningfully
    combined with **--count**.

- **-L, --files-without-matches**

    Suppress normal output, instead just print the FILE name of a FILE on
    which CODE did not evaluate successfully at all.  Cannot be
    meaningfully combined with **--count**.

- **--man**

    Print this manpage and exit.

- **--gpl**

    Display the GNU Public License and exit (note: it actually displays the GPL
    that comes with Perl so if your installation of Perl is not set up correctly
    then this might not work).

- **-n, --line-number**

    Prefix each line of output with its line number within its input file.
    Note: if CODE has mucked about with the number of lines in a section
    of mathematics then the line count may go a little awry within that
    section but it will correct itself again for the next section.

- **-O, --postamble**

    Stop applying CODE to maths when `\end{document}` has been reached.

- **-P, --\[no\]separate**

    Toggle whether to print a separator between matches.  Default is on,
    unless **--replace** has been specified.  The separator is `%---%` so
    won't mess up running LaTeX on the output.

- **-R, --preamble**

    Don't start applying CODE to maths until `\begin{document}` has been
    reached.

- **-r, --replace \[EXT\]**

    Prints all lines whether CODE successfully ran or not.  If EXT is
    specified then it backups up FILE to FILEEXT and redirects the output
    to FILE.  Similar to the **-i** option of perl.

- **-s, --quiet**

    Suppress verbosity.

- **-S, --start NUM**

    Only apply CODE to maths found on or after line NUM.  Note: depending
    on **--before-context**, **--context**, or **--replace** then lines
    before NUM may still be printed.

- **-U, --colo\[u\]r \[COLOUR\]**

    If CODE is of the form `m/PATTERN/[cgimosx]` or one of its simple
    variants then this option colours the matching text; red if COLOUR is
    not specified.

- **-v, --verbose**

    Increase the verbosity level.

- **-V, --version**

    Print version number and exit.

# EXAMPLES

Suppose that `quantum.tex` is

    \documentclass{article}
    
    \newcommand{\texthbar}{\(\hbar\)}
    
    \begin{document}
    
    The number \(\hbar\) is very small.  The product, \(\hbar\hbar\) is
    miniscule.   On the other hand, \(c\), the speed of light is very big.
    \[
    \hbar = \frac{h}{2\pi}
    \]
    \begin{myenv}
    Think of hbar as your friend.
    \end{myenv}
    
    \end{document}
    
    Some junk involving \(\hbar\).

Then the following examples produce the specified output

`mathgrep '/hbar/' quantum.tex`

    %---%
    \newcommand{\texthbar}{\(\hbar\)}
    %---%
    The number \(\hbar\) is very small.  The product, \(\hbar\hbar\) is
    %---%
    \[
    \hbar = \frac{h}{2\pi}
    \]
    %---%
    Some junk involving \(\hbar\).

`mathgrep --replace -- 's/hbar/hfoo/g' quantum.tex`

    \documentclass{article}

    \newcommand{\texthbar}{\(\hfoo\)}

    \begin{document}

    The number \(\hfoo\) is very small.  The product, \(\hfoo\hfoo\) is
    miniscule.  On the other hand, \(c\), the speed of light is very big.
    \[
    \hfoo = \frac{h}{2\pi}
    \]
    \begin{myenv}
    Think of hbar as your friend.
    \end{myenv}

    \end{document}

    Some junk involving \(\hfoo\).

Note the `g` option on the `s///`, otherwise the line "The
number..." would have been:

    The number \(\hfoo\) is very small.  The product, \(\hfoo\hbar\) is

`mathgrep --replace .orig 's/hbar/hfoo/g' quantum.tex`

Produces no output, but `quantum.tex` is now the altered text above
and `quantum.tex.orig` is the original.

`mathgrep --preamble '/hbar/' quantum.tex`

    %---%
    The number \(\hbar\) is very small.  The product, \(\hbar\hbar\) is
    %---%
    \[
    \hbar = \frac{h}{2\pi}
    \]
    %---%
    Some junk involving \(\hbar\).

`mathgrep --postamble '/hbar/' quantum.tex`

    %---%
    \newcommand{\texthbar}{\(\hbar\)}
    %---%
    The number \(\hbar\) is very small.  The product, \(\hbar\hbar\) is
    %---%
    \[
    \hbar = \frac{h}{2\pi}
    \]

`mathgrep --noinline --nodisplay --environments myenv '/hbar/' quantum.tex`

    %---%
    \begin{myenv}
    Think of hbar as your friend.
    \end{myenv}

# NOTES

This program determines what is mathematics by looking for a matched
pair; for example, `\(` and `\)`.  It does not check that the end of
the pair is the correct one, just that it is the next one.  Thus on
encountering the text:

    \(x = y \text{ if \(a = b\), otherwise } x = z\)

the program thinks that `x = y \text{ if \(a = b` is the next bit of
mathematics.  Consequently, it won't see the `x = z` part at all.
This sort of thing only happens when the **same** type of mathematical
environment is used inside and outside the `\text` command.  On
encountering the text:

    \[x = y \text{ if \(a = b\), otherwise } x = z\]

then the whole of the inner part is considered as mathematics.  Note
that a byproduct of this is that the arguments of `\text` and
`\intertext` are viewed as being in mathematics.

Another place where this might cause a problem is if you define an
alias for a standard math environment:

    \let\myendalign\endalign
    \begin{align}
      x = y
    \end{myalign}

then this program will continue slurping in input until it finds the
next `\end{align}`.

Currently this program does not consider $ and $$ to have anything to
do with mathematics.  This is partly because the coding is a bit
tricky (consider the text `$x=y$$a=b$`) but also because $ and $$ are
not actually LaTeX commands but are inherited from TeX.  Whilst $ is
synonymous with \\(, $$ and \\\[ are actually slightly different (take a
look at `latex.ltx` if you don't believe me).  Maybe if I get bored
one day I'll take a look at implementing it.

# BUGS

Whad'dya mean, "I've found a bug."?  Impossible.  There are no bugs,
only features.  To tell me about features, send an email to 

loopspace@mathforge.org

With the following in the subject line:

Howay man, av fund ah borg een maathgrip.

Or open an issue on github at: https://github.com/loopspace/mathgrep

# AUTHOR

Andrew Stacey

# LICENSE

Copyright (C) 2007   Andrew Stacey

This program is free software; you can redistribute it and/or mod ify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or any
later version.

This program is distributed in the hope that it will be useful, but
WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 59 Temple Place - Suite 330, Boston, MA 02111-1307,
USA.
