#
# Copyright (C) 2007-2016 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=apr-util
PKG_VERSION:=1.6.1
PKG_RELEASE:=1

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.bz2
PKG_SOURCE_URL:=@APACHE/apr/
PKG_HASH:=d3e12f7b6ad12687572a3a39475545a072608f4ba03a6ce8a3778f607dd0035b
PKG_MAINTAINER:=Thomas Heil <heil@terminal-consulting.de>
PKG_LICENSE:=Apache License

PKG_FIXUP:=autoreconf
PKG_REMOVE_FILES:=aclocal.m4 build/ltmain.sh

PKG_INSTALL:=1

include $(INCLUDE_DIR)/package.mk
include $(INCLUDE_DIR)/nls.mk

define Package/libaprutil
  SECTION:=libs
  CATEGORY:=Libraries
  DEPENDS:=+libapr +libexpat +libsqlite3 +libuuid $(ICONV_DEPENDS)
  TITLE:=Apache Portable Runtime Utility Library
  URL:=http://apr.apache.org/
endef

TARGET_CFLAGS += $(FPIC)
TARGET_CPPFLAGS += -D_LARGEFILE64_SOURCE -D_FILE_OFFSET_BITS=64 -D_GNU_SOURCE

CONFIGURE_ARGS += \
	--with-pgsql=no \
	--with-apr="$(STAGING_DIR)/opt/bin/apr-1-config" \
	--with-expat="$(STAGING_DIR)/opt" \
	--without-sqlite2 \
	--with-sqlite3="$(STAGING_DIR)/opt"

ifdef $(CONFIG_BUILD_NLS)
	CONFIGURE_ARGS += --with-iconv="$(ICONV_PREFIX)"
else
	CONFIGURE_ARGS += --without-iconv
endif

CONFIGURE_VARS += \
	ac_cv_file_dbd_apr_dbd_mysql_c=no \
	ac_cv_path_ODBC_CONFIG= \
	APR_BUILD_DIR="$(STAGING_DIR)/opt/share/build-1"

MAKE_FLAGS += \
	APRUTIL_LIBS="-lsqlite3 $(TARGET_LDFLAGS) -lexpat $(if $(ICONV_FULL),-liconv) -lapr-1 -luuid -lm -lcrypt"

define Build/InstallDev
	$(INSTALL_DIR) $(1)/opt/bin $(1)/opt/include/apr-1/ $(1)/opt/lib  $(1)/opt/lib/pkgconfig/
	$(CP)   $(PKG_INSTALL_DIR)/opt/bin/apu-1-config \
	 	$(1)/opt/bin/
	$(CP)   $(PKG_INSTALL_DIR)/opt/include/apr-1/* \
		$(1)/opt/include/apr-1/
	$(CP)   $(PKG_INSTALL_DIR)/opt/lib/libaprutil-1.{la,a,so*} \
		$(1)/opt/lib/
	$(CP)   $(PKG_INSTALL_DIR)/opt/lib/pkgconfig/apr-util-1.pc \
		$(1)/opt/lib/pkgconfig/
	$(SED) 's, -e \"s -I$$$$prefix/lib  g\",,g' $(1)/opt/bin/apu-1-config
	$(SED) 's,^libdir=\"$$$${exec_prefix}/lib,libdir=\"$(STAGING_DIR)/opt/lib,g' $(1)/opt/bin/apu-1-config
	$(SED) 's,^includedir=\"$$$${prefix}/include/,includedir=\"$(STAGING_DIR)/opt/include/,g' $(1)/opt/bin/apu-1-config
	$(SED) 's,-L$$$$libdir,,g' $(1)/opt/bin/apu-1-config
	$(SED) 's,-R$$$$libdir,,g' $(1)/opt/bin/apu-1-config
endef

define Package/libaprutil/install
	$(INSTALL_DIR) $(1)/opt/lib
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/libaprutil-1.so.* $(1)/opt/lib/
endef

$(eval $(call BuildPackage,libaprutil))
