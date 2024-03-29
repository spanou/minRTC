#=============================================================================
# Copyright (c) 2019, Sakis Panou <sakis.panou@gmail.com>
# All rights reserved.

# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions are met:
#     * Redistributions of source code must retain the above copyright
#       notice, this list of conditions and the following disclaimer.
#     * Redistributions in binary form must reproduce the above copyright
#       notice, this list of conditions and the following disclaimer in the
#       documentation and/or other materials provided with the distribution.
#     * Neither the name of the <organization> nor the
#       names of its contributors may be used to endorse or promote products
#       derived from this software without specific prior written permission.

# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS"
# AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE
# IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS FOR A PARTICULAR PURPOSE
# ARE DISCLAIMED. IN NO EVENT SHALL THE COPYRIGHT HOLDER OR CONTRIBUTORS BE
# LIABLE FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR
# CONSEQUENTIAL DAMAGES (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF
# SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS
# INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN
# CONTRACT, STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
# ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED OF THE
# POSSIBILITY OF SUCH DAMAGE.
#=============================================================================

EXECUTABLE=rtc_validation
LIB_RTC_NAME=min_rtc
LIB_RTC_VER_MAJ=1
LIB_RTC_VER_MIN=0
LIB_RTC_VER_REL=0
LIB_RTC_PREFIX=lib

LIB_RTC_LINKER=$(LIB_RTC_PREFIX)$(LIB_RTC_NAME).so
LIB_RTC_SONAME=$(LIB_RTC_LINKER).$(LIB_RTC_VER_MAJ)
LIB_RTC_REAL= $(LIB_RTC_SONAME).$(LIB_RTC_VER_MIN).$(LIB_RTC_VER_REL)

LN?=ln

OSFLAG :=

ifeq (0, ${MAKELEVEL})
	CURDIR?=$(PWD)
else
	CURDIR?=$(PWD)/$(SUBDIR)
endif

LIBPATH?=$(CURDIR)/../../lib

ifeq ($(OS),Windows_NT)
	OSFLAG += -D WIN32
	ifeq ($(PROCESSOR_ARCHITEW6432),AMD64)
		OSFLAG += -D AMD64
	else
		ifeq ($(PROCESSOR_ARCHITECTURE),AMD64)
			OSFLAG += -D AMD64
		endif
		ifeq ($(PROCESSOR_ARCHITECTURE),x86)
			OSFLAG += -D IA32
		endif
	endif
else
	UNAME_S := $(shell uname -s)
	ifeq ($(UNAME_S),Linux)
		OSFLAG += -D LINUX
	endif
	ifeq ($(UNAME_S),Darwin)
		OSFLAG += -D OSX
	endif
	UNAME_P := $(shell uname -p)
	ifeq ($(UNAME_P),x86_64)
		OSFLAG += -D AMD64
	endif
	ifneq ($(filter %86,$(UNAME_P)),)
		OSFLAG += -D IA32
	endif
	ifneq ($(filter arm%,$(UNAME_P)),)
		OSFLAG += -D ARM
	endif
endif

#vpath=

# LOG_LEVEL values:
#
#  NONE=1,
#  INFO=2,
#  WARNING=4,
#  ERROR=8,
#  CRITICAL=16
LOGL?= 1

# LOG_MODE values:
#  FLAG=0
#  LEVEL=1
LOGM?= 0

CXX= g++
CXXFLAGS= -Wall -std=c++11 -g -I $(LIBPATH)/include -D LOG_LEVEL=$(LOGL) -D LOG_MODE=$(LOGM)
CPP_SRC?= $(wildcard ./*.cpp)

LFLAGS= -Wl,-rpath=$(LIBPATH)/ -o


OBJS= $(CPP_SRC:.cpp=.o)
DEPS= $(CPP_SRC:.cpp=.d)

%.o:%.cpp
	$(CXX) $(CXXFLAGS) -c $< -o $@

%.d:%.cpp
	$(CXX) $(CXXFLAGS) -M -MF"$@" $<


$(EXECUTABLE): $(OBJS) $(DEPS) $(LIBPATH)/$(LIB_RTC_LINKER)
	$(CXX) $(OBJS) $(LFLAGS) $@ -L$(LIBPATH)/ -l$(LIB_RTC_NAME)

all: $(EXECUTABLE)

.PHONY: printinfo
printinfo:
	@echo "Compiler Pre Defines ..........."
	$(CC) -dM -E - < /dev/null
	@echo "CXXFLAGS ......................."
	@echo $(CXXFLAGS)
	@echo "CFLAGS ........................."
	@echo $(CFLAGS)
	@echo "LFLAGS ........................."
	@echo $(LFLAGS)
	@echo "AR.............................."
	@echo $(AR)
	@echo "LN.............................."
	@echo $(LN)
	@echo "OSFLAG.........................."
	@echo $(OSFLAG)
	@echo "PWD............................."
	@echo $(PWD)

.PHONY: clean
clean:
	$(RM) $(EXECUTABLE) *.o *.d

.PHONY: rebuild
rebuild: clean all