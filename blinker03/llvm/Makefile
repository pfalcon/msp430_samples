OPT = opt-3.0
LLC = llc-3.0

AOPS = --warn --fatal-warnings

OOPS = -std-compile-opts -strip-debug -disable-simplify-libcalls

LOPS = -Wall -m32 -emit-llvm 

HLOPS = -Wall -emit-llvm 

LLCOPS = -march=msp430

APP = blinker03

all : $(APP).opt.hex $(APP).norm.hex $(APP).cbackend.elf

clean :
	rm -f *.o
	rm -f *.elf
	rm -f *.list
	rm -f *.bc
	rm -f *.hex
	rm -f *.norm.s
	rm -f *.opt.s


startup.o : startup.s
	msp430-as startup.s -o startup.o

reset.o : reset.s
	msp430-as reset.s -o reset.o


$(APP).bc : $(APP).c
	clang $(LOPS) -c $(APP).c -o $(APP).bc

$(APP).opt.o : $(APP).bc
	$(OPT) $(OOPS) $(APP).bc -f -o $(APP).opt.bc
	$(LLC) $(LLCOPS) $(APP).opt.bc -o $(APP).opt.s
	msp430-as $(APP).opt.s -o $(APP).opt.o

$(APP).opt.elf : memmap startup.o reset.o $(APP).opt.o
	msp430-ld -T memmap reset.o $(APP).opt.o startup.o -o $(APP).opt.elf

$(APP).opt.hex : $(APP).opt.elf
	msp430-objdump -D $(APP).opt.elf > $(APP).opt.list
	msp430-objcopy -O ihex $(APP).opt.elf $(APP).opt.hex



$(APP).norm.o : $(APP).bc
	$(LLC) $(LLCOPS) $(APP).bc -o $(APP).norm.s
	msp430-as $(APP).norm.s -o $(APP).norm.o

$(APP).norm.elf : memmap startup.o reset.o $(APP).norm.o
	msp430-ld -T memmap reset.o $(APP).norm.o startup.o -o $(APP).norm.elf

$(APP).norm.hex : $(APP).norm.elf
	msp430-objdump -D $(APP).norm.elf > $(APP).norm.list
	msp430-objcopy -O ihex $(APP).norm.elf $(APP).norm.hex


$(APP).cbackend.o : $(APP).bc
	$(LLC) -march=c $(APP).bc -o $(APP).cbackend.c
	clang $(LOPS) -c $(APP).cbackend.c -o $(APP).cbackend.bc
	$(OPT) $(OOPS) $(APP).cbackend.bc -f -o $(APP).cbackend.opt.bc
	$(LLC) $(LLCOPS) $(APP).cbackend.opt.bc -o $(APP).cbackend.s
	msp430-as $(APP).cbackend.s -o $(APP).cbackend.o

$(APP).cbackend.elf : memmap startup.o reset.o $(APP).cbackend.o
	msp430-ld -T memmap reset.o $(APP).cbackend.o startup.o -o $(APP).cbackend.elf

