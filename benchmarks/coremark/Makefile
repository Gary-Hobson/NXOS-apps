############################################################################
# apps/benchmarks/coremark/Makefile
#
# Licensed to the Apache Software Foundation (ASF) under one or more
# contributor license agreements.  See the NOTICE file distributed with
# this work for additional information regarding copyright ownership.  The
# ASF licenses this file to you under the Apache License, Version 2.0 (the
# "License"); you may not use this file except in compliance with the
# License.  You may obtain a copy of the License at
#
#   http://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS, WITHOUT
# WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.  See the
# License for the specific language governing permissions and limitations
# under the License.
#
############################################################################

include $(APPDIR)/Make.defs

# Coremark application

############################################################################
# Flags
############################################################################

COREMARK_URL ?= "https://github.com/eembc/coremark/archive"

COREMARK_VERSION = main
COREMARK_ZIP = $(COREMARK_VERSION).zip

COREMARK_UNPACKNAME = coremark
UNPACK ?= unzip -q -o

VPATH += $(COREMARK_UNPACKNAME)
VPATH += $(COREMARK_UNPACKNAME)$(DELIM)posix

CFLAGS += -Wno-undef

ifeq ($(CONFIG_COREMARK_MULTITHREAD_OVERRIDE),y)
COREMARK_NTHREADS = $(CONFIG_COREMARK_MULTITHREAD_COUNT)
else
ifneq ($(CONFIG_SMP_NCPUS),)
COREMARK_NTHREADS = $(CONFIG_SMP_NCPUS)
else
COREMARK_NTHREADS = 1
endif
endif

CFLAGS += ${DEFINE_PREFIX}USE_PTHREAD ${DEFINE_PREFIX}PERFORMANCE_RUN=1
CFLAGS += ${DEFINE_PREFIX}MULTITHREAD=$(COREMARK_NTHREADS)
CFLAGS += ${DEFINE_PREFIX}FLAGS_STR="\"Using NuttX compilation options\""
CFLAGS += ${DEFINE_PREFIX}MEM_LOCATION="\"Defined by the NuttX configuration\""

ifeq ($(CONFIG_COREMARK_ITERATIONS_OVERRIDE),y)
CFLAGS += ${DEFINE_PREFIX}ITERATIONS=$(CONFIG_COREMARK_ITERATIONS_COUNT)
endif

CSRCS =  core_list_join.c
CSRCS += core_matrix.c
CSRCS += core_state.c
CSRCS += core_util.c
CSRCS += core_portme.c

############################################################################
# Targets
############################################################################

$(COREMARK_ZIP):
	@echo "Downloading: $(COREMARK_URL)/$(COREMARK_ZIP)"
	$(Q) curl -O -L $(COREMARK_URL)/$(COREMARK_ZIP)

$(COREMARK_UNPACKNAME): $(COREMARK_ZIP)
	@echo "Unpacking: $(COREMARK_ZIP) -> $(COREMARK_UNPACKNAME)"
	$(Q) $(UNPACK) $(COREMARK_ZIP)
	$(Q) mv	coremark-$(COREMARK_VERSION) $(COREMARK_UNPACKNAME)
	$(Q) touch $(COREMARK_UNPACKNAME)

clean::
	$(call DELFILE, $(OBJS))

# Download and unpack tarball if no git repo found

ifeq ($(wildcard $(COREMARK_UNPACKNAME)/.git),)
context:: $(COREMARK_UNPACKNAME)

distclean::
	$(call DELDIR, $(COREMARK_UNPACKNAME))
	$(call DELFILE, $(COREMARK_ZIP))
endif

############################################################################
# Applications Configuration
############################################################################

MODULE = $(CONFIG_BENCHMARK_COREMARK)

PROGNAME  += $(CONFIG_COREMARK_PROGNAME)
PRIORITY  += $(CONFIG_COREMARK_PRIORITY)
STACKSIZE += $(CONFIG_COREMARK_STACKSIZE)

MAINSRC += $(COREMARK_UNPACKNAME)/core_main.c

include $(APPDIR)/Application.mk