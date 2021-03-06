# Makefile for efabless design kits for SkyWater Sky130:
#
#  sky130A      =  5-metal backend stack with dual MiM
#
# Written by Tim Edwards March 2019
# efabless corporation
# updated October 2019
# updated December 2019 (divide installation sections for individual tools)
# updated March 2020 (refactored the install process)
# updated May 2020 (changed to new process name Sky130)
#
# Instructions:
#
# Modify values below as needed:
#
#	VENDOR_PATH: points to vendor sources
#	EF_STYLE: 1 for efabless style, 0 otherwise
#	LINK_TARGETS: link back to source or link to 1st PDK when possible
#	DIST_PATH: install location for distributed install
#	LOCAL_PATH: install location for local install or runtime location
#		    for distributed install
#
# Run "make" to stage the PDK for tool setup and vendor libraries

# If installing into the final destination (local install):
#
#   Run "make install-local" to install all staged files
#   ("make install" is equivalent to "make install-local")
#
# If installing into a repository to be distributed to the final destination:
#
#   Run "make install-dist" to install all staged files
#
# Run "make clean" to remove all staging files.
#
# Run "make veryclean" to remove all staging and install log files.
#
# For the sake of simplicity, the "standard" local install can be done
# with the usual
#
#   make
#   make install
#   make clean
#
#--------------------------------------------------------------------
# This Makefile contains bash-isms
SHELL = bash

REVISION    = 20200508
TECH        = sky130

# If EF_STYLE is set to 1, then efabless naming conventions are
# used, otherwise the generic naming conventions are used.
# Mainly, the hierarchy of library names and file types is reversed
# (e.g., sky130_fd_sc_hd/lef vs. lef/sky130_fd_sc_hd).

EF_STYLE = 0
# EF_STYLE = 1

# Normally it's fine to keep the staging path in a local directory,
# although /tmp or a dedicated staging area are also fine, as long
# as the install process can write to the path.

STAGING_PATH = `pwd`

# If LINK_TARGETS is set to "none", then files are copied
# from the SkyWater sources to the target.  If set to "source",
# symbolic links are made in the target directories pointing
# back to the SkyWater sources.  If set to the name of another
# PDK (e.g, "sky130A"), then symbolic links are made to the
# same files in that PDK, where they exist, and are copied
# from source, where they don't.

# LINK_TARGETS = source
# LINK_TARGETS = none
# LINK_TARGETS = sky130A
LINK_TARGETS = source

# Paths:

# Path to skywater_pdk (to be changed to public repo;  work in progress)
# Version below comes from foss-eda-tools.googlesource.com
# SKYWATER_PATH = ~/gits/skywater-pdk-scratch/skywater-pdk/libraries
# Version below is also from foss-eda-tools and is more recent than
# the scratch repo above.
SKYWATER_PATH = ~/gits/skywater-pdk/libraries

# Path to OSU standard cell library sources
OSU_PATH = ~/gits/osu_130_pdk

# NOTE:  Install destination is the git repository of the technology platform.
# Once updated in git, the git project can be distributed to all hosts.
#
ifeq (${EF_STYLE}, 1)
    LOCAL_PATH = /ef/tech/SW
    CONFIG_DIR = .ef-config
    REV_DIR = ${REVISION}
else
    # LOCAL_PATH = /usr/local/share/vlsi/SkyWater
    LOCAL_PATH = ~/projects/efabless/tech/SW
    CONFIG_DIR = .config
    REV_DIR = .
endif

DIST_PATH = ~/gits/ef-skywater-${TECH}

# EF process nodes created from the master sources
SKY130A = sky130A

ifeq (${LINK_TARGETS}, ${SKY130A})
    DIST_LINK_TARGETS = ${LOCAL_PATH}/${LINK_TARGETS}
else
    DIST_LINK_TARGETS = ${LINK_TARGETS}
endif

# Basic definitions for each EF process node
SKY130A_DEFS = -DTECHNAME=sky130A -DREVISION=${REVISION}

# Module definitions for each process node
# (Note that MOS is default and therefore not used anywhere)
# SKY130A_DEFS += -DMETAL5 -DMIM -DREDISTRIBUTION
SKY130A_DEFS += -DMETAL5 -DMIM

# Add staging path
SKY130A_DEFS += -DSTAGING_PATH=${STAGING_PATH}

ifeq (${EF_STYLE}, 1)
    EF_FORMAT = -ef_format
    SKY130A_DEFS += -DEF_FORMAT
else
    EF_FORMAT = -std_format
endif

MAGICTOP = libs.tech/magic
NETGENTOP = libs.tech/netgen
QFLOWTOP = libs.tech/qflow
KLAYOUTTOP = libs.tech/klayout
OPENLANETOP = libs.tech/openlane

ifeq (${EF_STYLE}, 1)
    MAGICPATH = ${MAGICTOP}/${REVISION}
else
    MAGICPATH = ${MAGICTOP}
endif

# Currently, netgen, qflow, and klayout do not use revisioning (needs to change!)
NETGENPATH = ${NETGENTOP}
QFLOWPATH = ${QFLOWTOP}
KLAYOUTPATH = ${KLAYOUTTOP}
OPENLANEPATH = ${OPENLANETOP}

MAGICTOP_STAGING_A = ${STAGING_PATH}/${SKY130A}/${MAGICTOP}
NETGENTOP_STAGING_A = ${STAGING_PATH}/${SKY130A}/${NETGENTOP}
QFLOWTOP_STAGING_A = ${STAGING_PATH}/${SKY130A}/${QFLOWTOP}
KLAYOUTTOP_STAGING_A = ${STAGING_PATH}/${SKY130A}/${KLAYOUTTOP}
OPENLANETOP_STAGING_A = ${STAGING_PATH}/${SKY130A}/${OPENLANETOP}

MAGIC_STAGING_A = ${STAGING_PATH}/${SKY130A}/${MAGICPATH}
NETGEN_STAGING_A = ${STAGING_PATH}/${SKY130A}/${NETGENPATH}
QFLOW_STAGING_A = ${STAGING_PATH}/${SKY130A}/${QFLOWPATH}
KLAYOUT_STAGING_A = ${STAGING_PATH}/${SKY130A}/${KLAYOUTPATH}
OPENLANE_STAGING_A = ${STAGING_PATH}/${SKY130A}/${OPENLANEPATH}

SKY130A_DEFS += -DMAGIC_CURRENT=${MAGICTOP}/current

# Where cpp syntax is followed, this is equivalent to cpp, but it does not
# mangle non-C source files under the belief that they are actually C code.
CPP   = ../common/preproc.py

# The following script in the ../common directory does most of the work of
# copying or linking the foundry vendor files to the target directory.
STAGE = set -f ; ../common/foundry_install.py ${EF_FORMAT}
INSTALL = ../common/staging_install.py ${EF_FORMAT}

# The script(s) below are used for custom changes to the vendor PDK files
ADDPROP = ../common/insert_property.py ${EF_FORMAT} 

# List the EDA tools to install local setup files for
TOOLS = magic qflow netgen klayout openlane

all: all-a

all-a:
	echo "Starting sky130A PDK staging on "`date` > ${SKY130A}_install.log
	${MAKE} tools-a
	${MAKE} vendor-a
	echo "Ended sky130A PDK staging on "`date` >> ${SKY130A}_install.log

tools-a: general-a magic-a qflow-a netgen-a klayout-a openlane-a

general-a: ${TECH}.json
	mkdir -p ${STAGING_PATH}/${SKY130A}/${CONFIG_DIR}
	rm -f ${STAGING_PATH}/${SKY130A}/${CONFIG_DIR}/nodeinfo.json
	${CPP} ${SKY130A_DEFS} ${TECH}.json > \
		${STAGING_PATH}/${SKY130A}/${CONFIG_DIR}/nodeinfo.json

magic-a: magic/${TECH}.tech magic/${TECH}gds.tech magic/${TECH}.magicrc magic/${TECH}.tcl
	mkdir -p ${MAGICTOP_STAGING_A}
	mkdir -p ${MAGIC_STAGING_A}
	rm -f ${MAGICTOP_STAGING_A}/current
	rm -f ${MAGIC_STAGING_A}/${SKY130A}.tech
	rm -f ${MAGIC_STAGING_A}/${SKY130A}-GDS.tech
	rm -f ${MAGIC_STAGING_A}/${SKY130A}.tcl
	rm -f ${MAGIC_STAGING_A}/${SKY130A}-BindKeys
	rm -f ${MAGIC_STAGING_A}/magicrc
	(cd ${MAGICTOP_STAGING_A} ; ln -s ${REV_DIR} current)
	cp -rp custom/scripts/seal_ring_generator ${MAGIC_STAGING_A}/.
	${CPP} ${SKY130A_DEFS} magic/${TECH}.tech > ${MAGIC_STAGING_A}/${SKY130A}.tech
	${CPP} ${SKY130A_DEFS} magic/${TECH}gds.tech > ${MAGIC_STAGING_A}/${SKY130A}-GDS.tech
	${CPP} ${SKY130A_DEFS} magic/${TECH}.magicrc > ${MAGIC_STAGING_A}/${SKY130A}.magicrc
	${CPP} ${SKY130A_DEFS} ../common/pdk.bindkeys > ${MAGIC_STAGING_A}/${SKY130A}-BindKeys
	${CPP} ${SKY130A_DEFS} magic/${TECH}.tcl > ${MAGIC_STAGING_A}/${SKY130A}.tcl
	${CPP} ${SKY130A_DEFS} ../common/pdk.tcl >> ${MAGIC_STAGING_A}/${SKY130A}.tcl

qflow-a: qflow/${TECH}.sh qflow/${TECH}.par
	mkdir -p ${QFLOWTOP_STAGING_A}
	mkdir -p ${QFLOW_STAGING_A}
	rm -f ${QFLOW_STAGING_A}/${SKY130A}hd.sh
	rm -f ${QFLOW_STAGING_A}/${SKY130A}hd.par
	rm -f ${QFLOW_STAGING_A}/${SKY130A}hdll.sh
	rm -f ${QFLOW_STAGING_A}/${SKY130A}hdll.par
	rm -f ${QFLOW_STAGING_A}/${SKY130A}hs.sh
	rm -f ${QFLOW_STAGING_A}/${SKY130A}hs.par
	rm -f ${QFLOW_STAGING_A}/${SKY130A}hvl.sh
	rm -f ${QFLOW_STAGING_A}/${SKY130A}hvl.par
	rm -f ${QFLOW_STAGING_A}/${SKY130A}ls.sh
	rm -f ${QFLOW_STAGING_A}/${SKY130A}ls.par
	rm -f ${QFLOW_STAGING_A}/${SKY130A}lp.sh
	rm -f ${QFLOW_STAGING_A}/${SKY130A}lp.par
	rm -f ${QFLOW_STAGING_A}/${SKY130A}ms.sh
	rm -f ${QFLOW_STAGING_A}/${SKY130A}ms.par
	rm -f ${QFLOW_STAGING_A}/${SKY130A}osu.sh
	rm -f ${QFLOW_STAGING_A}/${SKY130A}osu.par
	${CPP} ${SKY130A_DEFS} -DLIBRARY=sky130_fd_sc_hd qflow/${TECH}.sh > \
		${QFLOW_STAGING_A}/${SKY130A}hd.sh
	${CPP} ${SKY130A_DEFS} -DLIBRARY=sky130_fd_sc_hdll qflow/${TECH}.sh > \
		${QFLOW_STAGING_A}/${SKY130A}hdll.sh
	${CPP} ${SKY130A_DEFS} -DLIBRARY=sky130_fd_sc_hvl qflow/${TECH}.sh > \
		${QFLOW_STAGING_A}/${SKY130A}hvl.sh
	${CPP} ${SKY130A_DEFS} -DLIBRARY=sky130_fd_sc_hs qflow/${TECH}.sh > \
		${QFLOW_STAGING_A}/${SKY130A}hs.sh
	${CPP} ${SKY130A_DEFS} -DLIBRARY=sky130_fd_sc_lp qflow/${TECH}.sh > \
		${QFLOW_STAGING_A}/${SKY130A}lp.sh
	${CPP} ${SKY130A_DEFS} -DLIBRARY=sky130_fd_sc_ls qflow/${TECH}.sh > \
		${QFLOW_STAGING_A}/${SKY130A}ls.sh
	${CPP} ${SKY130A_DEFS} -DLIBRARY=sky130_fd_sc_ms qflow/${TECH}.sh > \
		${QFLOW_STAGING_A}/${SKY130A}ms.sh
	${CPP} ${SKY130A_DEFS} -DLIBRARY=sky130_osu_sc qflow/sky130osu.sh > \
		${QFLOW_STAGING_A}/${SKY130A}osu.sh
	${CPP} ${SKY130A_DEFS} qflow/${TECH}.par > ${QFLOW_STAGING_A}/${SKY130A}hd.par
	${CPP} ${SKY130A_DEFS} qflow/${TECH}.par > ${QFLOW_STAGING_A}/${SKY130A}hdll.par
	${CPP} ${SKY130A_DEFS} qflow/${TECH}.par > ${QFLOW_STAGING_A}/${SKY130A}hvl.par
	${CPP} ${SKY130A_DEFS} qflow/${TECH}.par > ${QFLOW_STAGING_A}/${SKY130A}hs.par
	${CPP} ${SKY130A_DEFS} qflow/${TECH}.par > ${QFLOW_STAGING_A}/${SKY130A}ms.par
	${CPP} ${SKY130A_DEFS} qflow/${TECH}.par > ${QFLOW_STAGING_A}/${SKY130A}lp.par
	${CPP} ${SKY130A_DEFS} qflow/${TECH}.par > ${QFLOW_STAGING_A}/${SKY130A}ls.par
	${CPP} ${SKY130A_DEFS} qflow/${TECH}.par > ${QFLOW_STAGING_A}/${SKY130A}osu.par

netgen-a: netgen/${TECH}_setup.tcl
	mkdir -p ${NETGENTOP_STAGING_A}
	mkdir -p ${NETGEN_STAGING_A}
	rm -f ${NETGEN_STAGING_A}/${SKY130A}_setup.tcl
	rm -f ${NETGEN_STAGING_A}/setup.tcl
	${CPP} ${SKY130A_DEFS} netgen/${TECH}_setup.tcl > ${NETGEN_STAGING_A}/${SKY130A}_setup.tcl
	(cd ${NETGEN_STAGING_A} ; ln -s ${SKY130A}_setup.tcl setup.tcl)

klayout-a: klayout/${TECH}.lyp klayout/${TECH}.lyt
	mkdir -p ${KLAYOUTTOP_STAGING_A}
	mkdir -p ${KLAYOUT_STAGING_A}
	rm -f ${KLAYOUT_STAGING_A}/${SKY130A}.lyp
	rm -f ${KLAYOUT_STAGING_A}/${SKY130A}.lyt
	${CPP} ${SKY130A_DEFS} klayout/${TECH}.lyp > ${KLAYOUT_STAGING_A}/${SKY130A}.lyp
	${CPP} ${SKY130A_DEFS} klayout/${TECH}.lyt > ${KLAYOUT_STAGING_A}/${SKY130A}.lyt

openlane-a: openlane/common_pdn.tcl openlane/config.tcl openlane/sky130_fd_sc_hd/config.tcl  openlane/sky130_fd_sc_hs/config.tcl
	mkdir -p ${OPENLANETOP_STAGING_A}
	mkdir -p ${OPENLANE_STAGING_A}
	mkdir -p ${OPENLANE_STAGING_A}/sky130_fd_sc_hd
	mkdir -p ${OPENLANE_STAGING_A}/sky130_fd_sc_hs
	rm -f ${OPENLANE_STAGING_A}/common_pdn.info
	rm -f ${OPENLANE_STAGING_A}/config.tcl
	rm -f ${OPENLANE_STAGING_A}/sky130_fd_sc_hd/config.tcl
	rm -f ${OPENLANE_STAGING_A}/sky130_fd_sc_hd/tracks.info
	rm -f ${OPENLANE_STAGING_A}/sky130_fd_sc_hd/no_synth.cells
	rm -f ${OPENLANE_STAGING_A}/sky130_fd_sc_hd/sky130_fd_sc_hd__fakediode_2.gds
	rm -f ${OPENLANE_STAGING_A}/sky130_fd_sc_hs/config.tcl
	rm -f ${OPENLANE_STAGING_A}/sky130_fd_sc_hs/tracks.info
	rm -f ${OPENLANE_STAGING_A}/sky130_fd_sc_hs/no_synth.cells
	${CPP} ${SKY130A_DEFS} openlane/common_pdn.tcl > ${OPENLANE_STAGING_A}/common_pdn.tcl
	${CPP} ${SKY130A_DEFS} openlane/config.tcl > ${OPENLANE_STAGING_A}/config.tcl
	${CPP} ${SKY130A_DEFS} openlane/sky130_fd_sc_hd/config.tcl > ${OPENLANE_STAGING_A}/sky130_fd_sc_hd/config.tcl
	${CPP} ${SKY130A_DEFS} openlane/sky130_fd_sc_hd/tracks.info > ${OPENLANE_STAGING_A}/sky130_fd_sc_hd/tracks.info
	${CPP} ${SKY130A_DEFS} openlane/sky130_fd_sc_hd/no_synth.cells > ${OPENLANE_STAGING_A}/sky130_fd_sc_hd/no_synth.cells
	cp openlane/sky130_fd_sc_hd/sky130_fd_sc_hd__fakediode_2.gds ${OPENLANE_STAGING_A}/sky130_fd_sc_hd/sky130_fd_sc_hd__fakediode_2.gds
	${CPP} ${SKY130A_DEFS} openlane/sky130_fd_sc_hs/config.tcl > ${OPENLANE_STAGING_A}/sky130_fd_sc_hs/config.tcl
	${CPP} ${SKY130A_DEFS} openlane/sky130_fd_sc_hs/tracks.info > ${OPENLANE_STAGING_A}/sky130_fd_sc_hs/tracks.info
	${CPP} ${SKY130A_DEFS} openlane/sky130_fd_sc_hs/no_synth.cells > ${OPENLANE_STAGING_A}/sky130_fd_sc_hs/no_synth.cells

vendor-a:
	# Install base device models from vendor files
	# (NOTE: .mod and .pm3 files should not be in /cells/?)
	${STAGE} -source ${SKYWATER_PATH} -target ${STAGING_PATH}/${SKY130A} \
		-ngspice sky130_fd_pr_base/v%v/models/* filter=custom/scripts/fixspice.py \
		-ngspice sky130_fd_pr_base/v%v/cells/*.mod filter=custom/scripts/fixspice.py \
		-ngspice sky130_fd_pr_base/v%v/cells/*.pm3 filter=custom/scripts/fixspice.py \
		|& tee -a ${SKY130A}_install.log
	# Install RF device models from vendor files
	${STAGE} -source ${SKYWATER_PATH} -target ${STAGING_PATH}/${SKY130A} \
		-ngspice sky130_fd_pr_rf/v%v/models/* filter=custom/scripts/fixspice.py \
		|& tee -a ${SKY130A}_install.log
	# Install additional RF device models from vendor files
	${STAGE} -source ${SKYWATER_PATH} -target ${STAGING_PATH}/${SKY130A} \
		-ngspice sky130_fd_pr_rf2/v%v/models/* filter=custom/scripts/fixspice.py \
		|& tee -a ${SKY130A}_install.log
	# Install base device library from vendor files
	${STAGE} -source ${SKYWATER_PATH} -target ${STAGING_PATH}/${SKY130A} \
		-gds %l/v%v/cells/*/*.gds \
		-spice %l/v%v/cells/*/*.spice ignore=topography \
		-spice %l/v%v/cells/*/*.sp \
		-library primitive sky130_fd_pr_base \
		-library primitive sky130_fd_pr_rf \
		-library primitive sky130_fd_pr_rf2 |& tee -a ${SKY130A}_install.log
	# Install SkyWater I/O pad library
	# Purposely ignoring "-lef sky130_fd_io/v%v/lef/*.lef" and making our own LEF views
	${STAGE} -source ${SKYWATER_PATH} -target ${STAGING_PATH}/${SKY130A} \
		-gds %l/v%v/cells/*/*.gds \
		-verilog %l/v%v/cells/*/*.v \
		-lib %l/v%v/cells/*/*.lib \
		-doc %l/v%v/cells/*/*.doc \
		-cdl %l/v%v/cells/*/*.cdl ignore=topography \
		-spice %l/v%v/cells/*/*.spice  \
		-library general sky130_fd_io |& tee -a ${SKY130A}_install.log
        # Install all SkyWater digital standard cells.
	${STAGE} -source ${SKYWATER_PATH} -target ${STAGING_PATH}/${SKY130A} \
		-techlef %l/latest/tech/*.tlef \
		-spice %l/latest/cells/*/*.spice compile-only \
		-cdl %l/latest/cells/*/*.cdl ignore=topography compile-only \
		-lef %l/latest/cells/*/*.lef compile-only \
		-doc %l/latest/cells/*/*.pdf \
		-lib %l/latest/timing/*.lib \
		-gds %l/latest/cells/*/*.gds compile-only \
		-verilog %l/latest/cells/*/*.v exclude=*.*.v compile-only \
		-library digital sky130_fd_sc_hd \
		-library digital sky130_fd_sc_hdll \
		-library digital sky130_fd_sc_hvl \
		-library digital sky130_fd_sc_hs \
		-library digital sky130_fd_sc_ls \
		-library digital sky130_fd_sc_ms \
		-library digital sky130_fd_sc_lp |& tee -a ${SKY130A}_install.log
        # Install OSU digital standard cells.
	${STAGE} -source ${OSU_PATH} -target ${STAGING_PATH}/${SKY130A} \
		-techlef char/techfiles/scs8.lef rename sky130_osu_sc.tlef \
		-spice lib/spice/*.spice compile-only \
		-lef outputs/s8_osu130.lef rename sky130_osu_sc.lef \
		-lib outputs/*.lib \
		-gds lib/gds/*.gds compile-only \
		-verilog outputs/VERILOG/*.v \
		-library digital sky130_osu_sc
	# Install additional model file (efabless)
	${STAGE} -source ./custom -target ${STAGING_PATH}/${SKY130A} \
		-ngspice models/*.lib rename ${SKY130A}.lib \
		|& tee -a ${SKY130A}_install.log
	# Install custom additions to I/O pad library
	${STAGE} -source ./custom -target ${STAGING_PATH}/${SKY130A} \
		-gds %l/gds/*.gds \
		-verilog %l/verilog/*.v \
		-lef %l/lef/*.lef \
		-spice %l/spice/*.spice \
		-library general sky130_fd_io |& tee -a ${SKY130A}_install.log
	# Add correct bounding boxes on Magic layouts
	${ADDPROP} ${STAGING_PATH}/${SKY130A} sky130_fd_io sky130_fd_io_top_gpio_ovtv2 \
		"FIXED_BBOX 0 407 28000 40000"
	${ADDPROP} ${STAGING_PATH}/${SKY130A} sky130_fd_io sky130_fd_io_top_xres4v2 \
		"FIXED_BBOX 0 407 15000 40000"

install: install-local

install-local: install-local-a

install-local-a:
	echo "Starting SKY130 PDK migration on "`date` > ${SKY130A}_migrate.log
	${INSTALL} -source ${STAGING_PATH}/${SKY130A} \
		-target ${LOCAL_PATH}/${SKY130A} \
		-link_from ${LINK_TARGETS} |& tee -a ${SKY130A}_migrate.log
	echo "Ended SKY130 PDK migration on "`date` >> ${SKY130A}_migrate.log

install-dist: install-dist-a

install-dist-a:
	echo "Starting SKY130 PDK migration on "`date` > ${SKY130A}_migrate.log
	${INSTALL} -source ${STAGING_PATH}/${SKY130A} \
		-target ${DIST_PATH}/${SKY130A} \
		-local ${LOCAL_PATH}/${SKY130A} \
		-link_from ${DIST_LINK_TARGETS} |& tee -a ${SKY130A}_migrate.log
	echo "Ended SKY130 PDK migration on "`date` >> ${SKY130A}_migrate.log

clean: clean-a

clean-a:
	${STAGE} -target ${STAGING_PATH}/${SKY130A} -clean

veryclean: veryclean-a

veryclean-a: clean-a
	${RM} ${SKY130A}_install.log
	${RM} ${SKY130A}_migrate.log
