Installing C compilers
======================

Finally you're probably wondering how to get the C compilers installed for
your computer. The answer here is that it depends what operating system you're
using.

Linux
-----

Instruct your package manager to install the compilers e.g. on Ubuntu the
following command will install all of the compilers and some other stuff you
might want:

~~~~
$ sudo apt-get install build-essential
~~~~

Now you can run `gcc` etc in the terminal.

OSX
---

Install [Xcode](https://developer.apple.com/xcode/).

That will give you an editor, compilers, and a load of other stuff. You should
then be able to run `gcc` from the terminal.

Windows
-------

Install [notepad++](https://notepad-plus-plus.org/) to get an editor.

Windows is the trickiest platform to install compilers. To install MinGW (like
on the lab machines) there are instructions
[here](http://www.mingw.org/wiki/getting_started). At the time of writing (Jan
2016) I have seen that the following instructions should work:

First MinGW is not just a compiler. The MinGW project consists of several
things

- An environment for using MinGW software packages.
- A collection of software packages (some of which we want e.g. `gcc` etc.).
- A package manager program that can download and install the packages you
  want.

To install the compilers with MinGW we need to first install the "MinGW
installation Manager".

- Download
  [mingw-get-setup.exe](https://sourceforge.net/projects/mingw/files/latest/download)
- Run the downloaded `mingw-get-setup.exe` to install the MinGW Installation
  Manager.

Now that you have installed the Installation Manager you need to run it to
install the packages we want.

- Launch MinGW Installation Manager
- On the left tab click "basic setup"
- Then on the right tab select the following packages (by clicking the tickbox
  next to each package):
    - mingw32-base
    - mingw32-gcc-c++
    - mingw32-gcc-objc
    - msys-base
- Go to the program menu (at the top) and choose "Intallation" and then "Apply
  changes".
- Click "Apply"

By default MinGW will install all of its files in the folder `C:\MinGW`. We
have now installed the shell (`sh.exe`) and the compilers (`gcc.exe`) and some
other bits that we need. The only thing left is to create a shortcut that will
open a console and run the shell. Use Notepad++ to create a `.bat` file.

Open Notepad++, create a new file and save it on the desktop as `mingw.bat`.
The contents of the file should be

~~~
set HOME=%HOMEPATH%
C:\MinGW\msys\1.0\bin\sh.exe --login
~~~

You should now be able to double-click that file to open the MinGW shell (it
should look like it does on the lab computers). From the shell test that the
command `gcc` works e.g.:

~~~
$ gcc
gcc: fatal error: no input files
compilation terminated.
~~~

That error message is fine. It just means that you need to tell `gcc` which
files to compile. If instead you see

~~~
$ gcc
gcc: command not found
~~~

then it means that `gcc` is not installed or is not on your `PATH` environment
variable. This could be because you didn't install `gcc` but actually a common
problem that I've seen is that the `/etc/fstab` file is missing. If that
happens then try:

~~~~
$ ls /etc
~~~~

If you can see a file called `fstab.sample` but not a file called `fstab` then
your `fstab` is missing. The file `fstab.sample` is an example of what this
file should look like. You can fix the problem by typing

~~~~
$ cp /etc/fstab.sample /etc/fstab
~~~~

This will copy (`cp`) the `fstab.sample` file to `/etc/fstab`. Having done
that close the shell and reopen it. You should then find that `gcc` is working.

If it still doesn't work or if you get it to work some other way then please
let me know because I want these instructions to be as good as possible.

----------

[Back to index](index.html)
