# $Id: Makefile,v 1.6 2011/04/18 17:14:00 lukagarb Exp $
#

PYTHON=`which python2`
DESTDIR=/
BUILDIR=$(CURDIR)/debian/python-nipap-build
PROJECT=nipap

all:
#	$(PYTHON) setup.py build
	@echo "make source - Create source package"
	@echo "make install - Install on local system"
	@echo "make buildrpm - Generate a rpm package"
	@echo "make builddeb - Generate a deb package"
	@echo "make bumpversion - Bump version to latest in ../NEWS file"
	@echo "make clean - Get rid of scratch and byte files"

source:
	$(PYTHON) setup.py sdist

install:
	$(PYTHON) setup.py install

buildrpm:
	$(PYTHON) setup.py bdist_rpm --post-install=rpm/postinstall --pre-uninstall=rpm/preuninstall

builddeb:
	# build the source package in the parent directory
	# then rename it to project_version.orig.tar.gz
	$(PYTHON) setup.py sdist --dist-dir=../ --prune
	rename -f 's/$(PROJECT)-(\d.*)\.tar\.gz/$(PROJECT)_$$1\.orig\.tar\.gz/' ../*
	# build the package
	debuild -us -uc

distclean:
realclean:
test:
check:

upload:
	python setup.py sdist upload

clean:
	$(PYTHON) setup.py clean
	rm -rf build/ MANIFEST dist/ debian/tmp debian/nipap-common \
		debian/$(PROJECT) debian/nipapd debian/files .pc/ nipapd.8* \
		nipap-passwd.1* debian/nipapd.debhelper.log \
		debian/nipapd.postinst.debhelper debian/nipapd.postrm.debhelper \
		debian/nipapd.prerm.debhelper debian/nipapd.substvars \
		debian/nipap-common.debhelper.log \
		debian/nipap-common.postinst.debhelper \
		debian/nipap-common.prerm.debhelper debian/nipap-common.substvars
	find . -name '*.pyc' -delete

VER := $(shell head -n1 ../NEWS | awk '{print $$2}')
DB_VER := $(shell grep "COMMENT ON DATABASE nipap IS" sql/ip_net.plsql | sed 's/.*version: \([0-9]\+\).*/\1/')
bumpversion:
	# update debian package nipapd's config and postinst script with current db
	# version
	sed -i 's/^CURRENT_DB_VERSION.*/CURRENT_DB_VERSION=$(DB_VER)/' debian/nipapd.config debian/nipapd.postinst
	# replace version & db_version number in nipap/__init__.py
	sed -i 's/\(__version__\s*= \)"[^"]\+"/\1"$(VER)"/' nipap/__init__.py
	sed -i "s/\(__db_version__\s*= \)[0-9]\+/\1$(DB_VER)/" nipap/__init__.py
	# update debian/changelog
	../utilities/news2dch.py ../NEWS debian/changelog
