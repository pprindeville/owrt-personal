#
# Copyright (C) 2018 Philip Prindeville
#
# This is free software, licensed under the GNU General Public License v3 or
# later.
#

include $(TOPDIR)/rules.mk

PKG_NAME:=customize
PKG_VERSION:=1.0
PKG_RELEASE:=1
PKG_LICENSE:=GPLv3

PKG_MAINTAINER:=Philip Prindeville <philipp@redfish-solutions.com>

include $(INCLUDE_DIR)/package.mk

define Package/customize/Default
  SECTION:=admin
  CATEGORY:=Administration
endef

define Package/customize/description
  Customize your target system.
endef

define Package/customize/download
endef

define Build/Prepare
endef

define Build/Configure
endef

define Build/Compile
endef

define Package/root-passwd
$(call Package/customize/Default)
  TITLE:=Set default root password
endef

define Package/root-passwd/description
  Use this package to seed your images with a preset root password.
endef

define Package/root-passwd/config
	config TARGET_ROOT_PASSWD
		string "Default root password (hashed, with dollars doubled up)"
		help
			Default root password.
endef

define Package/root-passwd/install
   # nothing to do
endef

ROOT_PASSWD:=$(call qstrip,$(CONFIG_TARGET_ROOT_PASSWD))

define Package/root-passwd/postinst
#!/bin/sh
[ -n "$${IPKG_INSTROOT}" ] || exit 0
[ -n '$(ROOT_PASSWD)' ] || exit 0

sed -i -e 's#^root:::0:#root:$(ROOT_PASSWD):::#' $$IPKG_INSTROOT/etc/shadow

exit 0
endef

define Package/disable-openssh-password-login
$(call Package/customize/Default)
  TITLE:=Turn off password logins via openssh
  DEPENDS+= @(PACKAGE_openssh-server||PACKAGE_openssh-server-pam)
endef

define Package/disable-openssh-password-login/description
  Change default openssh server setting to disallow password logins (and
  thereby mitigate possible password attacks)
endef

define Package/disable-openssh-password-login/install
	$(INSTALL_DIR) -m 0700 $(1)/etc/ssh/sshd_config.d
	$(INSTALL_DATA) -m 0600 ./files/20-no-password-auth.conf $(1)/etc/ssh/sshd_config.d/
endef

define Package/crontab-for-logrotate
$(call Package/customize/Default)
  TITLE:=Rotate logs w/ cron
  DEPENDS+= +syslog-ng \
	    +logrotate \
	    @BUSYBOX_CONFIG_CRONTAB @BUSYBOX_CONFIG_CROND
endef

define Package/crontab-for-logrotate/description
  Do regular log rotations via crontab.
endef

define Package/crontab-for-logrotate/install
	$(INSTALL_DIR) $(1)/etc/crontabs
	$(INSTALL_DATA) ./files/logrotate.cron $(1)/etc/crontabs/root
endef

define Package/files-overlay
$(call Package/customize/Default)
  TITLE:=Copyout contents of files/
  DEPENDS:=
ifneq ("$(wildcard $(TOPDIR)/files/etc/sensors3.conf)","")
  DEPENDS+= +lm-sensors
endif
ifneq ("$(wildcard $(TOPDIR)/files/etc/syslog-ng.d/*)","")
  DEPENDS+= +syslog-ng
endif
endef

define Package/files-overlay/description
  Use this package to provide some of the functionality of the old overlay
  support without the complexity.
endef

define Package/files-overlay/config
endef

# a little tedious to maintain
define Package/files-overlay/conffiles
/etc/crontabs/root
endef

define Package/files-overlay/install
   $(CP) $(TOPDIR)/files/. $(1)/
endef

$(eval $(call BuildPackage,root-passwd))
$(eval $(call BuildPackage,disable-openssh-password-login))
$(eval $(call BuildPackage,crontab-for-logrotate))
$(eval $(call BuildPackage,files-overlay))

