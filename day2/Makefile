SHELL = /bin/sh
HOSTNAME = $(shell hostname | sed 's/[0-9]*//g' | sed 's/\..*//g')

VERSION ?= orig

TARGET = stencil2d-$(VERSION).x

SRCS = m_utils.F90 \
       stencil2d-$(VERSION).F90

# create object file list
OBJS := $(SRCS:.F90=.o)

ifeq "$(HOSTNAME)" "daint"
  F90 = ftn
  FFLAGS = -O3 -hfp3 -eZ -ffree -N255 -ec -eC -eI -eF -rm -h omp
endif
ifeq "$(HOSTNAME)" "nid"
  F90 = ftn
  FFLAGS = -O3 -hfp3 -eZ -ffree -N255 -ec -eC -eI -eF -rm -h omp
endif
ifeq "$(HOSTNAME)" "papaya"
  F90 = mpif90 -cpp 
  FFLAGS = -O0 -g -fbacktrace -fno-fast-math -ffree-line-length-none -fno-backslash \
    -pedantic -Waliasing -Wampersand -Wline-truncation -Wsurprising -Wtabs -Wunderflow \
    -fdump-core -ffpe-trap=invalid,zero,overflow -fbounds-check -finit-real=nan \
    -finit-integer=9999999 -finit-logical=true -finit-character=35
endif
ifndef F90
  $(error F90 is not set for $(HOSTNAME))
endif

# definition of phony targets

.PHONY : clean

$(TARGET): $(OBJS)
	$(F90) $(FFLAGS) $(OBJS) -o $(TARGET)
	cp $(TARGET) stencil2d.x

clean: clean_examples
	-$(RM) -rf *~ *.o *.mod *.MOD *.i core.* *.out *.lst *.x *.x+orig *.x+[0-9]* *.dat *.report report_*.txt out.txt

clean_examples:
	rm -f parallel private reduction regions schedule scoping shared threadnum timing weak weak.txt barrier barrier2 caching.txt caching2 caching2.txt infos loops nowait out.txt caching 

distclean: clean
	-$(RM) -rf *.png *.dat *.npy *.x *.x+orig* *.x+[0-9]*

%.o: %.F90
	$(F90) $(FFLAGS) -c $*.F90

