#
# Copyright (C) 2008 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk
include $(INCLUDE_DIR)/kernel.mk

PKG_NAME:=rtl8723du
PKG_VERSION:=5.6.3
PKG_RELEASE:=5
PKG_LICENSE:=GPLv2

PKG_MAINTAINER:=Han Pengfei <pengphei@foxmail.com>
PKG_BUILD_PARALLEL:=1

include $(INCLUDE_DIR)/package.mk

define KernelPackage/rtl8723du
  SUBMENU:=Wireless Drivers
  TITLE:=rtl8723du Support
  DEPENDS:=@USB_SUPPORT +kmod-cfg80211 +@DRIVER_WEXT_SUPPORT +@DRIVER_11N_SUPPORT +@DRIVER_11AC_SUPPORT
  FILES:=$(PKG_BUILD_DIR)/rtl8723du.ko
  AUTOLOAD:=$(call AutoLoad,90,rtl8723du)
  PROVIDES:=kmod-rtl8723du
endef

define KernelPackage/rtl8723du/description
 Kernel module for rtl8723 wireless module.
endef

define Package/rtl8723du-firmware-default
  SECTION:=firmware
  CATEGORY:=Firmware
  TITLE:=RTK $(1) firmware
  DEPENDS:=+kmod-rtl8723du
endef

define Package/rtl8723du-btcoexist-firmware
$(call Package/rtl8723du-firmware-default,8723du)
endef

define Package/rtl8723du-btcoexist-firmware/install
	$(INSTALL_DIR) $(1)/lib/firmware/8723du
	$(INSTALL_BIN) ./files/wifi_ant_isolation.txt $(1)/lib/firmware/8723du/
endef


EXTRA_KCONFIG:=\
	CONFIG_RTL8723DU=m \
	CONFIG_IOCTL_CFG80211=y \
	CONFIG_LITTLE_ENDIAN=y \
	CONFIG_BT_COEXIST_SOCKET_TRX=y \
	CONFIG_CONCURRENT_MODE=y \
	USER_MODULE_NAME=rtl8723du 

EXTRA_CFLAGS:= \
	$(patsubst CONFIG_%, -DCONFIG_%=1, $(patsubst %=m,%,$(filter %=m,$(EXTRA_KCONFIG)))) \
	$(patsubst CONFIG_%, -DCONFIG_%=1, $(patsubst %=y,%,$(filter %=y,$(EXTRA_KCONFIG)))) \
	-DRTW_USE_CFG80211_STA_EVENT \
 	-I$(PKG_BUILD_DIR)/include \
 	-I$(PKG_BUILD_DIR)/hal/phydm

NOSTDINC_FLAGS = \
	-I$(STAGING_DIR)/usr/include/mac80211-backport/uapi \
	-I$(STAGING_DIR)/usr/include/mac80211-backport \
	-I$(STAGING_DIR)/usr/include/mac80211/uapi \
	-I$(STAGING_DIR)/usr/include/mac80211 \
	-include generated/autoconf.h \
	-include backport/backport.h

MAKE_OPTS:= \
	$(KERNEL_MAKE_FLAGS) \
	SUBDIRS="$(PKG_BUILD_DIR)" \
	USER_EXTRA_CFLAGS="$(EXTRA_CFLAGS)" \
	$(EXTRA_KCONFIG) \
	PROJECT_DIR="$(PKG_BUILD_DIR)" \
	NOSTDINC_FLAGS="$(NOSTDINC_FLAGS)"

define Build/Compile
	+$(MAKE) $(PKG_JOBS) -C "$(LINUX_DIR)" \
		$(MAKE_OPTS) \
		modules
endef

$(eval $(call KernelPackage,rtl8723du))
$(eval $(call BuildPackage,rtl8723du-btcoexist-firmware))
