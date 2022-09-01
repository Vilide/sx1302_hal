### get external defined data

include ../target.cfg

### User defined build options

ARCH ?=
CROSS_COMPILE ?=
BUILD_MODE := release
OBJDIR = obj

### ----- AVOID MODIFICATIONS BELLOW ------ AVOID MODIFICATIONS BELLOW ----- ###

ifeq '$(BUILD_MODE)' 'alpha'
  $(warning /\/\/\/ Building in 'alpha' mode \/\/\/\)
  WARN_CCOMPFLAGS   :=
  OPT_CCOMPFLAGS    := -O0
  DEBUG_CCOMPFLAGS  := -g
  LDFLAGS       :=
else ifeq '$(BUILD_MODE)' 'debug'
  $(warning /\/\/\/  Building in 'debug' mode \/\/\/\)
  WARN_CCOMPFLAGS   := -Wall -Wextra
  OPT_CCOMPFLAGS    := -O2
  DEBUG_CCOMPFLAGS  := -g
  LDFLAGS       :=
else ifeq  '$(BUILD_MODE)' 'release'
  $(warning /\/\/\/  Building in 'release' mode \/\/\/\)
  WARN_CCOMPFLAGS   := -Wall -Wextra
  OPT_CCOMPFLAGS    := -O2 -ffunction-sections -fdata-sections
  DEBUG_CCOMPFLAGS  :=
  LDFLAGS       := -Wl,--gc-sections
else
  $(error BUILD_MODE must be set to either 'alpha', 'debug' or 'release')
endif

### Application-specific variables
APP_NAME := chip_id
APP_LIBS := -lloragw -lm -ltinymt32 -lrt

### Environment constants
LIB_PATH := ../libloragw

### Expand build options
CCOMPFLAGS := -std=c99 $(WARN_CCOMPFLAGS) $(OPT_CCOMPFLAGS) $(DEBUG_CCOMPFLAGS)
CC ?=
AR := $(CROSS_COMPILE)ar

### General build targets
all: $(APP_NAME)

clean:
	rm -f obj/*.o
	rm -f $(APP_NAME)

install:
ifneq ($(strip $(TARGET_IP)),)
 ifneq ($(strip $(TARGET_DIR)),)
  ifneq ($(strip $(TARGET_USR)),)
	@echo "---- Copying chip_id files to $(TARGET_IP):$(TARGET_DIR)"
	@ssh -i $(SSH_KEY) $(TARGET_USR)@$(TARGET_IP) "mkdir -p $(TARGET_DIR)"
	@scp -i $(SSH_KEY) chip_id $(TARGET_USR)@$(TARGET_IP):$(TARGET_DIR)
  else
	@echo "ERROR: TARGET_USR is not configured in target.cfg"
  endif
 else
	@echo "ERROR: TARGET_DIR is not configured in target.cfg"
 endif
else
	@echo "ERROR: TARGET_IP is not configured in target.cfg"
endif

$(OBJDIR):
	mkdir -p $(OBJDIR)

### Compile main program
$(OBJDIR)/$(APP_NAME).o: src/$(APP_NAME).c | $(OBJDIR)
	$(CC) -c $< -o $@ $(CCOMPFLAGS) -Iinc -I../libloragw/inc

### Link everything together
$(APP_NAME): $(OBJDIR)/$(APP_NAME).o $(LIB_PATH)/libloragw.a
	$(CC) -L$(LIB_PATH) -L../libtools $^ -o $@ $(LDFLAGS) $(APP_LIBS)

### EOF
