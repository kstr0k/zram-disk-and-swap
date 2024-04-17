PKG := zram-disk-and-swap
PREFIX := /usr
SBIN := $(PREFIX)/sbin
LIBEXEC_PREFIX := $(PREFIX)/libexec
LIBEXEC := $(LIBEXEC_PREFIX)/$(PKG)
ETC := /etc
# ^ WARN: if changed, must patch service files
INITD := $(ETC)/init.d
# ^ for sysvinit

FSROOT_ARCHIVE := $(abspath $(PKG)-FSROOT.tgz)
DISTDIR := $(abspath distdir)

ifeq ($(file <config.mk),)
$(warning not configured -- using defaults; run ' ./configure --help ')
else
include config.mk
endif

define UNINST_BODY
#!/bin/sh
set -ue
$(RM) '$(ETC)/default/$(PKG)' '$(SBIN)/$(PKG)-disk' '$(SBIN)/$(PKG)-swap'
$(RM) '$(INITD)/$(PKG)-disk' '$(INITD)/$(PKG)-swap'
$(RM) -r '$(LIBEXEC)/'
endef
export UNINST_BODY

all:
	@echo 'Run "make install" (or tweak config.mk first)'
.PHONY: all

install:
	install -D -m 755 -t '$(DESTDIR)$(LIBEXEC)'/ lib/alloc-zram-dev
	install -D -m 755 -t '$(DESTDIR)$(SBIN)'/ etc/init.d/*
	install -D -m 644 etc_default.cfg '$(DESTDIR)$(ETC)/default/$(PKG)'
	echo 'ALLOC_ZDEV_PATH=$(LIBEXEC)/alloc-zram-dev' >>'$(DESTDIR)$(ETC)/default/$(PKG)'
	printf "$$UNINST_BODY" >'$(DESTDIR)$(LIBEXEC)/uninstall'
	chmod +x '$(DESTDIR)$(LIBEXEC)/uninstall'
install-sysv: install
	ln -sf '$(DESTDIR)$(SBIN)/$(PKG)-disk' '$(DESTDIR)$(SBIN)/$(PKG)-swap' $(INITD)/
	! command -v update-rc.d || for s in disk swap; do update-rc.d "$(PKG)-$$s" defaults; done
.PHONY: install install-sysv

$(DISTDIR): DESTDIR := $(DISTDIR)
$(DISTDIR): clean-dist install
	touch '$(DISTDIR)'
$(FSROOT_ARCHIVE): $(DISTDIR)
	(cd '$(DISTDIR)' && tar -cvf '$@' .)
dist: $(FSROOT_ARCHIVE)
.PHONY: dist

clean-dist:
	$(RM) -r '$(DISTDIR)' '$(FSROOT_ARCHIVE)' uninstall
clean: clean-dist
distclean: clean
	$(RM) config.mk
.PHONY: clean clean-distdir distclean
