#
# Copyright (C) 2008-2016 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=netdata
PKG_VERSION:=1.12.1
PKG_RELEASE:=1a
PKG_MAINTAINER:=
PKG_LICENSE:=GPL-3.0+
PKG_LICENSE_FILES:=COPYING

PKG_SOURCE:=$(PKG_NAME)-v$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://github.com/netdata/netdata/releases/download/v$(PKG_VERSION)
PKG_HASH:=ce4516af03a6dc17a1219e939eb6b4c14ec44fd73d186797c9390260c4cfe571
PKG_BUILD_DIR:=$(BUILD_DIR)/$(PKG_NAME)-v$(PKG_VERSION)

PKG_INSTALL:=1
PKG_FIXUP:=autoreconf
PKG_USE_MIPS16:=0

include $(INCLUDE_DIR)/package.mk

define Package/netdata
  SECTION:=admin
  CATEGORY:=Administration
  DEPENDS:=+zlib +libuuid +libmnl
  TITLE:=Real-time performance monitoring tool
  URL:=https://my-netdata.io/
endef

define Package/netdata/description
  netdata is a highly optimized Linux daemon providing real-time performance
  monitoring for Linux systems, applications and SNMP devices over the web.

 If you want to use Python plugins install python3, python3-yaml and
 python3-urllib3
endef

TARGET_CFLAGS := $(filter-out -O%,$(TARGET_CFLAGS))
TARGET_CFLAGS += -ffunction-sections -fdata-sections -O3
TARGET_LDFLAGS += -Wl,--gc-sections

# Entware specific: help Netdata find the correct headers
# (... fatal error: uuid/uuid.h: No such file or directory ...)
TARGET_CFLAGS += -I$(STAGING_DIR)/opt/include

CONFIGURE_ARGS += \
	--with-zlib \
	--with-math \
	--disable-x86-sse \
	--enable-lto \
	--without-libcap \
	--disable-plugin-nfacct \
	--disable-plugin-cups

define Package/netdata/conffiles
/opt/etc/netdata/
endef

define Package/netdata/install
	$(INSTALL_DIR) $(1)/opt/etc/netdata/custom-plugins.d
	$(CP) $(PKG_INSTALL_DIR)/opt/etc/netdata $(1)/opt/etc
	$(CP) ./files/netdata.conf $(1)/opt/etc/netdata
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/netdata $(1)/opt/lib
	$(CP) $(1)/opt/lib/netdata/conf.d/fping.conf $(1)/opt/etc
	$(CP) $(1)/opt/lib/netdata/conf.d/health_alarm_notify.conf $(1)/opt/etc
	rm -rf $(1)/opt/lib/netdata/python.d/python_modules/pyyaml2
	rm -rf $(1)/opt/lib/netdata/python.d/python_modules/pyyaml3
	rm -rf $(1)/opt/lib/netdata/python.d/python_modules/urllib3
	$(CP) $(1)/opt/lib/netdata/plugins.d/tc-qos-helper.sh $(1)/opt/etc
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/netdata $(1)/opt/sbin
	$(INSTALL_DIR) $(1)/opt/share/netdata
	$(CP) $(PKG_INSTALL_DIR)/opt/share/netdata $(1)/opt/share
	rm $(1)/opt/share/netdata/web/demo*html
	rm $(1)/opt/share/netdata/web/fonts/*.svg
	rm $(1)/opt/share/netdata/web/fonts/*.ttf
	rm $(1)/opt/share/netdata/web/fonts/*.woff
	rm $(1)/opt/share/netdata/web/images/*.png
	rm $(1)/opt/share/netdata/web/images/*.gif
	rm $(1)/opt/share/netdata/web/images/*.ico
	$(INSTALL_DIR) $(1)/opt/etc/init.d
	$(INSTALL_BIN) ./files/S60netdata $(1)/opt/etc/init.d
endef

$(eval $(call BuildPackage,netdata))
