PYVERSION=3.5.2
PYMINOR=$(basename $(PYVERSION))

ROOT=$(abspath ..)

HOSTINSTALL=$(ROOT)/build/$(PYVERSION)/host
HOSTBUILD=$(HOSTINSTALL)/Python-$(PYVERSION)
HOSTPYTHON=$(HOSTINSTALL)/bin/python3$(EXE)
HOSTPGEN=$(HOSTINSTALL)/bin/pgen$(EXE)

BUILD=$(ROOT)/build/$(PYVERSION)/Python-$(PYVERSION)
INSTALL=$(ROOT)/installs/python-$(PYVERSION)
TARBALL=$(ROOT)/downloads/Python-$(PYVERSION).tgz
URL=https://www.python.org/ftp/python/$(PYVERSION)/Python-$(PYVERSION).tgz
LIB=libpython$(PYMINOR).a


all: install


install: $(BUILD)/$(LIB)
	( \
		cd $(BUILD); \
		sed -i -e 's/libinstall:.*/libinstall:/' Makefile; \
		emmake make HOSTPYTHON=$(HOSTPYTHON) PYTHON_FOR_BUILD=$(HOSTPYTHON) CROSS_COMPILE=yes inclinstall libinstall $(LIB) && \
		cp $(LIB) $(INSTALL)/lib/ && \
		cp $(HOSTINSTALL)/lib/python$(PYMINOR)/_sysconfigdata.py $(INSTALL)/lib/python$(PYMINOR)/ \
	)


clean:
	-rm -fr $(HOSTINSTALL)
	-rm -fr $(BUILD)


$(TARBALL):
	[ -d ../downloads ] || mkdir ../downloads
	wget -q -O $@ $(URL)
	md5sum --quiet --check checksums || (rm $@; false)


$(HOSTPYTHON) $(HOSTPGEN): $(TARBALL)
	mkdir -p $(HOSTINSTALL)
	[ -d $(HOSTBUILD) ] || tar -C $(HOSTINSTALL) -xf $(TARBALL)
	( \
		cd $(HOSTBUILD); \
		CFLAGS=-m32 LDFLAGS=-m32 ./configure --prefix=$(HOSTINSTALL) && \
		make install && \
		cp Parser/pgen$(EXE) $(HOSTINSTALL)/bin/ && \
		make distclean \
	)


$(BUILD)/.patched: $(TARBALL)
	[ -d $(BUILD) ] || (mkdir -p $(dir $(BUILD)); tar -C $(dir $(BUILD)) -xf $(TARBALL))
	cat patches/*.patch | (cd $(BUILD) ; patch -p1)
	touch $@


$(BUILD)/Makefile: $(BUILD)/.patched
	cp config.site $(BUILD)/
	( \
		cd $(BUILD); \
		CFLAGS=-m32 LDFLAGS=-m32 CONFIG_SITE=./config.site READELF=true emconfigure ./configure --without-threads --without-pymalloc --disable-shared --disable-ipv6 --without-gcc --host=wasm32-wasi --build=$(shell $(BUILD)/config.guess) --prefix=$(INSTALL) ; \
	)


$(BUILD)/$(LIB): $(BUILD)/Makefile $(HOSTPYTHON) $(HOSTPGEN) Setup.local
	cp Setup.local $(BUILD)/Modules/
	( \
		cd $(BUILD)/Modules/zlib; \
		CFLAGS=-m32 LDFLAGS=-m32 emconfigure ./configure --static \
	)
	( \
		cd $(BUILD); \
		CFLAGS=-Wno-implicit-function-declaration emmake make HOSTPYTHON=$(HOSTPYTHON) HOSTPGEN=$(HOSTPGEN) CROSS_COMPILE=yes $(LIB) \
	)
