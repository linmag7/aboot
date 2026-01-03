# tools/Makefile – target-by-default, objstrip as host tool

# Programs in this directory
PGMS        := e2writeboot isomarkboot abootconf elfencap objstrip

# ---- Toolchains ---------------------------------------------------------
ifeq ($(ARCH),alpha)
CROSS_COMPILE  ?= alpha-unknown-linux-gnu-
endif

# Target (Alpha) toolchain – respects ARCH/CROSS_COMPILE
CC           := $(CROSS_COMPILE)gcc
CFLAGS       := -g -O2 -Wall -I. -I../include
LDFLAGS      ?=

# Host (build machine) toolchain – used for utilities that must run on host
HOSTCC       ?= gcc
HOSTCFLAGS   ?= -g -O2 -Wall  -I. -I../include
HOSTLDFLAGS  ?=

# ---- Host vs target tools ----------------------------------------------

# Anything in HOSTTOOLS is built with HOSTCC instead of CC.
HOSTTOOLS    := objstrip
TARGETTOOLS  := $(filter-out $(HOSTTOOLS),$(PGMS))

EXEC_PREFIX  ?= /usr

# ---- Default targets ----------------------------------------------------

all: $(PGMS)

install: $(PGMS)
	install -c -o root -g root -m 755 $(PGMS) $(EXEC_PREFIX)/bin

clean:
	rm -f *~ *.o *.a core $(PGMS)

# ---- Generic *target* build rules --------------------------------------
# By default, .c → .o goes through the TARGET compiler.

%.o: %.c
	$(CC) $(HOSTCFLAGS) -c -o $@ $<

# ---- Target-linked tools -----------------------------------------------

# These will run on Alpha (or whatever CROSS_COMPILE points at).

e2writeboot: e2writeboot.o e2lib.o bio.o
	$(CC) $(LDFLAGS) -o $@ $^

isomarkboot: isomarkboot.o ../lib/isolib.o
	$(CC) $(LDFLAGS) -o $@ $^

abootconf: abootconf.o
	$(CC) $(LDFLAGS) -o $@ $^

elfencap: elfencap.o
	$(CC) $(LDFLAGS) -o $@ $^

# Header deps
e2writeboot.o: e2lib.h
e2lib.o:      e2lib.h

# ---- Host-built tools ---------------------------------------------------
# objstrip must run on the build host, so we force HOSTCC here.
# These explicit rules override the generic %.o / link rules above.

objstrip.o: objstrip.c
	$(HOSTCC) $(HOSTCFLAGS) -c -o $@ $<

objstrip: objstrip.o
	$(HOSTCC) $(HOSTLDFLAGS) -o $@ $^

