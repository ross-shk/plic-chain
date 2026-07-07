plicc
=====

PL/I Compile & Link Toolchain

Compile and link Iron Spring PL/I programs with a single command.
Each `.pli` or `.pl1` source is compiled to an object file with
`plic`, then linked into a Linux executable with `gcc`.

Usage
-----
    plicc [options] <source.pli|.pl1>... [object.o|archive.a]...

### Example
-------
Create a sample program:

    echo ' main: proc options(main); display("Hello, world!"); end;' > hello.pli

then run:

    plicc hello.pli 
    ./hello

Options
-------

`--compile`
:   Compile only; do not link.

`--cinterop`
:   Link with the C interop modules (`fhs.o`, `ghs.o`).  Required when
    mixing PL/I with C code (replaces PL/I's internal heap with
    `malloc`/`free`).

`--out <name>`
:   Name the output executable.  Default: basename of the first `.pli`
    source.

`--verbose`
:   Show progress messages during compilation and linking.

`--help`
:   Display usage and exit.

Arguments
---------

`source.pli` / `source.pl1`
:   PL/I source files to compile.

`object.o` / `object.obj`
:   Pre-compiled object files to include in the link step.

`archive.a`
:   Static libraries to include in the link step.

`-l<name>`
:   Linker library flag (e.g. `-lnet`).  Passed directly to `gcc`.
    Not to be confused with `plic` listing flags (`-lsx`, `-lg`, etc.),
    which are passed to the compiler.

All other flags are forwarded to the `plic` compiler (for example
`-i<dir>` for include paths, `-m(2,72)` for source margins, or listing
options like `-lsiaxgmo`).

Additional Examples
--------

Compile with a custom output name:

    plicc hello.pli --out greeter
    ./greeter

Compile multiple sources and link together:

    plicc main.pli aux.pli
    ./main

Compile and link with a pre-built object file, likely needs `--cinterop` if there are any non-PL/I object files:

    plicc program.pli helper.o
    ./program

Compile and link with a static library, likely needs `--cinterop`:

    plicc app.pli libhelper.a
    ./app

Compile and link with a linker library flag - `libnet` uses C bindings, so `--cinterop` is used:

    plicc client.pli -lnet --cinterop 
    ./client

Compile only, inspect the object, then link manually:

    plicc --compile module.pli
    nm module.o
    plicc module.o --out prog

Compile with include paths and listing output:

    plicc -i../include -lsiaxgmo payroll.pli

Link existing objects only (skip compilation):

    plicc main.o helper.o libstuff.a
    ./a.out

Override the PL/I compiler and linker flags:

    PLIC=~/pli/dev/comp/plic LDFLAGS="-no-pie" plicc bigapp.pli

Environment
-----------

All tool and flag variables can be overridden through the environment:

| Variable         | Default                                    |
|------------------|--------------------------------------------|
| `PLIC`           | `plic`                                     |
| `CCLD`           | `gcc`                                      |
| `LDFLAGS`        | `-m32 -no-pie -z muldefs`                  |
| `LIBS`           | `-lprf`                                    |
| `CINTEROP_LIBS`  | `/usr/lib/pli/alt/fhs.o /usr/lib/pli/alt/ghs.o` |
| `PLIINC`         | `-i/usr/lib/pli/include -i/usr/local/include`    |

