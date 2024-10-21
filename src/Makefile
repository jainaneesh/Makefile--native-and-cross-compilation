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
#      all	    : Generates the final executable by compiling all the project source files
#      compile-all  : This phony will compile all the object files BUT will NOT link them
#      clean        : Purges all the object(s), preprocessed outputs, assembly outputs and executable
#      		      files
#
# Platform Overrides:
#      <Put a description of the supported Overrides here
#      PLATFORM=MSP32 : Compiles the project for MSP32 embedded target.
#      PLATFORM=HOST  : Compiles the project for the host machine
#
#------------------------------------------------------------------------------
# Defining variables
TARGET = c1m2.out
MAPFILE = c1m2.map
include sources.mk

# Platform Overrides
PLATFORM ?= HOST 

# General flags used regardless of the target
LDFLAGS = -Wl,-Map=$(MAPFILE)
CFLAGS = -Wall -g -O0 -std=c99
CPPFLAGS = 

ifeq ($(PLATFORM),MSP432)
	LINKER_FILE = -T ../msp432p401r.lds
	CPU = -mcpu=cortex-m4 
	ARCH = -march=armv7e-m
	SPECS = --specs=nosys.specs
	THUMB = -mthumb
   # Compiler Flags and Defines
	CC = arm-none-eabi-gcc
	LD = arm-none-eabi-gcc
	CFLAGS += $(CPU) $(THUMB) $(ARCH)
	LDFLAGS += $(LINKER_FILE) $(CPU) $(ARCH) $(SPECS)
	CPPFLAGS += -DMSP432
	#SOURCES = main.c memory.c interrupts_msp432p401r_gcc.c startup_msp432p401r_gcc.c system_msp432p401r.c
	#INCLUDES = -I../include/common -I../include/msp432 -I../include/CMSIS
else ifeq ($(PLATFORM),HOST)
   # Architectures Specific Flags
	LINKER_FILE = 
	CPU = 
	ARCH = 
	SPECS = 
   # Compiler Flags and Defines
	CC = gcc
	LD = gcc
	CPPFLAGS += -DHOST
	#SOURCES = main.c memory.c interrupts_msp432p401r_gcc.c startup_msp432p401r_gcc.c system_msp432p401r.c
	#INCLUDES = -Iinclude/common -Iinclude/host
endif

# Define build rules
.PHONY: build compile-all clean show-macros

build: $(TARGET)

compile-all: $(SOURCES:.c=.o)

%.o: %.c
	$(CC) $(CFLAGS) $(CPPFLAGS) $(INCLUDES) -c $< -o $@

%.i: %.c
	$(CC) $(CFLAGS) $(CPPFLAGS) $(INCLUDES) -E $< -o $@

%.asm: %.c
	$(CC) $(CFLAGS) $(CPPFLAGS) $(INCLUDES) -S $< -o $@
	objdump -d $@ > $@.dump

$(TARGET): $(SOURCES:.c=.o)
	$(CC) $(CFLAGS) $(CPPFLAGS) $(INCLUDES) $^ $(LDFLAGS) -o $@
	size $@
show-macros:
	$(CC) $(CPPFLAGS) $(INCLUDES) -dM -E $(SOURCES) > macros.txt

OBJECTS = $(SOURCES:.c=.o)
PREPROP = $(SOURCES:.c=.i)
ASSEMBL = $(SOURCES:.c=.asm)

clean:
	rm -f *.o *.i *.asm $(TARGET) $(MAPFILE)
	@echo "Cleaning up..."
	@echo "Removing object file: $(SOURCES:.c=.o)"
	rm -f $(SOURCES:.c=.o)
	rm -f $(SOURCES:.c=.i) $(SOURCES:.c=.asm) $(SOURCES:.c=.asm.dump) $(TARGET) $(MAPFILE)
	#rm -f $(OBJECTS) $(PREPROP) $(ASSEMBL) $(SOURCES:.c=.asm.dump) $(TARGET) $(MAPFILE)
