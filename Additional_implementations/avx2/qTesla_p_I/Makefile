CC=gcc
ifeq "$(CC)" "gcc"
    COMPILER=gcc
else ifeq "$(CC)" "clang"
    COMPILER=clang
endif

ARCHITECTURE=_AMD64_
ifeq "$(ARCH)" "x64"
    ARCHITECTURE=_AMD64_
endif

AVX2=-D _AVX2_ -mavx2

AR=ar rcs
RANLIB=ranlib

CFLAGS = -std=gnu11 -O3 -D $(ARCHITECTURE) $(AVX2) -D __LINUX__ -fomit-frame-pointer
LDFLAGS=-lm -L/usr/lib/ -lssl -lcrypto 

ifeq "$(CC)" "gcc"
    CFLAGS+= -march=native
endif

DFLAG=
ifeq "$(STATS)" "TRUE"
    DFLAG=-DSTATS
endif

OBJECTS_ASM_p_I = objs_p_I/s_consts.o objs_p_I/poly_mul1024.o
OBJECTS_EXTRAS = objs/fips202x4.o objs/KeccakP-1600-times4-SIMD256.o

OBJECTS_p_I = objs_p_I/sign.o objs_p_I/pack.o objs_p_I/sample.o objs_p_I/gauss.o objs_p_I/poly.o objs_p_I/consts.o $(OBJECTS_ASM_p_I) objs/fips202.o objs/random.o $(OBJECTS_EXTRAS)
SOURCE_TEST = tests/cpucycles.c tests/test_qtesla.c
SOURCE_KATS_GEN  = tests/rng.c tests/PQCgenKAT_sign.c
SOURCE_KATS_TEST = tests/rng.c tests/PQCtestKAT_sign.c

all: lib_p_I tests

objs_p_I/%.o: %.c
	@mkdir -p $(@D)
	$(CC) -c $(CFLAGS) $(DFLAG) -D _qTESLA_p_I_ $< -o $@

objs/random.o: random/random.c
	@mkdir -p $(@D)
	$(CC) -c $(CFLAGS) random/random.c -o objs/random.o

objs/fips202.o: sha3/fips202.c
	@mkdir -p $(@D)
	$(CC) -c $(CFLAGS) sha3/fips202.c -o objs/fips202.o
	
objs/fips202x4.o: sha3/fips202x4.c
	$(CC) -c $(CFLAGS) sha3/fips202x4.c -o objs/fips202x4.o

objs/KeccakP-1600-times4-SIMD256.o: sha3/keccak4x/KeccakP-1600-times4-SIMD256.c
	$(CC) -c $(CFLAGS) sha3/keccak4x/KeccakP-1600-times4-SIMD256.c -o objs/KeccakP-1600-times4-SIMD256.o

objs_p_I/poly_mul1024.o: poly_mul1024.S
	@mkdir -p $(@D)
	$(CC) -c $(CFLAGS) poly_mul1024.S -o objs_p_I/poly_mul1024.o

lib_p_I: $(OBJECTS_p_I)
	rm -rf lib_p_I
	mkdir lib_p_I
	$(AR) lib_p_I/libqtesla.a $^
	$(RANLIB) lib_p_I/libqtesla.a

tests: lib_p_I
	$(CC) $(CFLAGS) -L./lib_p_I -D _qTESLA_p_I_ $(SOURCE_TEST) $(DFLAG) -lqtesla $(LDFLAGS) -o test_qtesla-p-I $(ARM_SETTING)
	$(CC) $(CFLAGS) -L./lib_p_I -D _qTESLA_p_I_ $(SOURCE_KATS_GEN) $(DFLAG) -lqtesla $(LDFLAGS) -o PQCgenKAT_sign-p-I $(ARM_SETTING)
	$(CC) $(CFLAGS) -L./lib_p_I -D _qTESLA_p_I_ $(SOURCE_KATS_TEST) $(DFLAG) -lqtesla $(LDFLAGS) -o PQCtestKAT_sign-p-I $(ARM_SETTING)

.PHONY: clean

clean:
	rm -rf objs* lib* test_qtesla-* PQCgenKAT_sign-* PQCtestKAT_sign-* PQCsignKAT_qTesla*