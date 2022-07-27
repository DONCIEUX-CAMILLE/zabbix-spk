PKG_NAME = zabbix
PKG_VERS = 6.2.1
PKG_EXT = tar.gz
PKG_DIST_NAME = $(PKG_NAME)-$(PKG_VERS).$(PKG_EXT)
PKG_DIST_SITE = http://sourceforge.net/projects/zabbix/files/ZABBIX%20Latest%20Stable/$(PKG_VERS)
PKG_DIR = $(PKG_NAME)-$(PKG_VERS)
DEPENDS = cross/zlib cross/net-snmp cross/libssh2 cross/curl cross/openssl cross/libxml2 cross/fping cross/libevent cross/pcre cross/mysql5 cross/busybox native/go
# Change with DSM7.0. migrate from mysql5 to libmysqlclient
#DEPENDS = cross/zlib cross/net-snmp cross/libssh2 cross/curl cross/openssl cross/libxml2 cross/fping cross/libevent cross/pcre cross/libmysqlclient cross/busybox native/go


HOMEPAGE = http://www.zabbix.com
COMMENT  = The Enterprise-class Monitoring Solution for Everyone 
LICENSE  = 

PRE_CONFIGURE_TARGET = myPreConfigure
POST_INSTALL_TARGET = myPostInstall

GNU_CONFIGURE = 1
#CONFIGURE_ARGS = --enable-server --enable-proxy --enable-agent --with-mysql=$(WORK_DIR)/mysql-5.6.38/scripts/mysql_config --enable-ipv6 --with-net-snmp=$(STAGING_INSTALL_PREFIX)/bin/net-snmp-config --with-ssh2=$(STAGING_INSTALL_PREFIX) --with-libcurl=$(STAGING_INSTALL_PREFIX)/bin/curl-config --with-libxml2=$(STAGING_INSTALL_PREFIX)/bin/xml2-config --with-openssl=$(STAGING_INSTALL_PREFIX)
CONFIGURE_ARGS = --enable-server --enable-proxy --enable-agent --with-mysql=$(STAGING_INSTALL_PREFIX)/bin/mysql_config --enable-ipv6 --with-net-snmp=$(STAGING_INSTALL_PREFIX)/bin/net-snmp-config --with-ssh2=$(STAGING_INSTALL_PREFIX) --with-libcurl=$(STAGING_INSTALL_PREFIX)/bin/curl-config --with-libxml2=$(STAGING_INSTALL_PREFIX)/bin/xml2-config --with-openssl=$(STAGING_INSTALL_PREFIX)
#CONFIGURE_ARGS = --enable-agent2 

#ADDITIONAL_LDFLAGS need for configure
#ADDITIONAL_LDFLAGS = -L$(WORK_DIR)/mysql-5.6.38/libmysql -mno-unaligned-access
#ADDITIONAL_LDFLAGS = -L$(WORK_DIR)/mysql-5.6.38/libmysql -L/usr/include
ADDITIONAL_LDFLAGS = -L/usr/include
# error adding symbols: DSO missing from command line
# change LIBS in MakeFile inside /spksrc/spksrc/spk/zabbix/work-armada38x-5.2/zabbix-3.4.3/src/zabbix_server for example
ADDITIONAL_LIBS="-lpthread"

# use workdir as gopath
ENV += GOROOT_BOOTSTRAP=$(WORK_DIR)/../../../native/go-1.4/work-native/go
ENV += GOPATH=$(WORK_DIR)
ENV += USE_GO_TOOL=true
ENV += CGO_ENABLED=1
#$(CGO_ENABLED)
ENV += PATH=$(WORK_DIR)/../../../native/go/work-native/go/bin/:$$PATH
ENV += GOOS=linux
ENV += GOARCH=arm
ENV += CC_FOR_linux_arm=$(CC)

include ../../mk/spksrc.cross-cc.mk

.PHONY: myPreConfigure
myPreConfigure:
	cd $(WORK_DIR)/$(PKG_DIR)/src/libs/zbxsysinfo/;ln -fs linux $(ARCH);cd -
	# in case of recursive link
	rm -f $(WORK_DIR)/$(PKG_DIR)/src/libs/zbxsysinfo/linux/linux

	#mkdir -p $(WORK_DIR)/install/usr/local/lib && cd  $(WORK_DIR)/install/usr/local/lib && cp $(WORK_DIR)/mysql-5.6.38/libmysql/libmysqlclient.a .
	#mkdir -p $(WORK_DIR)/install/usr/local/lib && cd  $(WORK_DIR)/install/usr/local/lib && cp $(WORK_DIR)/mysql-5.6.38/libmysql/libmysqlclient.a .
	#mkdir -p /usr/local/mysql && cd /usr/local/mysql && ln -fs $(WORK_DIR)/mysql-5.6.38/include include
	# pb empty libs for mysql_config and connector 6.1
	#sed -i -e "s|libs=\"\$libs -l \"|libs=\"\$libs -lmysqlclient -lpthread -lm -lssl -lcrypto -ldl\"|g" $(WORK_DIR)/install/usr/local/zabbix/bin/mysql_config
	sed -i '/$as_echo_n "checking for long long format... " >&6; }/{n; s/yes/maybe/}' $(WORK_DIR)/$(PKG_DIR)/configure
	sed -i '/CFLAGS="-m64"/{n; s/yes/maybe/}' $(WORK_DIR)/$(PKG_DIR)/configure
	# ADD GO env variables
	env $(ENV)
	sed -i "0,/GOCMD/s/GOCMD/GOCMD\nGOARCH\nGOARM\nUSE_GO_TOOL\nGOOS\nCGO_ENABLED/1" $(WORK_DIR)/$(PKG_DIR)/configure
	sed -i "s/GOCMD=go/GOCMD=go\nexport GOARCH=arm\nexport GOARM=5\nexport USE_GO_TOOL=true\nexport GOOS=linux\nexport CGO_ENABLED=1\n/g" $(WORK_DIR)/$(PKG_DIR)/configure
	for file in "Makefile.in" "go/Makefile.in" "go/src/Makefile.in" "go/src/zabbix/Makefile.in" "go/src/zabbix/cmd/Makefile.in" "go/src/zabbix/cmd/zabbix_agent2/Makefile.in"; do \
		if [ -f $(WORK_DIR)/$(PKG_DIR)/$$file ] ; then cat $(WORK_DIR)/$(PKG_DIR)/$$file | awk '{if (index($$0,"GOCMD = @GOCMD@") > 0) {print $$0; print "GOARCH = @GOARCH@"; print "CGO_ENABLED = @CGO_ENABLED@"; print "GOARM = @GOARM@"; print "USE_GO_TOOL = @USE_GO_TOOL@"} else {print $$0}}' > $(WORK_DIR)/$(PKG_DIR)/$$file.new && mv $(WORK_DIR)/$(PKG_DIR)/$$file.new $(WORK_DIR)/$(PKG_DIR)/$$file; fi; \
	done; \


.PHONY: myPostInstall
myPostInstall:
	mkdir -p $(STAGING_INSTALL_PREFIX)/share/zabbix
	mkdir -p $(STAGING_INSTALL_PREFIX)/share/database
	#tar -cf - -C $(WORK_DIR)/$(PKG_DIR)/frontends/php . | tar -xf - -C $(STAGING_INSTALL_PREFIX)/share/zabbix
	# frontend moved to ui directory
	tar -cf - -C $(WORK_DIR)/$(PKG_DIR)/ui . | tar -xf - -C $(STAGING_INSTALL_PREFIX)/share/zabbix
	tar -cf - -C $(WORK_DIR)/$(PKG_DIR)/database/mysql . | tar -xf - -C $(STAGING_INSTALL_PREFIX)/share/database

