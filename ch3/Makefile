TARGET = px86
PROGRAM = crt0.bin
PROGRAM_CALL = call-test.bin
PROGRAM_LEAVE = leave-test.bin
PROGRAM_LEAVE2 = leave-test2.bin
OBJS = main.o emulator_function.o instruction.o modrm.o

CC = gcc
CFLAGS += -O2 -Wall -Wextra

.PHONY: all run-call-test run-crt run-crt2
all : $(TARGET)

run-call-test : $(TARGET) $(PROGRAM_CALL)
	./$(TARGET) $(PROGRAM_CALL)

run-crt: $(TARGET) $(PROGRAM_LEAVE)
	./$(TARGET) $(PROGRAM_LEAVE)

$(PROGRAM_LEAVE) : crt0.o leave-test.o
	$(LD) -m elf_i386 --entry=start --oformat=binary -Ttext 0x7c00 -o $@ $^

leave-test.o : leave-test.c
	$(CC) -O0 -march=i386 -m32 -nostdlib -fno-asynchronous-unwind-tables -g -fno-stack-protector -fno-pie -c -o $@ $<

run-crt2: $(TARGET) $(PROGRAM_LEAVE2)
	./$(TARGET) $(PROGRAM_LEAVE2)

$(PROGRAM_LEAVE2) : crt0.o leave-test2.o
	$(LD) -m elf_i386 --entry=start --oformat=binary -Ttext 0x7c00 -o $@ $^

leave-test2.o : leave-test2.c
	$(CC) -O0 -march=i386 -m32 -nostdlib -fno-asynchronous-unwind-tables -g -fno-stack-protector -fno-pie -c -o $@ $<


%.bin : %.asm
	nasm -f bin -o $@ $<

crt0.o : crt0.asm
	nasm -f elf $<

%.o : %.c
	$(CC) $(CFLAGS) -c $<

$(TARGET) : $(OBJS)
	$(CC) -o $@ $(OBJS)


clean:
	rm -f $(TARGET) $(PROGRAM) $(OBJS) crt0.o call-test.bin leave-test.o leave-test.bin leave-test2.o leave-test2.bin