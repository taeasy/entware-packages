#
# Copyright (C) 2006-2015 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
# conffile + all files from /opt/lib/sqid added

include $(TOPDIR)/rules.mk

PKG_NAME:=squid
PKG_VERSION:=4.4
PKG_RELEASE:=1

PKG_LICENSE:=GPL-2.0
PKG_MAINTAINER:=Marko Ratkaj <marko.ratkaj@sartura.hr>

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.xz
PKG_SOURCE_URL:=http://www3.us.squid-cache.org/Versions/v4/ \
	http://www2.pl.squid-cache.org/Versions/v4/ \
	http://www.squid-cache.org/Versions/v4/
PKG_HASH:=4905e6da7f5574d2583ba36f398bb062a12d51e70d67035078b6e85b09e9ee82

PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1
PKG_FIXUP:=autoreconf

include $(INCLUDE_DIR)/package.mk

define Package/squid/Default
  SECTION:=net
  CATEGORY:=Network
  SUBMENU:=Web Servers/Proxies
  URL:=http://www.squid-cache.org/
endef

define Package/squid
  $(call Package/squid/Default)
  MENU:=1
  DEPENDS:=+libpthread +librt +libltdl +libstdcpp +libatomic
  DEPENDS+= +SQUID_use-gnutls:libgnutls +SQUID_use-openssl:libopenssl
  DEPENDS+= +SQUID_with-libcap:libcap
  DEPENDS+= +SQUID_with-nettle:libnettle
  DEPENDS+= +SQUID_with-expat:libexpat
  DEPENDS+= +SQUID_with-libxml2:libxml2
  USERID:=squid=137:squid=137
  TITLE:=full-featured Web proxy cache
endef

define Package/squid/description
  Squid is a caching proxy for the Web supporting HTTP, HTTPS, FTP, and more.
  It reduces bandwidth and improves response times by caching and reusing
  frequently-requested web pages.
endef

define Package/squid/config
  source "$(SOURCE)/Config.in"
endef

define Package/squid/conffiles
  /opt/etc/squid/squid.conf
endef

define Package/squid-mod-cachemgr
  $(call Package/squid/Default)
  DEPENDS:=squid
  TITLE:=Web based proxy manager and reporting tool
endef

ifeq ($(CONFIG_GCC_USE_VERSION_7_3_0),y)
TARGET_CFLAGS += -Werror=format-truncation=0
endif

CONFIGURE_ARGS += \
	BUILDCXX=$(HOSTCXX) \
	BUILDCXXFLAGS=$(if $(HOST_CXXFLAGS),$(HOST_CXXFLAGS),-O2) \
	--config-cache \
	--datadir=/opt/share/squid \
	--libexecdir=/opt/lib/squid \
	--sysconfdir=/opt/etc/squid \
	--enable-shared \
	--disable-static \
	--enable-delay-pools \
	--enable-kill-parent-hack \
	--enable-ssl \
	--enable-cache-digests \
	--enable-linux-netfilter \
	--disable-unlinkd \
	--enable-x-accelerator-vary \
	--disable-translation \
	--disable-auto-locale \
	--with-dl \
	--with-pthreads \
	--enable-epoll \
	--with-maxfd=2048 \
	--disable-ecap \
	--disable-external-acl-helpers \
	--disable-arch-native \
	--with-krb5-config=no \
	--without-mit-krb5 \
	--without-netfilter-conntrack \
	--disable-ident-lookups \
	$(if $(CONFIG_SQUID_auth-basic),--enable,--disable)-auth-basic \
	$(if $(CONFIG_SQUID_auth-digest),--enable,--disable)-auth-digest \
	$(if $(CONFIG_SQUID_auth-ntlm),--enable,--disable)-auth-ntlm \
	$(if $(CONFIG_SQUID_auth-negotiate),--enable,--disable)-auth-negotiate \
	$(if $(CONFIG_SQUID_enable-ipv6),--enable,--disable)-dlmalloc \
	$(if $(CONFIG_SQUID_enable-ipv6),--enable,--disable)-ipv6 \
	$(if $(CONFIG_SQUID_enable-ssl-crtd),--enable-ssl-crtd) \
	$(if $(CONFIG_SQUID_use-gnutls),--with,--without)-gnutls \
	$(if $(CONFIG_SQUID_use-gnutls),--without-openssl) \
	$(if $(CONFIG_SQUID_use-gnutls),,--with-openssl="$(STAGING_DIR)/opt") \
	$(if $(CONFIG_SQUID_enable-icmp),--enable,--disable)-icmp \
	$(if $(CONFIG_SQUID_enable-icap-client),--enable,--disable)-icap-client \
	$(if $(CONFIG_SQUID_enable-snmp),--enable,--disable)-snmp \
	$(if $(CONFIG_SQUID_with-libcap),--with,--without)-libcap \
	$(if $(CONFIG_SQUID_with-nettle),--with,--without)-nettle \
	$(if $(CONFIG_SQUID_with-expat),--with,--without)-expat \
	$(if $(CONFIG_SQUID_with-libxml2),--with,--without)-libxml2

CONFIGURE_VARS += \
	ac_cv_header_linux_netfilter_ipv4_h=yes \
	ac_cv_epoll_works=yes

define Build/Compile
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR)/lib all
	+$(MAKE) $(PKG_JOBS) -C $(PKG_BUILD_DIR) \
		DESTDIR="$(PKG_INSTALL_DIR)" \
		install
endef

define Package/squid/install
	$(INSTALL_DIR) $(1)/opt/sbin
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/sbin/squid $(1)/opt/sbin/

	$(INSTALL_DIR) $(1)/opt/lib/squid
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/opt/lib/squid/* $(1)/opt/lib/squid/

#	$(INSTALL_DIR) $(1)/etc/config
#	$(INSTALL_CONF) ./files/squid.config $(1)/etc/config/squid

	$(INSTALL_DIR) $(1)/opt/etc/squid
	$(CP) $(PKG_INSTALL_DIR)/opt/etc/squid/* $(1)/opt/etc/squid/
	$(INSTALL_CONF) ./files/squid.conf $(1)/opt/etc/squid/

	$(INSTALL_DIR) $(1)/opt/etc/init.d/
	$(INSTALL_BIN) ./files/S22squid $(1)/opt/etc/init.d/

	$(INSTALL_DIR) $(1)/opt/share/squid/icons/
	$(CP) $(PKG_INSTALL_DIR)/opt/share/squid/icons/* $(1)/opt/share/squid/icons/
	$(CP) $(PKG_INSTALL_DIR)/opt/share/squid/mib.txt $(1)/opt/share/squid/

	$(INSTALL_DIR) $(1)/opt/share/squid/errors/templates/
	$(CP) $(PKG_INSTALL_DIR)/opt/share/squid/errors/templates/* $(1)/opt/share/squid/errors/templates/

	# fix perl exec path in scripts
	$(SED) '1c\#!/opt/bin/perl' $(1)/opt/lib/squid/helper-mux
	$(SED) '1c\#!/opt/bin/perl' $(1)/opt/lib/squid/log_db_daemon
	$(SED) '1c\#!/opt/bin/perl' $(1)/opt/lib/squid/security_fake_certverify
	$(SED) '1c\#!/opt/bin/perl' $(1)/opt/lib/squid/storeid_file_rewrite
	$(SED) '1c\#!/opt/bin/perl' $(1)/opt/lib/squid/url_lfs_rewrite
endef

define Package/squid-mod-cachemgr/install
	$(INSTALL_DIR) $(1)/opt/share/www/cgi-bin/
	$(CP) $(PKG_INSTALL_DIR)/opt/lib/squid/cachemgr.cgi $(1)/opt/share/www/cgi-bin/
endef

$(eval $(call BuildPackage,squid))
$(eval $(call BuildPackage,squid-mod-cachemgr))
