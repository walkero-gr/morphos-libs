# Set the libc to be used with the LIBC argument
# possible values newlib, clib2, clib4
# make LIBC=clib4
# 

LIB_VERSION=10.44

PKGNAME=pcre2-${LIB_VERSION}

include ../_common/help.mk

.PHONY: download
download:
	@echo "${CCPINK}${CCBOLD}\n-> Download archive ${PKGNAME}\n${CCEND}"
	@curl -fsSL "https://github.com/PCRE2Project/pcre2/releases/download/pcre2-${LIB_VERSION}/pcre2-${LIB_VERSION}.tar.gz" -o ${PKGNAME}.tar.gz && \
	echo "-> Extract archive" && \
	tar xzpf ${PKGNAME}.tar.gz

#####################################################################
##@ Build

.PHONY: init
init: download patch
	@cd ${PKGNAME} && \
	mkdir build

patch:
	@echo "-> Apply the patches"
	@patch -s -p0 < ./patch-1.diff

.PHONY: build
build: # Build the library
	@echo "${CCPINK}${CCBOLD}\n-> Building ${LIBC} based libraries...\n${CCEND}"
	@cd ${PKGNAME}/build && \
	cmake .. -DCMAKE_BUILD_TYPE=Release \
		-DCMAKE_CROSSCOMPILING=ON \
		-DCMAKE_TOOLCHAIN_FILE=$(realpath morphos.cmake) \
		-DCMAKE_C_FLAGS="-noixemul -O3 -DHAVE_CONFIG_H -DPCRE2_CODE_UNIT_WIDTH=8" \
		-DBUILD_SHARED_LIBS=OFF \
		-DPCRE2GREP_SUPPORT_CALLOUT=OFF \
		--install-prefix=${SDK_PATH}/local/${LIBC} \
	&& \
	make -j$(shell nproc)

#####################################################################
##@ Clean

.PHONY: clean
clean: ## Clean the latest build
	@echo "${CCPINK}${CCBOLD}\n-> Clean the build folder\n${CCEND}"
	@rm -rf ${PKGNAME}/build/*

.PHONY: cleanall
cleanall: ## Clean the downloaded files and folders
	@echo "${CCPINK}${CCBOLD}\n-> Remove archive and folders\n${CCEND}"
	@rm -rf ./${PKGNAME}.tar.gz ./${PKGNAME} ./SDK

#####################################################################
##@ Release

.PHONY: release
release: release-init build package archive ## Build and create the release

.PHONY: release-init
release-init: ## Prepare the re folders
	@echo "${CCPINK}${CCBOLD}\n-> Clear previous release folders...\n${CCEND}"
	@rm -rf ./SDK
	@echo "${CCPINK}${CCBOLD}\n-> Create release folders...\n${CCEND}"
	@mkdir -p ./SDK/Documentation/pcre2
	@cp ${PKGNAME}/LICENCE ./SDK/Documentation/pcre2/
	@cp ${PKGNAME}/README* ./SDK/Documentation/pcre2/
	@cp ${PKGNAME}/COPYING* ./SDK/Documentation/pcre2/
	@cp ${PKGNAME}/ChangeLog* ./SDK/Documentation/pcre2/
	@cp -R ${PKGNAME}/doc/* ./SDK/Documentation/pcre2/

.PHONY: package
package:
	@echo "${CCPINK}${CCBOLD}\n-> Packaging ${LIBC} based libraries...\n${CCEND}"
	@mkdir -p ./SDK/lib \
		./SDK/lib/pkgconfig \
		./SDK/bin \
		./SDK/include
	@cp ./${PKGNAME}/build/libpcre2-8.a ./${PKGNAME}/build/libpcre2-posix.a \
		 ./SDK/lib/
	@cp ./${PKGNAME}/build/pcre2*test ./${PKGNAME}/build/pcre2grep \
		./SDK/bin/
	@cp ./${PKGNAME}/build/libpcre2-8.pc ./SDK/lib/pkgconfig/
	@cp ./${PKGNAME}/build/pcre2.h ./SDK/include/

.PHONY: archive
archive: ## Create the release archive
	@echo "${CCPINK}${CCBOLD}\n-> Creating the lha release file...\n${CCEND}"
	@rm -f ./${PKGNAME}.lha
	@lha aq ./${PKGNAME}.lha ./SDK/
	@rm -f ./${PKGNAME}.lha
	@lha aq ./${PKGNAME}.lha ./SDK/
