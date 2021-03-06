PROG = unit_test
CFLAGS = -W -Wall -Werror -I.. -DNS_ENABLE_SSL -DNS_ENABLE_IPV6 -pthread -g -O0 $(CFLAGS_EXTRA)
PEDANTIC=$(shell gcc --version 2>/dev/null | grep -q clang && echo -pedantic)
AMALGAMATED_SOURCES = $(PROG).c ../net_skeleton.c

include ../modules/modules.mk

# http://crossgcc.rts-software.org/doku.php?id=compiling_for_win32
MINGW_GCC=/usr/local/gcc-4.8.0-qt-4.8.4-for-mingw32/win32-gcc/bin/i586-mingw32-gcc

all: clean ismerged $(PROG)_ansi $(PROG)_c99 $(PROG) $(PROG)_coverage

.PHONY: clean clean_coverage $(PROG) $(PROG)_ansi $(PROG)_c99 $(PROG).exe $(PROG)_mingw.exe

ismerged:
	$(MAKE) -C ../modules -s --no-print-directory merge_net_skeleton.c | diff ../net_skeleton.c -
	$(MAKE) -C ../modules -s --no-print-directory merge_net_skeleton.h | diff ../net_skeleton.h -

$(PROG): Makefile
	g++ -x c++ $(AMALGAMATED_SOURCES) -o $(PROG) $(CFLAGS) -lssl && ./$(PROG)

$(PROG)_ansi: Makefile
	gcc $(PEDANTIC) -ansi $(AMALGAMATED_SOURCES) -o $(PROG)_ansi $(CFLAGS) -lssl && ./$(PROG)_ansi

$(PROG)_c99: Makefile
	gcc $(PEDANTIC) -std=c99 $(AMALGAMATED_SOURCES) -o $(PROG)_c99 $(CFLAGS) -lssl && ./$(PROG)_c99

$(PROG)_mingw.exe: Makefile
	$(MINGW_GCC) $(AMALGAMATED_SOURCES) -o $(PROG)_mingw.exe -W -Wall -Werror -I..

$(PROG).exe:
	wine cl $(AMALGAMATED_SOURCES) /MD /I.. /Zi $(CFLAGS_EXTRA) && echo "Compiled OK\n"

win: $(PROG).exe
	wine $(PROG).exe

$(PROG)_coverage: Makefile
	$(MAKE) clean_coverage
	gcc $(PEDANTIC) -std=c99 -fprofile-arcs -ftest-coverage $(PROG).c $(addprefix ../modules/, $(SOURCES)) -o $(PROG)_coverage $(CFLAGS) -lssl && ./$(PROG)_coverage
	gcov -p $(PROG).c $(notdir $(SOURCES)) >/dev/null

clean: clean_coverage
	rm -rf $(PROG) $(PROG)_ansi $(PROG)_c99 $(PROG)_coverage *.txt *.exe *.obj *.o a.out *.pdb *.opt

clean_coverage:
	rm -rf *.gc* *.dSYM
