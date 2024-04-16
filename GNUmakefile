PKG := zram-disk-and-swap
PREFIX := /usr
LIBEXEC_PREFIX := $(PREFIX)/libexec
LIBEXEC := $(LIBEXEC_PREFIX)/$(PKG)
ETC := /etc
# ^ WARN: if changed, must patch service files
INITD := $(ETC)/init.d
# ^ change to /usr/sbin to avoid startup

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
$(RM) -r '$(LIBEXEC)/' '$(ETC)/default/$(PKG)' '$(INITD)/$(PKG)-disk' '$(INITD)/$(PKG)-swap'
endef
export UNINST_BODY

all:
	@echo 'Run "make install" (or tweak config.mk first)'
.PHONY: all

install:
	install -D -m 755 -t '$(DESTDIR)$(LIBEXEC)/alloc-zram-dev' lib/alloc-zram-dev
	install -D -m 755 -t '$(DESTDIR)$(INITD)' etc/init.d/*
	install -D -m 644 etc_default.cfg '$(DESTDIR)$(ETC)/default/$(PKG)'
	echo 'ALLOC_ZDEV_PATH=$(LIBEXEC)/alloc-zram-dev' >>'$(DESTDIR)$(ETC)/default/$(PKG)'
	printf "$$UNINST_BODY" >'$(DESTDIR)$(LIBEXEC)/uninstall'
	chmod +x '$(DESTDIR)$(LIBEXEC)/uninstall'
.PHONY: install

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
