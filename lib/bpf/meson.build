# SPDX-License-Identifier: BSD-3-Clause
# Copyright(c) 2018 Intel Corporation

if is_windows
    build = false
    reason = 'not supported on Windows'
    subdir_done()
endif

cflags += no_wvla_cflag

if arch_subdir == 'x86' and dpdk_conf.get('RTE_ARCH_32')
    build = false
    reason = 'not supported on 32-bit x86'
    subdir_done()
endif

sources = files('bpf.c',
        'bpf_dump.c',
        'bpf_exec.c',
        'bpf_load.c',
        'bpf_pkt.c',
        'bpf_stub.c',
        'bpf_validate.c')

if arch_subdir == 'x86' and dpdk_conf.get('RTE_ARCH_64')
    sources += files('bpf_jit_x86.c')
elif dpdk_conf.has('RTE_ARCH_ARM64')
    sources += files('bpf_jit_arm64.c')
endif

headers = files('bpf_def.h',
        'rte_bpf.h',
        'rte_bpf_ethdev.h')

deps += ['mbuf', 'net', 'ethdev']

dep = dependency('libelf', required: false, method: 'pkg-config')
if dep.found()
    dpdk_conf.set('RTE_LIBRTE_BPF_ELF', 1)
    sources += files('bpf_load_elf.c')
    ext_deps += dep
else
    warning('libelf is missing, rte_bpf_elf_load API will be disabled')
endif

if dpdk_conf.has('RTE_HAS_LIBPCAP')
    sources += files('bpf_convert.c')
    ext_deps += pcap_dep
else
    warning('libpcap is missing, rte_bpf_convert API will be disabled')
endif
