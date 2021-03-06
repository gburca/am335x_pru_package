ROOTDIR = ..
TARGET = libprussdrv
CROSS_COMPILE?=arm-arago-linux-gnueabi-
PREFIX?=/usr/local

CC = $(CROSS_COMPILE)gcc
AR = $(CROSS_COMPILE)ar

INCLUDEDIR = ../include
LIBDIR	   = ../lib

C_FLAGS += -I. -Wall -I$(INCLUDEDIR) 

COMPILE.c = $(CC) $(C_FLAGS) $(CPP_FLAGS) -c
AR.c	  = $(AR) rc
LINK.c	  = $(CC) -shared

DBGTARGET = $(LIBDIR)/$(TARGET)d.a
RELTARGET = $(LIBDIR)/$(TARGET).a
SODBGTARGET = $(LIBDIR)/$(TARGET)d.so
SORELTARGET = $(LIBDIR)/$(TARGET).so

DBGCFLAGS = -g -O0 -D__DEBUG
RELCFLAGS = -O3 -mtune=cortex-a8 -march=armv7-a

SOURCES = $(wildcard *.c)
HEADERS = $(wildcard *.h)

TARGETHEADERS = $(addprefix $(INCLUDEDIR)/, $(HEADERS))
INSTALL_HDRS = $(wildcard $(INCLUDEDIR)/*.h)

DBGOBJFILES = $(SOURCES:%.c=debug/%.o) 
RELOBJFILES = $(SOURCES:%.c=release/%.o) 
PIC_DBGOBJFILES = $(SOURCES:%.c=debug/%_PIC.o)
PIC_RELOBJFILES = $(SOURCES:%.c=release/%_PIC.o)

.PHONY: clean debug release sodebug sorelease install

all:	debug release sodebug sorelease

install: release
	install -m 0755 $(PREFIX)/lib
	install -m 0755 $(PREFIX)/include
	install -m 0644 $(LIBDIR)/* $(PREFIX)/lib
	install -m 0644 $(INSTALL_HDRS) $(PREFIX)/include

release:	$(RELTARGET) 
#release:	$(RELTARGET) $(TARGETHEADERS)

sorelease:	$(SORELTARGET)
#sorelease:	$(SORELTARGET) $(TARGETHEADERS)

sodebug:	$(SODBGTARGET)
#sodebug:	$(SODBGTARGET) $(TARGETHEADERS)

debug:		$(DBGTARGET)
#debug:		$(DBGTARGET) $(TARGETHEADERS)


$(RELTARGET):	$(RELOBJFILES)
	@mkdir -p $(ROOTDIR)/lib
	$(AR.c) $@ $(RELOBJFILES)

$(SORELTARGET):	$(PIC_RELOBJFILES)
	@mkdir -p $(ROOTDIR)/lib
	$(LINK.c) -o $@ $(PIC_RELOBJFILES)

$(SODBGTARGET):	$(PIC_DBGOBJFILES)
	@mkdir -p $(ROOTDIR)/lib
	$(LINK.c) -o $@ $(PIC_DBGOBJFILES)

$(DBGTARGET):	$(DBGOBJFILES) 
	@mkdir -p $(ROOTDIR)/lib
	$(AR.c) $@ $(DBGOBJFILES)

$(RELOBJFILES):	release/%.o: %.c $(HEADERS)
	@mkdir -p release
	$(COMPILE.c) $(RELCFLAGS) -o $@ $<

$(PIC_RELOBJFILES):	release/%_PIC.o: %.c $(HEADERS)
	@mkdir -p release
	$(COMPILE.c) -fPIC $(RELCFLAGS) -o $@ $<

$(DBGOBJFILES):	debug/%.o: %.c $(HEADERS)
	@mkdir -p debug
	$(COMPILE.c) $(DBGCFLAGS) -o $@ $<

$(PIC_DBGOBJFILES):	debug/%_PIC.o: %.c $(HEADERS)
	@mkdir -p debug
	$(COMPILE.c) -fPIC $(DBGCFLAGS) -o $@ $<

$(TARGETHEADERS): $(HEADERS)
	@echo Installing headers...
	@install -d $(INCLUDEDIR)
	@install -c $< $@

clean:
	-rm -rf release debug *~ ../lib/*

