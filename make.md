run all Makefile in subdirectories
---
```
subdirs:
	for d in $(SUBDIRS); do make PYTHON=$(PYTHON) -C $$d; [ $$? = 0 ] || exit 1 ; done
```
