# Include the core environment definitions; this will set $(TOP).
include ../../py/mkenv.mk

# Include py core make definitions.
include $(TOP)/py/py.mk
include $(TOP)/extmod/extmod.mk

CROSS_COMPILE ?= aarch64-none-elf-

SEL4CP_BOARD ?= odroidc4_hyp
CPU ?= cortex-a55
SEL4CP_CONFIG ?= debug
BOARD_DIR := $(SEL4CP_SDK)/board/$(SEL4CP_BOARD)/$(SEL4CP_CONFIG)
SEL4CP_TOOL := $(SEL4CP_SDK)/bin/sel4cp

# Set CFLAGS and libraries.
CFLAGS += -I. -I$(BUILD) -I$(TOP) -I$(BOARD_DIR)/include -nostdlib -ffreestanding -mtune=$(CPU)
LDFLAGS += -L$(BOARD_DIR)/lib -nostdlib
LIBS += -lm -lsel4cp -Tsel4cp.ld

# Define the required source files.
SRC_C = \
	main.c \
	uart.c \
	mphalport.c \
	shared/readline/readline.c \
	shared/runtime/gchelper_generic.c \
	shared/runtime/pyexec.c \
	shared/runtime/stdout_helpers.c \
	shared/libc/string0.c \
	shared/libc/printf.c \
	shared/libc/__errno.c \

# Define source files containung qstrs.
SRC_QSTR += shared/readline/readline.c shared/runtime/pyexec.c

# Define the required object files.
OBJ = $(PY_CORE_O) $(addprefix $(BUILD)/, $(SRC_C:.c=.o))

IMAGE_FILE = $(BUILD)/loader.img
REPORT_FILE = $(BUILD)/report.txt

all: $(IMAGE_FILE)

$(BUILD)/micropython.elf: $(OBJ)
	$(ECHO) "LINK $@"
	$(Q)$(CC) $(LDFLAGS) -o $@ $^ $(LIBS)
	$(Q)$(SIZE) $@

$(IMAGE_FILE) $(REPORT_FILE): $(BUILD)/micropython.elf micropython.system
	$(SEL4CP_TOOL) micropython.system --search-path $(BUILD) --board $(SEL4CP_BOARD) --config $(SEL4CP_CONFIG) -o $(IMAGE_FILE) -r $(REPORT_FILE)

# Include remaining core make rules.
include $(TOP)/py/mkrules.mk
