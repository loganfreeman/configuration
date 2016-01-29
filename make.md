run all Makefile in subdirectories
---
```
subdirs:
	for d in $(SUBDIRS); do make PYTHON=$(PYTHON) -C $$d; [ $$? = 0 ] || exit 1 ; done

install:
	for d in $(SUBDIRS); do \
		make PYTHON=$(PYTHON) DESTDIR=`cd $(DESTDIR); pwd` UNITDIR=$(UNITDIR) INIT=$(INIT) -C $$d install; [ $$? = 0 ] || exit 1;\
	done
```
[The magic behind configure, make, make install](https://robots.thoughtbot.com/the-magic-behind-configure-make-make-install)
---
