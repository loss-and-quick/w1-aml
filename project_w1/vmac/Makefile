ifeq ($(ARCH),)
ARCH ?= arm64
CROSS_COMPILE ?= aarch64-linux-gnu-
else ifeq ($(ARCH),arm)
SYSTEM_TYPE = SYSTEM32
CROSS_COMPILE ?= arm-linux-gnueabihf-
ccflags-y += -DSYSTEM32
else ifeq ($(ARCH),arm64)
SYSTEM_TYPE = SYSTEM64
CROSS_COMPILE ?= aarch64-linux-gnu-
ccflags-y += -DSYSTEM64
endif


ifeq ($(PROJ_NAME),p-amlogic)
SRCTOP ?= /proj/vlsi.wifi/p-amlogic
KERNELDIR ?= $(SRCTOP)/out/target/product/newton/obj/KERNEL_OBJ
ccflags-y += -DSYSTEM64
else ifeq ($(PROJ_NAME),yeke)
SRCTOP ?= /proj/vlsi.wifi/yeke_64bit
KERNELDIR ?= $(SRCTOP)/out/target/product/p281/obj/KERNEL_OBJ
ccflags-y += -DSYSTEM64
else ifeq ($(PROJ_NAME),9-xiaomi)
SRCTOP ?= /proj/vlsi.wifi/9-xiaomi
KERNELDIR ?= $(SRCTOP)/out/target/product/aquaman/obj/KERNEL_OBJ
ccflags-y += -DSYSTEM64
else ifeq ($(PROJ_NAME),gva)
SRCTOP ?= /proj/vlsi.wifi/gva
KERNELDIR ?= $(SRCTOP)/kernel-4.9/common
ccflags-y += -DSYSTEM64
else
ifeq ($(SYSTEM_TYPE),)
ARCH = arm
CROSS_COMPILE = arm-linux-gnueabihf-
ccflags-y += -DSYSTEM32
endif
SRCTOP ?= /proj/vlsi.wifi/p212_32bit
KERNELDIR ?= $(SRCTOP)/out/target/product/ampere/obj/KERNEL_OBJ
endif

#KERNELDIR ?= $(SRCTOP)/out/target/product/newton/obj/KERNEL_OBJ
#KERNELDIR ?= $(SRCTOP)/out/target/product/p281/obj/KERNEL_OBJ
#KERNELDIR ?= $(SRCTOP)/output/c1_ae402_a64_release/build/linux-amlogic-4.19-dev
#KERNELDIR ?= $(SRCTOP)/out/target/product/aquaman/obj/KERNEL_OBJ
#KERNELDIR ?= $(SRCTOP)/out/target/product/ampere/obj/KERNEL_OBJ
#KERNELDIR ?= $(SRCTOP)/kernel-4.9/common

#CC        = $(CROSS_COMPILE)gcc
#STRIP     = $(CROSS_COMPILE)strip

M ?= $(shell pwd)
ifneq ($(KERNEL_SRC),)
CUR_DIR  := $(KERNEL_SRC)/$(M)
else
CUR_DIR  := $(M)
endif
EXT_INCS := $(CUR_DIR)/../common
INCS     := -I$(CUR_DIR) -I$(EXT_INCS)
KERNEL_SRC ?= $(KERNELDIR)
#n=for normal mode,y=product mode
#n=not support WIFI_RF when boot up. y=support WIFI_RF when boot up
CONFIG_WIFI_RF_SUPPORT=y
#n=not support access bt rf reg, y=support access bt rf reg
CONFIG_BT_RF_SUPPORT=n
CONFIG_AML_CFG80211=y
CONFIG_AML_P2P=y
CONFIG_SDIO=y
CONFIG_MAC_SUPPORT=y
CONFIG_WIFI_CAPTURE=y
TARGET = vlsicomm

ccflags-y += -Wno-enum-conversion -Wno-strict-prototypes
ccflags-y += -Wno-implicit-function-declaration -Wno-int-conversion
ccflags-y += -Wno-misleading-indentation
ccflags-y += -Wno-unused-but-set-variable -Wno-strict-prototypes -Wno-extra-tokens -Wno-unused-label
ccflags-y += -Wno-macro-redefined -Wno-unused-but-set-variable -Wno-implicit-function-declaration
ccflags-y += ${INCS}  -DCONFIG_WAPI
ccflags-y += -Wno-unused-function -Wno-unused-variable #show all warning
ccflags-y += -Werror -Wno-format -Wno-error #convert warning into error
#ccflags-y += -W	#more strict, linux head file cannot pass, so give up
#ccflags-y += -w	#ignore warning
#ccflags-y += -DHALMODULE
ccflags-y += -DPHY_TEST_FUNC
ccflags-y += -DCONFIG_CONCURRENT_MODE
#ccflags-y += -O2	#kernel has
#ccflags-y += -DDRV_SUPPORT_TX_WITHDRAW
ccflags-y += -DHAL_FPGA_VER
ccflags-y += -DPROJECT_W1
ccflags-y += -DINTER_BEACON
ccflags-y += -DSRAM_CONCURRENT
ccflags-y += -DCTS_VERIFIER_GAS
#ccflags-y += -DRX_PN_CHECK
#ccflags-y += -DCONFIG_COMPAT
ccflags-y += -DCONFIG_AML_USE_STATIC_BUF
ccflags-y += -DSDIO_BUILD_IN
#ccflags-y += -DDEBUG_MALLOC
#ccflags-y += -DCONFIG_DPD_ENABLE
ccflags-y += -DFW_RF_CALIBRATION
#ccflags-y += -DPOWER_SAVE_NO_SDIO
ccflags-y += -DAML_WPA3
ccflags-y += -DDYNAMIC_BW
#ccflags-y += -DWFA_P2P_TEST

ifneq ($(UNO_GXL),)
ifeq ($(UNOENV),$(UNO_GXL))
ccflags-y += -DCONFIG_GXL
ccflags-y += -DCONFIG_HAS_WAKELOCK
#ccflags-y += -DCONFIG_RTC_ENABLE
#ccflags-y += -DCONFIG_GPIO_RESET
ccflags-y += -DUSE_GPIO_IRQ=1
ccflags-y += -DUSE_SDIO_IRQ=0
endif
endif

ifeq ($(USE_GPIO_IRQ)$(USE_SDIO_IRQ),)
ccflags-y += -DUSE_SDIO_IRQ=1
endif

ifeq ($(CLANG_VERSION), r437112)
ccflags-y += -Wno-unused-but-set-variable
endif
ifeq ($(CLANG_VERSION), r450784d)
ccflags-y += -Wno-unused-but-set-variable
endif
ifeq ($(CLANG_VERSION), r487747)
ccflags-y += -Wno-unused-but-set-variable
endif

ifneq ($(KERNELRELEASE),)

obj-m += aml_sdio.o
aml_sdio-objs += w1_sdio/w1_sdio.o

obj-m += $(TARGET).o
DISCARD :=
SRC_FILE := $(wildcard $(CUR_DIR)/*.c $(CUR_DIR)/rf_t9026/*.c $(CUR_DIR)/crypto/*.c)
TARGET_FILE = $(subst $(CUR_DIR)/,, $(SRC_FILE:%.c=%.o))

ifneq ($(CONFIG_SDIO),)
ccflags-y += -DCONFIG_SDIO_IF
ccflags-y += -DFPGA
#ccflags-y += -DCONFIG_GPIO_WAKEUP

ifeq ($(CONFIG_NOT_AMLOGIC_PLATFORM),y)
ccflags-y += -DNOT_AMLOGIC_PLATFORM
endif

ifeq ($(CONFIG_PT_SUPPORT),y)
$(warning Building PT image)
ccflags-y += -DDRV_PT_SUPPORT
KBUILD_CFLAGS_MODULE += -Wframe-larger-than=200000000
KBUILD_CFLAGS_MODULE += -Wlarger-than=20000000
else
KBUILD_CFLAGS_MODULE += -Wframe-larger-than=200000000
KBUILD_CFLAGS_MODULE += -Wlarger-than=20000000
endif

ifeq ($(CONFIG_WIFI_RF_SUPPORT),y)
ccflags-y += -DWITH_RF
ccflags-y += -DRF_T9026
endif

ifeq ($(CONFIG_MAC_SUPPORT),y)
ccflags-y += -DCONFIG_MAC_SUPPORT
ifeq ($(CONFIG_BUILDROOT),y)
ccflags-y += -DWIFIMAC_PATH=\"/etc/wifi/wifimac.txt\"
ccflags-y += -DLINUX_PLATFORM
ccflags-y += -DWPA3_PATCH
else
ccflags-y += -DWIFIMAC_PATH=\"/storage/.config/wifimac.txt\"
endif
endif

ifeq ($(CONFIG_BT_RF_SUPPORT),y)
ccflags-y += -DDRIVER_FOR_BT
endif

ifeq ($(CONFIG_WIFI_CAPTURE),y)
ccflags-y += -DWIFI_CAPTURE
endif

ccflags-y += -DUSE_T902X_RF
else#CONFIG_SDIO_IF
$(DISCARD) += wifi_hif.o
$(DISCARD) += wifi_sdio.o
endif #CONFIG_SDIO_IF

ifneq ($(CONFIG_CFG80211),)
ifneq ($(CONFIG_AML_CFG80211),)
ccflags-y += -DCONFIG_AML_CFG80211

ifeq ($(CONFIG_AML_P2P),y)
ccflags-y += -DCONFIG_P2P
ccflags-y += -DCONFIG_WFD
endif

endif#CONFIG_AML_CFG80211
endif#CONFIG_CFG80211

TARGET_LIST := $(filter-out $(DISCARD), $(TARGET_FILE))
$(TARGET)-objs = $(TARGET_LIST)

else

modules:
	-rm ./print_version.c
	perl ./create_version_file.pl
	@echo "UNO_GXL_SUB_P200=$(UNO_GXL_SUB_P200)"
	@echo "PROJECT = $(PROJ_NAME)"
	@echo "UNOENV=$(UNOENV) MAC_VER=$(MAC_VER)"
	@echo "ccflags-y=$(ccflags-y)"
	$(MAKE) ARCH=$(ARCH) CROSS_COMPILE=$(CROSS_COMPILE) -C $(KERNEL_SRC) M=$(M) modules
ifneq (${OUT_DIR},)
ifneq (${LLVM},)
	llvm-strip --strip-unneeded ${OUT_DIR}/$(M)/$(TARGET).ko
else
	$(CROSS_COMPILE)strip --strip-unneeded ${OUT_DIR}/$(M)/$(TARGET).ko
endif
else
	$(STRIP) -S $(TARGET).ko
endif

modules_install:
	@$(MAKE) INSTALL_MOD_STRIP=1 M=$(M) -C $(KERNEL_SRC) modules_install
	mkdir -p ${OUT_DIR}/../vendor_lib/modules
	cd ${OUT_DIR}/$(M)/; find -name "*.ko" -exec cp {} ${OUT_DIR}/../vendor_lib/modules/ \;

endif

clean:
	rm -rf *.o *~ core .depend .*.cmd *.ko *.mod.c .tmp_versions *.order *.symvers
	rm -rf ./rf_t9026/*.o ./rf_t9026/.*.cmd ./w1_sdio/*.o ./w1_sdio/.*.cmd ./w1_sdio/.*.o.d
	rm -rf ./crypto/*.o ./crypto/.*.cmd
	rm -rf ./print_version.c

depend .depend dep:
	$(CC) $(ccflags-y) -M *.c > .depend

ifeq (.depend,$(wildcard .depend))
include .depend
endif
