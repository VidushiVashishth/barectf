![Icon made by Freepik from www.flaticon.com](https://lttng.org/blog/images/barectf.png)

[![](https://img.shields.io/pypi/v/barectf.svg)](https://pypi.python.org/pypi/barectf)
[![Jenkins](https://img.shields.io/jenkins/s/https/ci.lttng.org/barectf.svg)](https://ci.lttng.org/job/barectf)

**barectf** is a command-line utility which generates C99
code that is able to write native [Common Trace Format](http://diamon.org/ctf)
(CTF) binary streams.

You will find barectf interesting if:

  1. You need to trace an application.
  2. You need tracing to be efficient, yet flexible:
     record integers of custom sizes, custom floating point numbers,
     enumerations supported by a specific integer type, and
     null-terminated UTF-8/ASCII strings (C strings).
  3. You need to be able to convert the recorded binary events to
     human-readable text, as well as analyze them with Python scripts
     ([Babeltrace](http://diamon.org/babeltrace/) does all that,
     given a CTF input).
  4. You _cannot_ use [LTTng](http://lttng.org/), an efficient tracing
     framework for the Linux kernel and Linux/BSD user applications, which
     also outputs CTF.

The target audience of barectf is developers who need to trace bare metal
systems (without an operating system). The code produced by barectf
is pure C99 and can be lightweight enough to fit on a tiny microcontroller.

**Key features**:

  * Single input: easy-to-write [YAML configuration
    file](https://github.com/efficios/barectf/wiki/Writing-the-YAML-configuration-file)
  * 1-to-1 mapping from tracing function parameters to event fields
  * Custom and bundled
    [_platforms_](https://github.com/efficios/barectf/wiki/barectf-platform)
    hiding the details of opening/closing packets and writing them to a
    back-end (continuous tracing), getting the clock values, etc.:
    * _linux-fs_: basic Linux application tracing writing stream files to
      the file system for demonstration purposes
    * _parallella_: Adapteva Epiphany/[Parallella](http://parallella.org/)
      with host-side consumer
  * CTF metadata generated by the command-line tool (automatic trace UUID,
    stream IDs, and event IDs)
  * All basic CTF types are supported: integers, floating point numbers,
    enumerations, and null-terminated strings (C strings)
  * Binary streams produced by the generated C code and metadata file
    produced by barectf are CTF 1.8-compliant
  * Human-readable error reporting

**Current limitations**:

As of this version:

  * All the generated tracing C functions, for a given barectf
    stream-specific context, need to be called from the same thread, and cannot
    be called from an interrupt handler, unless a user-provided
    synchronization mechanism is used.
  * CTF compound types (array, sequence, structure, variant) are not supported
    yet, except at some very specific locations in the metadata.
  * The current generated C code is not strictly C99 compliant:
    [statement expressions](https://gcc.gnu.org/onlinedocs/gcc/Statement-Exprs.html)
    and the
    [`typeof` keyword](https://gcc.gnu.org/onlinedocs/gcc/Typeof.html)
    GCC extensions are used in the generated bitfield macros. The
    generated C code is known to be compiled with no warnings using
    both GCC and Clang.

barectf is written in Python 3.


## Installing

Make sure you have Python 3 and `pip` for Python 3 installed, then
install barectf.

Note that you may pass the `--user` argument to
`pip install` to install the tool in your home directory (instead of
installing globally).

**Latest Ubuntu**:

    sudo apt-get install python3-pip
    sudo pip3 install barectf

**Ubuntu 12.04 and lower**:

    sudo apt-get install python3-setuptools
    sudo easy_install3 pip
    sudo pip3 install barectf

**Debian**:

    sudo apt-get install python3-pip
    sudo pip3 install barectf

**Fedora 20 and up**:

    sudo yum install python3-pip
    sudo pip3 install barectf

**Arch Linux**:

    sudo pacman -S python-pip
    sudo pip install barectf

**OS X**

With [Homebrew](http://brew.sh/):

    brew install python3
    pip3 install barectf


## What is CTF?

See the [CTF in a nutshell](http://diamon.org/ctf/#ctf-in-a-nutshell)
section of CTF's website to understand the basics of this
trace format.

The most important thing to understand about CTF, for barectf use
cases, is the layout of a binary stream packet:

  * Packet header (defined at the trace level)
  * Packet context (defined at the stream level)
  * Sequence of events (defined at the stream level):
    * Event header (defined at the stream level)
    * Stream event context (defined at the stream level)
    * Event context (defined at the event level)
    * Event payload (defined at the event level)

The following diagram, stolen without remorse from CTF's website, shows
said packet layout:

![](http://diamon.org/ctf/img/ctf-stream-packet.png)

Any of those six dynamic scopes, if defined at all, has an associated
CTF type. barectf requires them to be structure types.


## Using

See the [project's wiki](https://github.com/efficios/barectf/wiki) which
contains all the information needed to use barectf.


## Testing

Bash is required for testing barectf.

The barectf tests execute the `barectf` command available in your
`$PATH`. The best way to test a specific version of barectf is to create
a Python 3 [virtual environment](https://virtualenv.pypa.io/en/latest/),
install the appropriate version, and then run the tests.

In the barectf source tree root, do:

    virtualenv --python=python3 virt
    . ./virt/bin/activate
    rehash # if using zsh
    ./setup.py install
    (cd tests && ./test.bash)

You can specify [Bats](https://github.com/sstephenson/bats) options to
`./test.bash`, like `--tap` to get a [TAP](https://testanything.org/)
output.

You can exit the virtual environment by running `deactivate`.


## Community

Since the barectf community is small, it's sharing the communication
channels of the [LTTng](http://lttng.org/) project,
as [EfficiOS](http://www.efficios.com/) is the main sponsor of both
projects. It goes like this:

| Item | Location | Notes |
| --- | --- | --- |
| Mailing list | [lttng-dev](https://lists.lttng.org/cgi-bin/mailman/listinfo/lttng-dev) (`lttng-dev@lists.lttng.org`) | Preferably, use the `[barectf]` subject prefix |
| IRC | [`#lttng`](irc://irc.oftc.net/lttng) on the OFTC network | More specifically, query `eepp` (barectf's maintainer) on this network or on freenode |
| Code contribution | Create a new GitHub [pull request](https://github.com/efficios/barectf/pulls) | |
| Bug reporting | Create a new GitHub [issue](https://github.com/efficios/barectf/issues/new) | |
| Continuous integration | [barectf item on LTTng's CI](https://ci.lttng.org/job/barectf/) | |
| Blog | The [LTTng blog](http://lttng.org/blog/) contains many posts about barectf | |
