# SPDX-License-Identifier: ISC
#obj-$(CONFIG_ATH9K_HW)		+= ath9k/
#obj-$(CONFIG_ATH10K)		+= ath10k/

obj-$(CONFIG_ATH_COMMON)	+= ath.o

ath-objs :=	main.o \
		regd.o \
		hw.o \
		key.o \
		dfs_pattern_detector.o \
		dfs_pri_detector.o

ath-$(CONFIG_ATH_DEBUG) += debug.o
ath-$(CONFIG_ATH_TRACEPOINTS) += trace.o

CFLAGS_trace.o := -I$(src)
