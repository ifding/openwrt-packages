
# librdkafka for OpenWrt

host machine: Ubuntu 14.04
target machine: mipsel
librdkafka version: v0.9.5


## 1. install librdkafka library

1. Add `src-git librdkafka https://github.com/ifding/openwrt-librdkafka.git` to your `feeds.conf`

2. Update the feed: `./scripts/feeds update librdkafka`

3. And then install it: `./scripts/feeds install librdkafa`

4. Now your package should be aviable when you do: `make menuconfig`

5. Because the librdkafka including sc-cpp and examples, it is not easy to cross-compile them together, it will be buggy. So it'd just compile the librdkafka. To do this, you should remove the `src-cpp` and `examples` from the `Makefile`, I also comments the `tests`. It is like this:

```
$ vi ~/Downloads/openwrt/build_dir/target-mipsel_24kec+dsp_musl-1.1.16/librdkafka-0.9.5/Makefile
LIBSUBDIRS=	src

CHECK_FILES+=	CONFIGURATION.md

PACKAGE_NAME?=	librdkafka
VERSION?=	$(shell python packaging/get_version.py src/rdkafka.h)

# Jenkins CI integration
BUILD_NUMBER ?= 1

.PHONY:

all: mklove-check libs CONFIGURATION.md check

include mklove/Makefile.base

libs:
	@(for d in $(LIBSUBDIRS); do $(MAKE) -C $$d || exit $?; done)

CONFIGURATION.md: src/rdkafka.h
	@printf "$(MKL_YELLOW)Updating$(MKL_CLR_RESET)\n"
	@echo '//@file' > CONFIGURATION.md.tmp
	@(cmp CONFIGURATION.md CONFIGURATION.md.tmp || \
		mv CONFIGURATION.md.tmp CONFIGURATION.md; \
		rm -f CONFIGURATION.md.tmp)

file-check: CONFIGURATION.md LICENSES.txt
check: file-check
	@(for d in $(LIBSUBDIRS); do $(MAKE) -C $$d $@ || exit $?; done)

install:
	@(for d in $(LIBSUBDIRS); do $(MAKE) -C $$d $@ || exit $?; done)

#tests: .PHONY libs
#	$(MAKE) -C $@

docs:
	doxygen Doxyfile
	@echo "Documentation generated in staging-docs"

clean-docs:
	rm -rf staging-docs

clean:
#	@$(MAKE) -C tests $@
	@(for d in $(LIBSUBDIRS); do $(MAKE) -C $$d $@ ; done)

distclean: clean
	./configure --clean
	rm -f config.log config.log.old

archive:
	git archive --prefix=$(PACKAGE_NAME)-$(VERSION)/ \
		-o $(PACKAGE_NAME)-$(VERSION).tar.gz HEAD
	git archive --prefix=$(PACKAGE_NAME)-$(VERSION)/ \
		-o $(PACKAGE_NAME)-$(VERSION).zip HEAD

rpm: distclean
	$(MAKE) -C packaging/rpm

LICENSES.txt: .PHONY
	@(for i in LICENSE LICENSE.* ; do (echo "$$i" ; echo "--------------------------------------------------------------" ; cat $$i ; echo "" ; echo "") ; done) > $@.tmp
	@cmp $@ $@.tmp || mv $@.tmp $@ ; rm -f $@.tmp
```

6. `make V=s`
  
## 2. install librdkafka example

Let's say the name of app is: kafkaClient

### 1. Create Makefile

```
$ mkdir package/kafkaClient
$ cd package/kafkaClient
$ vi Makefile
```
Note: [package/kafkaClient/Makefile](kafkaClient/Makefile)

### 2. Create src dir

```
$ mkdir src
$ vi Makefile
```

Note: [package/kafkaClient/src/Makefile](kafkaClient/src/Makefile)

Next, copy examples/rdkafka_example.c and libkafka.h to src

```
$ cp build_dir/target-mipsel_24kec+dsp_musl-1.1.16/librdkafka-0.9.5/examples/rdkafka_example.c package/kafkaClient/src
$ cp build_dir/target-mipsel_24kec+dsp_musl-1.1.16/librdkafka-0.9.5/src/rdkafka.h package/kafkaClient/src
```

### 3. Compile

```
$ make V=s
```

## 3. Reference

* [http://blog.sina.com.cn/s/blog_636a55070102wa1d.html](http://blog.sina.com.cn/s/blog_636a55070102wa1d.html)

* [http://blog.sina.com.cn/s/blog_636a55070102waby.html](http://blog.sina.com.cn/s/blog_636a55070102waby.html)
