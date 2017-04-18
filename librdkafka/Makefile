#
# Copyright (C) 2011 OpenWrt.org
#
# This is free software, licensed under the GNU General Public License v2.
# See /LICENSE for more information.
#
include $(TOPDIR)/rules.mk

PKG_NAME:=librdkafka
PKG_VERSION:=0.8

PKG_SOURCE:=$(PKG_VERSION).zip
PKG_SOURCE_URL:=https://github.com/edenhill/librdkafka/archive
PKG_SOURCE_VERSION:=0.8.6
PKG_SOURCE_SUBDIR:=$(PKG_NAME)-$(PKG_VERSION)
PKG_INSTALL:=1
PKG_FIXUP:=autoreconf

include $(INCLUDE_DIR)/package.mk

define Package/librdkafka
    TITLE:=librdkafka
    SECTION:=libs
    DEPENDS:=+libpthread +zlib +librt 
    CATEGORY:=Libraries
    URL:=https://github.com/edenhill/librdkafka
endef

define Package/librdkafka/description
	Apache Kafka C library
endef

TARGET_CONFIGURE_OPTS = \
  AR=$(TARGET_CROSS)ar \
  AS="$(TARGET_CC) -c $(TARGET_CFLAGS)" \
  LD=$(TARGET_CROSS)gcc \
  NM=$(TARGET_CROSS)nm \
  CC="$(TARGET_CC)" \
  GCC="$(TARGET_CC)" \
  CXX="$(TARGET_CXX)" \
  RANLIB=$(TARGET_CROSS)ranlib \
  STRIP=$(TARGET_CROSS)strip \
  OBJCOPY=$(TARGET_CROSS)objcopy \
  OBJDUMP=$(TARGET_CROSS)objdump \
  SIZE=$(TARGET_CROSS)size \
  PREFIX=$(PKG_INSTALL_DIR)/usr

define Build/Configure
        (cd $(PKG_BUILD_DIR); ./configure);
endef

define Package/librdkafka/prepare
	mkdir -p $(PKG_BUILD_DIR)
	$(INSTALL_DIR) $(PKG_BUILD_DIR)/usr/include
	$(INSTALL_DIR) $(PKG_BUILD_DIR)/usr/lib
endef

define Package/librdkafka/install
	$(INSTALL_DIR) $(1)/usr/lib
	$(CP) $(PKG_INSTALL_DIR)/usr/lib/*.so* $(1)/usr/lib/
endef

$(eval $(call BuildPackage,librdkafka))
