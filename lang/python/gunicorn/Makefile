#
# Copyright (C) 2007-2017 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=gunicorn
PKG_VERSION:=19.9.0
PKG_RELEASE=1
PKG_LICENSE:=MIT

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://files.pythonhosted.org/packages/source/g/gunicorn
PKG_HASH:=fa2662097c66f920f53f70621c6c58ca4a3c4d3434205e608e121b5b3b71f4f3

include $(INCLUDE_DIR)/package.mk
include ../python-package.mk

define Package/gunicorn
    SUBMENU:=Python
    SECTION:=lang
    CATEGORY:=Languages
    TITLE:=WSGI HTTP Server for UNIX
    MAINTAINER:=Gergely Kiss <mail.gery@gmail.com>
    URL:=https://gunicorn.org
    DEPENDS:=+python +python-setuptools
endef

define Package/gunicorn/description
    WSGI HTTP Server for UNIX
endef

define Build/Compile
	$(call Build/Compile/PyMod,,install --prefix=/opt --root=$(PKG_INSTALL_DIR))
endef

define Package/gunicorn/install
	$(INSTALL_DIR) $(1)$(PYTHON_PKG_DIR)
	$(INSTALL_DIR) $(1)/opt/bin
	$(CP) \
	    $(PKG_INSTALL_DIR)$(PYTHON_PKG_DIR)/* \
	    $(1)$(PYTHON_PKG_DIR)
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/bin/gunicorn* \
	    $(1)/opt/bin
	# fix python exec path in scripts
	$(SED) 's/#!.*/#!\/opt\/bin\/python/g' $(1)/opt/bin/gunicorn*
endef

$(eval $(call BuildPackage,gunicorn))
