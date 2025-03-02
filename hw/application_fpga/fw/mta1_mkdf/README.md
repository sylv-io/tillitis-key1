# Tillitis Key firmware

## Build the firmware

You need Clang with 32 bit RISC-V support. You can check this with:

```
  $ llc --version|grep riscv32
      riscv32    - 32-bit RISC-V
```

or just try building.

Build the FPGA bitstream with the firmware using `make` in the
`hw/application_fpga` directory.

If your available `objcopy` and `size` commands is anything other than
the default `llvm-objcopy-14` and `llvm-size-14` define `OBJCOPY` and
`SIZE` to whatever they're called on your system.

## Using QEMU

Checkout the `mta1` branch of [our version of the
qemu](https://github.com/tillitis/qemu) and build:

```
  $ git clone -b mta1 https://github.com/tillitis/qemu
  $ mkdir qemu/build
  $ cd qemu/build
  $ ../configure --target-list=riscv32-softmmu
  $ make -j $(nproc)
```

Run it like this:

```
  $ /path/to/qemu/build/qemu-system-riscv32 -nographic -M mta1_mkdf,fifo=chrid -bios firmware \
-chardev pty,id=chrid
```

This attaches the FIFO to a tty, something like `/dev/pts/16` which
you can use with host software to talk to the firmware.

To quit QEMU you can use: `Ctrl-a x` (see `Ctrl-a ?` for other commands).

Debugging? Use the HTIF console by removing `-DNOCONSOLE` from the
`CFLAGS` and using the helper functions in `lib.c` for printf-like
debugging.

You can also use the qemu monitor for debugging, e.g. `info
registers`, or run qemu with `-d in_asm` or `-d trace:riscv_trap`.

Happy hacking!
