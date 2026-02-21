PROJ = main
CPU ?= cortex-m4
BOARD ?= stm32vldiscovery

FREERTOS_PATH = ../FreeRTOS-Kernel

OBJ = boot.o \
      start.o \
      portFunctions.o \
      $(FREERTOS_PATH)/tasks.o \
      $(FREERTOS_PATH)/list.o \
      $(FREERTOS_PATH)/queue.o \
      $(FREERTOS_PATH)/event_groups.o \
      $(FREERTOS_PATH)/stream_buffer.o \
      $(FREERTOS_PATH)/portable/MemMang/heap_4.o \
      $(FREERTOS_PATH)/portable/GCC/ARM_CM4F/port.o 
     


INC = -I$(FREERTOS_PATH)/portable/GCC/ARM_CM4F \
      -I$(FREERTOS_PATH)/include/ \
      -I./

.PHONY: all
all: $(PROJ).elf

%.o: %.S
	arm-none-eabi-as -mthumb -mcpu=$(CPU) -mfloat-abi=hard -mfpu=fpv4-sp-d16  -g  -c $^ -o $@
%.o: %.c
	arm-none-eabi-gcc $(INC) -mthumb -mcpu=$(CPU) --specs=nano.specs -mfloat-abi=hard -mfpu=fpv4-sp-d16 -g -O0  -c $^ -o $@

$(PROJ).elf: $(OBJ)
	#arm-none-eabi-ld -Tmap.ld $^ -o $@
	arm-none-eabi-gcc -mthumb -mcpu=cortex-m4 -mfloat-abi=hard -mfpu=fpv4-sp-d16 --specs=nano.specs -Tmap.ld $(OBJ) -o $@ -nostartfiles
	arm-none-eabi-objdump -D -S $@ > $@.lst
	arm-none-eabi-readelf -a $@ > $@.debug

qemu:
	qemu-system-arm -S -M $(BOARD) -cpu $(CPU) -nographic -kernel \
	$(PROJ).elf -gdb tcp::1234
gdb:
	gdb-multiarch -q main.elf -ex "target remote localhost:1234"

clean:
	rm -rf *.out *.elf .gdb_history *.lst *.debug $(OBJ)

