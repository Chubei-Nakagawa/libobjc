#
#   GNUmakefile
#
#   Copyright (C) 1998 MDlink online service center, Helge Hess
#   All rights reserved.
#
#   Author: Helge Hess (helge@mdlink.de)
#
#   This file is part of the MDlink Object Framework 2 (MOF2)
#
#   Permission to use, copy, modify, and distribute this software and its
#   documentation for any purpose and without fee is hereby granted, provided
#   that the above copyright notice appear in all copies and that both that
#   copyright notice and this permission notice appear in supporting
#   documentation.
#
#   We disclaim all warranties with regard to this software, including all
#   implied warranties of merchantability and fitness, in no event shall
#   we be liable for any special, indirect or consequential damages or any
#   damages whatsoever resulting from loss of use, data or profits, whether in
#   an action of contract, negligence or other tortious action, arising out of
#   or in connection with the use or performance of this software.
#
# $Id$

ifeq ($(GNUSTEP_MAKEFILES),)
 GNUSTEP_MAKEFILES := $(shell gnustep-config --variable=GNUSTEP_MAKEFILES 2>/dev/null)
  ifeq ($(GNUSTEP_MAKEFILES),)
    $(warning )
    $(warning Unable to obtain GNUSTEP_MAKEFILES setting from gnustep-config!)
    $(warning Perhaps gnustep-make is not properly installed,)
    $(warning so gnustep-config is not in your PATH.)
    $(warning )
    $(warning Your PATH is currently $(PATH))
    $(warning )
  endif
endif

ifeq ($(GNUSTEP_MAKEFILES),)
  $(error You need to set GNUSTEP_MAKEFILES before compiling!)
endif

PACKAGE_NAME = gnustep-objc

include $(GNUSTEP_MAKEFILES)/common.make

VERSION=1.7.2
SVN_MODULE_NAME = libobjc
SVN_BASE_URL = svn+ssh://svn.gna.org/svn/gnustep/libs
SVN_TAG_NAME=objc

CLIBRARY_NAME = libobjc

# dce, decosf1, irix, mach, os2, posix, pthreads, single, solaris, vxworks
THREADING = posix
ifeq ($(GNUSTEP_TARGET_OS),netbsdelf)
ADDITIONAL_CPPFLAGS += -DMISSING_SCHED_PARAM_STRUCT
endif
ifeq ($(GNUSTEP_TARGET_OS),mingw32)
THREADING = win32
endif
ifeq ($(GNUSTEP_TARGET_OS), cygwin)
THREADING = win32
endif
ifeq ($(findstring darwin, $(GNUSTEP_TARGET_OS)), darwin)      
ifeq ($(CC_CPPPRECOMP), yes)
INTERNAL_CFLAGS += -no-cpp-precomp
INTERNAL_OBJCFLAGS += -no-cpp-precomp
endif
endif

GC_HEADER_FILES_DIR = ./gc/include
GC_HEADER_FILES = \
	cord.h		\
	ec.h		\
	gc.h		\
	gc_alloc.h	\
	gc_cpp.h	\
	gc_inl.h	\
	gc_inline.h	\
	gc_typed.h	\
	weakpointer.h	\

libobjc_HEADER_FILES = \
	hash.h objc-list.h sarray.h \
	objc.h objc-api.h objc-decls.h \
	NXConstStr.h Object.h \
	Protocol.h encoding.h typedstream.h \
	thr.h

libobjc_OBJC_FILES = \
	Object.m	\
	Protocol.m	\
	synchronization.m \
	linking.m

ifeq ($(GNUSTEP_TARGET_OS), cygwin)
  extra_C_FILES=libobjc_entry.c
else
  extra_C_FILES=
endif

libobjc_C_FILES = \
	archive.c		\
	class.c			\
	encoding.c		\
	gc.c			\
	hash.c			\
	init.c			\
	misc.c			\
	nil_method.c		\
	objects.c		\
	sarray.c		\
	selector.c		\
	sendmsg.c		\
	thr-$(THREADING).c      \
	thr.c			\
	$(extra_C_FILES)

# Add -DDEBUG_RUNTIME to add debug printf statments 
ADDITIONAL_CPPFLAGS += \
	-DIN_GCC \
	-pipe		\
	-DSTDC_HEADERS=1\
	-DHAVE_STDLIB_H

CC1OBJ = `$(CC) -print-prog-name=cc1obj`

ADDITIONAL_CFLAGS += -Wall

libobjc_HEADER_FILES_DIR         = objc
libobjc_HEADER_FILES_INSTALL_DIR = objc

libobjc_DLL_DEF = libobjc.def

ifeq ($(THREADING), single)
ADDITIONAL_CPPFLAGS += -DOBJC_WITHOUT_THREADING
endif

ifeq ($(gc), yes)
ADDITIONAL_CPPFLAGS     += -DOBJC_WITH_GC=1 -DGC_DEBUG=1
ADDITIONAL_CPPFLAGS     += -DDEBUG_OBJC_GC=0
libobjc_LIBRARIES_DEPEND_UPON += -lgc

ifeq ($(THREADING), solaris)
ADDITIONAL_CPPFLAGS += -DSOLARIS_THREADS
endif

else # gc
ADDITIONAL_CPPFLAGS += -DOBJC_WITH_GC=0 -DDEBUG_OBJC_GC=0
endif

ifeq ($(gc), xyes)
GC_OFILES = \
	alloc.o reclaim.o allchblk.o misc.o mach_dep.o os_dep.o mark_rts.o \
	headers.o mark.o obj_map.o blacklst.o finalize.o new_hblk.o dbg_mlc.o \
	malloc.o stubborn.o checksums.o solaris_threads.o irix_threads.o \
	typd_mlc.o ptr_chck.o mallocx.o solaris_pthreads.o \
	dyn_load.o \

ADDITIONAL_LIBRARY_OBJ_FILES = $(addprefix gc/, $(GC_OFILES))
endif

-include config/$(GNUSTEP_TARGET_CPU)/config.make
-include config/$(GNUSTEP_TARGET_CPU)/$(GNUSTEP_TARGET_OS)/config.make

-include GNUmakefile.preamble

include $(GNUSTEP_MAKEFILES)/clibrary.make

-include GNUmakefile.postamble

ADDITIONAL_INCLUDE_DIRS += \
	-Iconfig/$(GNUSTEP_TARGET_CPU)/$(GNUSTEP_TARGET_OS) \
	-Iconfig/$(GNUSTEP_TARGET_CPU)/generic \
	-I.

before-all:: runtime-info.h

ifeq ($(gc),xyes)

after-install::
	for file in $(GC_HEADER_FILES) __done; do \
	  if [ $$file != __done ]; then \
	    $(INSTALL_DATA) $(GC_HEADER_FILES_DIR)/$$file \
	    $(GNUSTEP_HEADERS)$(libobjc_HEADER_FILES_INSTALL_DIR)/$$file ; \
	  fi; \
	done;

endif

$(GNUSTEP_OBJ_DIR)/thr-backends: $(GNUSTEP_OBJ_DIR)
	@if [ ! -d $@ ]; then echo mkdir $@; mkdir $@; fi

ifeq ($(GNUSTEP_TARGET_OS),mingw32)
runtime-info.h: 
	./get-runtime-info $(CC1OBJ)
else
runtime-info.h: 
		echo "" > tmp-runtime
		echo "/* This file is automatically generated */" > $@
		$(CC1OBJ) -print-objc-runtime-info tmp-runtime >> $@
		rm -f tmp-runtime
endif

ifeq ($(findstring darwin, $(GNUSTEP_TARGET_OS)), darwin)      
after-install::
	rm -f $(GNUSTEP_LIBRARIES)/$(GNUSTEP_TARGET_DIR)/libobjc-gnu.dylib
	ln -s libobjc.dylib $(GNUSTEP_LIBRARIES)/$(GNUSTEP_TARGET_DIR)/libobjc-gnu.dylib
before-uninstall::
	rm -f $(GNUSTEP_LIBRARIES)/$(GNUSTEP_TARGET_DIR)/libobjc-gnu.dylib
endif

after-clean::
	rm -f runtime-info.h tmp-runtime.s

after-distclean::
	rm -f config.status config.log config.cache

