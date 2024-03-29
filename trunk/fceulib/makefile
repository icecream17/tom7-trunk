# Makefile made by tom7.
default: bench.exe emulator_test.exe difftrace.exe aot.exe

all: emulator_test.exe bench.exe fm2tofm7.exe difftrace.exe dumptrace.exe make-comprehensive-history.exe

# -fno-strict-aliasing
# -Wstrict-overflow=3
# -std=c++11
# -DDISABLE_SOUND=1 -DDISABLE_VIDEO=1
CXXFLAGS=-std=c++17 -Wall -Wno-deprecated -Wno-sign-compare -I/usr/local/include 
# XXX -O2
OPT=-g -O2  # -O3 -fno-strict-aliasing
# try -flto!
FLTO=

ARCH=-m64
# ARCH=-m32

ifdef OSX
CXX=g++
CC=gcc
else
ifdef LINUX
CXX=g++
CC=gcc
PLATFORMLINK=-lpthread -static
else
# for 64 bits on windows
CXX=x86_64-w64-mingw32-g++
CC=x86_64-w64-mingw32-gcc

# PLATFORMCFLAGS= -D__MINGW32__ -D_GLIBCXX_HAS_GTHREADS -mthreads
PLATFORMCFLAGS=
# without static, can't find lz or lstdcxx maybe?
PLATFORMLINK=-mthreads -Wl,--subsystem,console -lwinpthread -L. -static
endif
endif

# Suppress compilation commands, but show some indication of progress.
# (Explicitly invoke bash to get shell builtin, since on OS X echo
# otherwise treats -n literally.)

# AOT_INSTRUMENTATION is cheap but not free instrumentation in x6502
# core that's used to histogram the PC locations that are executed.
INSTRUMENT=-DAOT_INSTRUMENTATION=1

%.o : %.cc
	@$(CXX) $(CPPFLAGS) $(CXXFLAGS) $(INSTRUMENT) -DAOT=1 -c -o $@ $<
	@bash -c "echo -n '.'"

# version of PPU that does not call the AOT entry point at all.
%-noaot.o : %.cc
	@$(CXX) $(CPPFLAGS) $(CXXFLAGS) $(INSTRUMENT) -c -o $@ $<
	@bash -c "echo -n ':'"

# If you don't have SDL, you can leave these out, and maybe it still works.
LINKSDL= -mno-cygwin -lm -luser32 -lgdi32 -lwinmm -ldxguid

# Emulator uses city, yeah? See if we can make this work with no deps.
INCLUDES=-I "../cc-lib" -I "../cc-lib/city" -I "."

# tom7 added -mthreads on 9/7!
CPPFLAGS=-DPSS_STYLE=1 -DDUMMY_UI $(ARCH) $(OPT) $(PLATFORMCFLAGS) -DHAVE_ALLOCA -DNOWINSTUFF $(INCLUDES) $(PROFILE) $(FLTO) --std=c++11

# Should just be used for testing/utilities.
CCLIBOBJECTS=../cc-lib/util.o ../cc-lib/arcfour.o ../cc-lib/base/logging.o ../cc-lib/base/stringprintf.o ../cc-lib/city/city.o ../cc-lib/rle.o ../cc-lib/stb_image_write.o ../cc-lib/wavesave.o

MAPPEROBJECTS=mappers/6.o mappers/61.o mappers/24and26.o mappers/51.o mappers/69.o mappers/77.o mappers/40.o mappers/mmc2and4.o mappers/71.o mappers/79.o mappers/41.o mappers/72.o mappers/80.o mappers/42.o mappers/62.o mappers/73.o mappers/85.o mappers/emu2413.o mappers/46.o mappers/65.o mappers/75.o mappers/50.o mappers/67.o mappers/76.o mappers/tengen.o

# XXX: Probably a bunch of this can go?
UTILSOBJECTS= utils/memory.o utils/crc32.o utils/endian.o utils/md5.o utils/xstring.o

# unused - 
# boards/a9711.o
# boards/n-c22m.o
# boards/179.o
# only used via old _init interface:
# boards/subor.o

BOARDSOBJECTS=boards/mmc1.o boards/mmc5.o boards/datalatch.o boards/mmc3.o boards/01-222.o boards/32.o boards/gs-2013.o boards/103.o boards/33.o boards/h2288.o boards/106.o boards/34.o boards/karaoke.o boards/108.o boards/3d-block.o boards/kof97.o boards/112.o boards/411120-c.o boards/konami-qtai.o boards/116.o boards/43.o boards/ks7012.o boards/117.o boards/57.o boards/ks7013.o boards/120.o boards/603-5052.o boards/ks7017.o boards/121.o boards/68.o boards/ks7030.o boards/12in1.o boards/8157.o boards/ks7031.o boards/15.o boards/82.o boards/ks7032.o boards/151.o boards/8237.o boards/ks7037.o boards/156.o boards/830118c.o boards/ks7057.o boards/164.o boards/88.o boards/le05.o boards/168.o boards/90.o boards/lh32.o boards/17.o boards/91.o boards/lh53.o boards/170.o boards/95.o boards/malee.o boards/175.o boards/96.o boards/176.o boards/99.o boards/177.o boards/178.o boards/a9746.o boards/18.o boards/ac-08.o boards/n625092.o boards/183.o boards/addrlatch.o boards/novel.o boards/185.o boards/ax5705.o boards/onebus.o boards/186.o boards/pec-586.o boards/187.o boards/bb.o boards/sa-9602b.o boards/189.o boards/bmc13in1jy110.o boards/193.o boards/bmc42in1r.o boards/sc-127.o boards/199.o boards/bmc64in1nr.o boards/sheroes.o boards/208.o boards/bmc70in1.o boards/sl1632.o boards/222.o boards/bonza.o boards/smb2j.o boards/225.o boards/bs-5.o boards/228.o boards/cityfighter.o boards/super24.o boards/230.o boards/dance2000.o boards/n106.o boards/supervision.o boards/232.o boards/t-227-1.o boards/234.o boards/deirom.o boards/t-262.o boards/sachen.o boards/235.o boards/dream.o boards/244.o boards/edu2000.o boards/tf-1201.o boards/bandai.o boards/246.o boards/famicombox.o boards/transformer.o boards/252.o boards/fk23c.o boards/vrc2and4.o boards/253.o boards/ghostbusters63in1.o boards/vrc7.o boards/28.o boards/gs-2004.o boards/yoko.o

# old-style, not converted
# boards/tengen.o

INPUTOBJECTS=input/arkanoid.o input/ftrainer.o input/oekakids.o input/suborkb.o input/bworld.o input/hypershot.o input/powerpad.o input/toprider.o input/cursor.o input/mahjong.o input/quiz.o input/zapper.o input/fkb.o input/shadow.o

FCEUOBJECTS=cart.o version.o emufile.o fceu.o fds.o file.o filter.o ines.o input.o palette.o sound.o state.o unif.o vsuni.o x6502.o git.o fc.o
# ugh.
AOT_OBJECTS=ppu.o
NO_AOT_OBJECTS=ppu-noaot.o

#  $(DRIVERS_COMMON_OBJECTS)
EMUOBJECTS=$(FCEUOBJECTS) $(MAPPEROBJECTS) $(UTILSOBJECTS) $(PALLETESOBJECTS) $(BOARDSOBJECTS) $(INPUTOBJECTS)

# included in all tests, etc.
BASEOBJECTS=$(CCLIBOBJECTS)

FCEULIB_OBJECTS=emulator.o headless-driver.o stringprintf.o trace.o tracing.o
# simplefm2.o emulator.o util.o

# experimental! Need a much better way to do this...
GAME_OBJECTS= # mario_33280.o mario_34304.o mario_33792.o mario_32768.o mario_36352.o mario_34816.o mario_35328.o mario_35840.o mario_37376.o mario_36864.o mario_38400.o mario_38912.o mario_39424.o mario_41472.o mario_41984.o mario_40960.o mario_37888.o mario_40448.o mario_42496.o mario_43008.o mario_43520.o mario_44032.o mario_44544.o mario_39936.o mario_45056.o mario_45568.o mario_46080.o mario_47616.o mario_46592.o mario_47104.o mario_48128.o mario_48640.o mario_49664.o mario_51200.o mario_50176.o mario_51712.o mario_50688.o mario_49152.o mario_52224.o mario_54272.o mario_53760.o mario_53248.o mario_54784.o mario_55296.o mario_52736.o mario_56320.o mario_55808.o mario_57344.o mario_57856.o mario_56832.o mario_60928.o mario_58368.o mario_60416.o mario_61440.o mario_58880.o mario_59904.o mario_61952.o mario_59392.o mario_64000.o mario_63488.o mario_62464.o mario_64512.o mario_65024.o mario_62976.o  mario.o

GAME_OBJECTS= # contra_54272.o contra_49152.o contra_51712.o contra_55296.o contra_56320.o contra_52736.o contra_54784.o contra_55808.o contra_52224.o contra_56832.o contra_53248.o contra_49664.o contra_53760.o contra_59904.o contra_50176.o contra_57344.o contra_61952.o contra_58880.o contra_59392.o contra_60416.o contra_51200.o contra_58368.o contra_61440.o contra_57856.o contra_60928.o contra_65024.o contra_50688.o contra_64512.o contra_62464.o contra_63488.o contra_64000.o contra_62976.o  contra.o

OBJECTS_NO_GAMES=$(BASEOBJECTS) $(EMUOBJECTS) $(FCEULIB_OBJECTS)
OBJECTS=$(OBJECTS_NO_GAMES) $(GAME_OBJECTS) $(AOT_OBJECTS)

LFLAGS= $(ARCH) $(PLATFORMLINK) $(LINKNETWORKING) -lz $(OPT) $(FLTO) $(PROFILE) # -Wl,--subsystem,console
# -static -Wl,--subsystem,console
# -Wl,--subsystem,console
# -static -fwhole-program
# -static

# $* means the text matched by %.
results-%.txt : make-comprehensive-history.exe
	./make-comprehensive-history.exe --rev $*

fm2tofm7.exe : $(CCLIBOBJECTS) fm2tofm7.o simplefm2.o simplefm7.o
	$(CXX) $^ -o $@ $(LFLAGS)

difftrace.exe : stringprintf.o trace.o difftrace.o
	$(CXX) $^ -o $@ $(LFLAGS)

dumptrace.exe : stringprintf.o trace.o dumptrace.o
	$(CXX) $^ -o $@ $(LFLAGS)

emulator_test.exe : $(OBJECTS) test-util.o emulator_test.o simplefm2.o
	$(CXX) $^ -o $@ $(LFLAGS)

fm2wav.exe : $(OBJECTS) $(CCLIBOBJECTS) fm2wav.o simplefm2.o
	$(CXX) $^ -o $@ $(LFLAGS)

bench.exe : $(OBJECTS) test-util.o bench.o simplefm2.o simplefm7.o
	$(CXX) $^ -o $@ $(LFLAGS)

aot.exe : $(OBJECTS_NO_GAMES) ppu-noaot.o aot.o test-util.o
	$(CXX) $^ -o $@ $(LFLAGS)

make-comprehensive-history.exe : $(BASEOBJECTS) make-comprehensive-history.o
	$(CXX) $^ -o $@ $(LFLAGS)

sound_dmc_test.exe : $(CCLIBOBJECTS) sound_dmc_test.o test-util.o
	$(CXX) $^ -o $@ $(LFLAGS)

aot-analyze.exe : aot-analyze.o $(OBJECTS_NO_GAMES) ppu-noaot.o simplefm2.o
	$(CXX) $^ -o $@ $(LFLAGS)

# also mario_*.cc, but make suxxxx
mario.cc : aot.exe aot-prelude.inc
	./aot.exe mario

contra.cc : aot.exe aot-prelude.inc
	./aot.exe contra

# mario.o : mario.cc
# 	$(CXX) $(CPPFLAGS) $(CXXFLAGS) $< -g -c -o mario.o

test : emulator_test.exe
	time ./emulator_test.exe

clean :
	rm -f *_test.exe bench.exe difftrace.exe *.o $(EMUOBJECTS) $(CCLIBOBJECTS) gmon.out

veryclean : clean
	rm -f trace.bin mario*.cc contra*.cc
