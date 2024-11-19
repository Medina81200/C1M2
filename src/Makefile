#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# <Put a Description Here>
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      <Put a description of the supported targets here>
#
# Platform Overrides:
#      <Put a description of the supported Overrides here
#
#------------------------------------------------------------------------------
include sources.mk

# Platform Overrides
PLATFORM ?= MSP432
TARGET_FOLDER = C1M2

#OS detection for platform-specific flags
UNAME_S = $(shell uname -s)

# Architectures and platform specific flags
ifeq ($(PLATFORM),MSP432)
	#MSP432 Specific setup
	LINKER_FILE=../msp432p401r.lds 
	CPU=cortex-m4
	ARCH=armv7e-m
	SPECS=nosys.specs

	#Cross - compiler and linker for MSP432
	CC=arm-none-eabi-gcc
	LD=arm-none-eabi-ld
	#Compiler and linker flags for MSP432

	LDFLAGS=-Wl,-Map=$(TARGET_FOLDER).map -T $(LINKER_FILE)
	CFLAGS=-mcpu=$(CPU)\
		-mthumb \
	 	-march=$(ARCH) \
	 	-mfloat-abi=hard \
	 	-mfpu=fpv4-sp-d16 \
	 	--specs=$(SPECS) \
	 	-Wall \
	 	-Werror \
	 	-O0 \
	 	-g \
	 	-std=c99
		CPPFLAGs=-DMSP432 $(INCLUDES)

else
	#Host platform seutp
		CC=gcc
		LD=ld

	#Compiler flags for the HOST platform
		CFLAGS=-Wall \
		-Werror \
		-O0 \
		-g \
		-std=c99

		CPPFLAGS=-DHOST $(INCLUDES)
	#Specific linker flags
	ifeq ($(UNAME_S),DARWIN)
		LDFLAGS=-Wl, -map, $(TARGET_FOLDER).map
	else
        	LDFLAGS=-Wl, -map, $(TARGET_FOLDER).map
	endif
endif

#Build targets
.PHONY: all build clean compile-all
all: build

#Generate preprocessed output (.i files)

%.i: %.c
	$(CC) -E $(CPPFLAGS) $< -o $@
# Generate assembly output (.asm files)
%.asm: %.c
	$(CC) -S $(CFLAGS) $(CPPFLAGS) $< -o $@

# Special pattern rules for interrupt handlers (MSP432 specific)
interrupts_msp432p401r_gcc.o: interrupts_msp432p401r_gcc.c
	$(CC) -c $(CFLAGS) -mgeneral-regs-only $(CPPFLAGS) -MD -MP -MF $*.d $< -o $@

startup_msp432p401r_gcc.o: startup_msp432p401r_gcc.c
	$(CC) -c $(CFLAGS) -mgeneral-regs-only $(CPPFLAGS) -MD -MP -MF $*.d $< -o $@


compile-all: $(SOURCES:.c=.o)

build: $(TARGET_FOLDER).out
	@echo "Build successfully completed"
	size $(TARGET_FOLDER).out

$(TARGET_FOLDER).out: $(SOURCES:.c=.o)
	 $(CC) $(SOURCES:.c=.o) $(CFLAGS) $(LDFLAGS) -o $@

clean:
	rm -f *.o *.i *.asm *.d $(TARGET_FOLDER).out $(TARGET_FOLDER).map

