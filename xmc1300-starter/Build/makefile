
PROJ_NAME = dbg_blinky
BIN_DIR = Bin

BUILD_FILE = $(BIN_DIR)/$(PROJ_NAME)

# All of the sources participating in the build are defined here
-include sources.mk
-include Libraries/XMCLib/src/subdir.mk
-include Libraries/Newlib/subdir.mk
-include Startup/subdir.mk
-include App/subdir.mk

ifneq ($(MAKECMDGOALS),clean)
ifneq ($(strip $(S_DEPS)),)
-include $(S_DEPS)
endif

ifneq ($(strip $(S_UPPER_DEPS)),)
-include $(S_UPPER_DEPS)
endif

ifneq ($(strip $(C_DEPS)),)
-include $(C_DEPS)
endif
endif

# Add inputs and outputs from these tool invocations to the build variables 
SECONDARY_FLASH += \
$(BUILD_FILE).hex \

SECONDARY_LIST += \
$(PROJ_NAME).lst \

SECONDARY_SIZE += \
$(BUILD_FILE).siz \

LIST_FILES = $(addsuffix .o.lst, $(basename $(OBJS))) 
MAP_FILE = "$(PROJ_NAME).map"

TOOLCHAIN_ROOT=/usr/local/Caskroom/gcc-arm-embedded/9-2019-q4-major/gcc-arm-none-eabi-9-2019-q4-major
PROJECT_LOC=../

# All Target
all: $(BUILD_FILE).elf secondary-outputs

# Tool invocations
$(BUILD_FILE).elf: $(OBJS) ../linker_script.ld $(USER_OBJS)
	@echo 'Building target: $@'
	@echo 'Invoking: ARM-GCC C Linker'
	@echo " $(OBJS) " > objects.rsp
	"$(TOOLCHAIN_ROOT)/bin/arm-none-eabi-gcc" -T"../linker_script.ld" \
	-nostartfiles -Xlinker --gc-sections -specs=nano.specs -specs=nosys.specs \
	-Wl,-Map,$(MAP_FILE) -mcpu=cortex-m0 -mthumb -g -gdwarf-2 -o \
	"$(BUILD_FILE).elf" "@objects.rsp" $(USER_OBJS) $(LIBS)
	@echo 'Finished building target: $@'
	@echo;
$(BUILD_FILE).hex: $(BUILD_FILE).elf
	@echo 'Invoking: ARM-GCC Create Flash Image'
	"$(TOOLCHAIN_ROOT)/bin/arm-none-eabi-objcopy" -O ihex "$(BUILD_FILE).elf" "$(BUILD_FILE).hex"
	@echo 'Finished building: $@'
	@echo;
$(PROJ_NAME).lst: $(BUILD_FILE).elf
	@echo 'Invoking: ARM-GCC Create Listing'
	"$(TOOLCHAIN_ROOT)/bin/arm-none-eabi-objdump" -h -S "$(BUILD_FILE).elf" > "$(PROJ_NAME).lst"
	@echo 'Finished building: $@'
	@echo;
$(BUILD_FILE).siz: $(BUILD_FILE).elf
	@echo 'Invoking: ARM-GCC Print Size'
	"$(TOOLCHAIN_ROOT)/bin/arm-none-eabi-size" --format=berkeley "$(BUILD_FILE).elf"
	@echo 'Finished building: $@'
	@echo;
# Other Targets
clean:
	rm -rf $(OBJS)$(SECONDARY_FLASH)$(SECONDARY_LIST)$(SECONDARY_SIZE)\
		   $(ARM_GCC_FOR_XMC_LINKER_OUTPUT_TYPE_OUTPUTS)$(S_DEPS)$(S_UPPER_DEPS)\
		   $(C_DEPS) $(BUILD_FILE).elf $(LIST_FILES) $(MAP_FILE) objects.rsp
	-@echo;
secondary-outputs: $(SECONDARY_FLASH) $(SECONDARY_LIST) $(SECONDARY_SIZE)

.PHONY: all clean dependents
.SECONDARY:

