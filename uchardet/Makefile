# Set the libc to be used with the LIBC argument
# possible values newlib, clib2, clib4
# make LIBC=clib4
# 

LIB_VERSION=0.0.8

PKGNAME=uchardet-v${LIB_VERSION}

include ../_common/help.mk

.PHONY: download
download:
	@echo "${CCPINK}${CCBOLD}\n-> Download archive ${PKGNAME}\n${CCEND}"
		@curl -fsSL "https://gitlab.freedesktop.org/uchardet/uchardet/-/archive/v${LIB_VERSION}/uchardet-v${LIB_VERSION}.tar.gz" -o ${PKGNAME}.tar.gz && \
		echo "${CCPINK}${CCBOLD}\n-> Extract archive\n${CCEND}" && \
		tar xzpf ${PKGNAME}.tar.gz

#####################################################################
##@ Build

.PHONY: init
init: download patch
	@cd ${PKGNAME} && \
	mkdir build

.PHONY: patch
patch:
	@echo "${CCPINK}${CCBOLD}\n-> Apply the patches\n${CCEND}"
	@patch -s -p0 < ./patch-0.0.8.diff

.PHONY: build
build: # Build with specified LIBC=(newlib|clib2|clib4)
	@echo "${CCPINK}${CCBOLD}\n-> Building using the ${LIBC}...\n${CCEND}"
	@cd ${PKGNAME}/build && \
	cmake .. -DCMAKE_BUILD_TYPE=Release \
		-DCMAKE_CROSSCOMPILING=ON \
		-DCMAKE_TOOLCHAIN_FILE=$(realpath morphos.cmake) \
		-DCMAKE_C_FLAGS="-noixemul -O2 -fPIC" \
		-DCMAKE_MODULE_LINKER_FLAGS="-noixemul" \
		-DCMAKE_EXE_LINKER_FLAGS="-noixemul" \
		-DBUILD_SHARED_LIBS=OFF \
		-DBUILD_STATIC=ON \
		-DBUILD_BINARY=ON \
		-DCHECK_SSE2=OFF \
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
	@rm -rf ./${PKGNAME}.tar.gz ./${PKGNAME} ./release

#####################################################################
##@ Release

.PHONY: release
release: release-init build package archive ## Build and create the release

.PHONY: release-init
release-init: ## Prepare the release folders
	@echo "${CCPINK}${CCBOLD}\n-> Clear previous release folders...\n${CCEND}"
	@rm -rf ./release

	@echo "${CCPINK}${CCBOLD}\n-> Create release folders...\n${CCEND}"
	@mkdir -p ./release/${PKGNAME}

	@cp ${PKGNAME}/README* ./release/${PKGNAME}/
	@cp ${PKGNAME}/COPYING* ./release/${PKGNAME}/

.PHONY: package
package:
	@echo "${CCPINK}${CCBOLD}\n-> Packaging library files...\n${CCEND}"
	@mkdir -p ./release/${PKGNAME}/SDK/lib/pkgconfig \
			./release/${PKGNAME}/SDK/include \
			./release/${PKGNAME}/SDK/bin

	@\cp ./${PKGNAME}/src/uchardet.h ./release/${PKGNAME}/SDK/include/
	@cp ./${PKGNAME}/build/src/libuchardet.a ./release/${PKGNAME}/SDK/lib/
	@cp ./${PKGNAME}/build/uchardet.pc ./release/${PKGNAME}/SDK/lib/pkgconfig/
	@cp ./${PKGNAME}/build/src/tools/uchardet ./release/${PKGNAME}/SDK/bin/

.PHONY: archive
archive: ## Create the release archive
	@echo "${CCPINK}${CCBOLD}\n-> Creating the lha release file...\n${CCEND}"
	@rm -f ./${PKGNAME}.lha
	@cd ./release && lha aq ../${PKGNAME}.lha ./${PKGNAME}/