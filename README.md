VT100 Hax for Retrocomp 2014
============================

[Adam Mayer](https://github.com/phooky) reverse-engineered the VT100 firmware and added some patches for the 2014 Retrocomp.
It's crazy and fun, and details regarding the [ROMs](#rom-overview) and the [VT100-Hax Emulator](#using-the-vt100-hax-emulator)
are below.

ROM Overview
------------

Dumps of the four original 2Kb ROMs containing the firmware for the VT100's 8080 CPU were originally available as early as 2004
at [http://www.dunnington.u-net.com/public/DECROMs/](https://web.archive.org/web/20040609140108/http://www.dunnington.u-net.com/public/DECROMs/).

The VT100 firmware ROMs are:

- [23-061E2.bin](ROMs/23-061E2.bin)
- [23-032E2.bin](ROMs/23-032E2.bin)
- [23-033E2.bin](ROMs/23-033E2.bin)
- [23-034E2.bin](ROMs/23-034E2.bin)

Another important VT100 ROM is the Character Generator ROM, a 2Kb ROM containing 16 bytes of "font" data for each of the 128
characters.  However, this ROM wasn't needed for the challenge, and it isn't used by the VT100-Hax emulator.

- 23-018E2.bin

Unfortunately, the above website is not currently available, but it's still accessiable via the
[Wayback Machine](https://web.archive.org/web/20140723115846/http://www.dunnington.u-net.com/public/DECROMs/).
Information from that website has also been preserved at [pcjs.org](http://www.pcjs.org/devices/roms/dec/)
and in [GitHub](https://github.com/jeffpar/pcjs/tree/master/devices/roms/dec). 

To create a single 8Kb ROM image from the four 2Kb ROMs:

    cd ROMs
    cat 23-061E2.bin 23-032E2.bin 23-033E2.bin 23-034E2.bin > basic.bin

To disassemble the ROM, use `dz80` from [D52](http://www.brouhaha.com/~eric/software/d52/) (here's the
[manual](http://www.bipom.com/documents/dis51/d52manual.html)):

	dz80 -80 basic.bin

This produces `basic.d80`.  To reassemble, use [asm8080](https://github.com/begoon/asm8080).  Before trying to reassemble,
replace references to `X2000` with `2000h`, and pad the image to 8K by replacing the final `nop` with:

	org	1fffh
	nop

Now you're ready to (re)assemble:

	asm8080 -lbasicnew -obasicnew basic.d80

The assembler should produce `basicnew.bin` and `basicnew.lst`, and the new bin file should be identical to `basic.bin`.

Using the VT100-Hax Emulator
----------------------------

You'll probably want to use the [ncurses](https://www.gnu.org/software/ncurses/)-based [emulator](emulator/ncurses-vt100/),
since the [Qt](https://www.qt.io/)-based [emulator](emulator/qt-vt100/) is still a work-in-progress.

The ncurses [Makefile](emulator/ncurses-vt100/Makefile) was slightly modified for use in OS X, because the C/C++ compiler
that Apple now includes doesn't support variable-length array declarations.  The simplest solution is to install a newer
version of GCC using Brew:

    brew install gcc

As of this writing, Brew installs GCC v6.x and creates symbolic links such as `gcc-6`, `g++-6`, etc.

So the [Makefile](emulator/ncurses-vt100/Makefile) invokes `g++-6` instead of `g++` for .cpp files and `gcc-6`
for .c files, since the 8080 emulation C code is referenced by the VT100 simulation C++ code as `extern "C"`.

    cd emulator/ncurses-vt100
    make
    vt100sim --run ../../ROMs/basic.bin

The emulator starts a `bash` process and connects its I/O to the simulated VT100 terminal.  Press F10 to switch between
*TYPING* mode and *CONTROL* mode.  In *CONTROL* mode, you can press:

- ` ` to start/stop
- `b` to set a breakpoint
- `d` to delete a breakpoint
- `m` to snapshot memory
- `n` to single-step
- `q` to quit

In *TYPING* mode, these keys are remapped: 

- `F6` is BREAK
- `F8` is ESC
- `F9` is SET-UP

Note: the [8080 emulation code](emulator/ncurses-vt100/8080) is based on the
[Z80-CPU simulator](http://www.autometer.de/unix4fun/z80pack/) written by Udo Monk.
