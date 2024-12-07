# Set the libc to be used with the LIBC argument
# possible values newlib, clib2, clib4
# make LIBC=clib4
# 

LIB_VERSION=5.4.7

PKGNAME=lua-${LIB_VERSION}

include ../_common/help.mk

.PHONY: download
download:
	@echo "${CCPINK}${CCBOLD}\n-> Download archive ${PKGNAME}\n${CCEND}"
	@curl -fsSLk "https://www.lua.org/ftp/${PKGNAME}.tar.gz" -o ${PKGNAME}.tar.gz && \
		echo "${CCPINK}${CCBOLD}\n-> Extract archive\n${CCEND}" && \
		tar xzpf ${PKGNAME}.tar.gz

#####################################################################
##@ Build

.PHONY: init
init: download

.PHONY: build
build: # Build the library
	@echo "${CCPINK}${CCBOLD}\n-> Building ${LIBC} based libraries...\n${CCEND}"
	@make -C ${PKGNAME}/src generic \
		CC="ppc-morphos-gcc -std=gnu99 -noixemul" \
		-j$(shell nproc)

#####################################################################
##@ Clean

.PHONY: clean
clean: ## Clean the latest build
	@echo "${CCPINK}${CCBOLD}\n-> Clean the build folder\n${CCEND}"
	@make -C ${PKGNAME}/src clean

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
	@mkdir -p ./SDK/include/lua54
	@mkdir -p ./SDK/Documentation/lua54
	@cp -r ${PKGNAME}/doc ./SDK/Documentation/lua54/
	@cp ${PKGNAME}/README* ./SDK/Documentation/lua54/

.PHONY: package
package:
	@echo "${CCPINK}${CCBOLD}\n-> Packaging ${LIBC} based libraries...\n${CCEND}"
	@mkdir -p ./SDK/lib
	@mkdir -p ./SDK/bin
	@cp ./${PKGNAME}/src/lauxlib.h ./${PKGNAME}/src/lua.h \
		./${PKGNAME}/src/lua.hpp \
		./${PKGNAME}/src/luaconf.h ./${PKGNAME}/src/lualib.h \
		./SDK/include/lua54/
	@cp ./${PKGNAME}/src/liblua.a ./SDK/lib/liblua54.a
	@cp ./${PKGNAME}/src/lua ./${PKGNAME}/src/luac \
		./SDK/bin/

.PHONY: archive
archive: ## Create the release archive
	@echo "${CCPINK}${CCBOLD}\n-> Creating the lha release file...\n${CCEND}"
	@rm -f ./${PKGNAME}.lha
	@lha aq ./${PKGNAME}.lha ./SDK/
