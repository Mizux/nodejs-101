PROJECT := nodejs_101
BRANCH := $(shell git rev-parse --abbrev-ref HEAD)
SHA1 := $(shell git rev-parse --verify HEAD)
HOST_PORT := 8080

# General commands
.PHONY: help
BOLD=\e[1m
RESET=\e[0m

help:
	@echo -e "${BOLD}SYNOPSIS${RESET}"
	@echo -e "\tmake <target> [NOCACHE=1]"
	@echo
	@echo -e "${BOLD}DESCRIPTION${RESET}"
	@echo -e "\ttest build inside docker containers so we can test against various major distro setup."
	@echo
	@echo -e "${BOLD}MAKE TARGETS${RESET}"
	@echo -e "\t${BOLD}help${RESET}: display this help and exit."
	@echo
	@echo -e "\t${BOLD}docker${RESET}: build all docker devel images."
	@echo -e "\t${BOLD}docker_<distro>${RESET}: build a docker devel image for the specified distro."
	@echo -e "\t${BOLD}serve_<distro>${RESET}: run a container of the distro image specified."
	@echo -e "\t${BOLD}bash_<distro>${RESET}: run a container of the distro image specified opening a shell for debug purpose."
	@echo
	@echo -e "\t${BOLD}test${RESET}: ${BOLD}test${RESET} using all distro image containers."
	@echo -e "\t${BOLD}test_<distro>${RESET}: ${BOLD}test${RESET} using the distro image container specified."
	@echo
	@echo -e "\t${BOLD}build${RESET}: ${BOLD}build ${RESET} using all distro image containers."
	@echo -e "\t${BOLD}build_<distro>${RESET}: ${BOLD}build${RESET} using the distro image container specified."
	@echo
	@echo -e "\t${BOLD}clean${RESET}: Remove log files and docker image for all distros."
	@echo -e "\t${BOLD}clean_<distro>${RESET}: Remove log files and docker image for the distro specified."
	@echo
	@echo -e "\t${BOLD}<distro>${RESET}:"
	@echo -e "\t\t${BOLD}node${RESET} (latest node:alpine container)"
	@echo -e "\t\t${BOLD}alpine${RESET} (latest)"
	@echo -e "\t\t${BOLD}archlinux${RESET} (latest)"
	@echo -e "\t\t${BOLD}centos${RESET} (latest)"
	@echo -e "\t\t${BOLD}debian${RESET} (latest)"
	@echo -e "\t\t${BOLD}fedora${RESET} (latest)"
	@echo -e "\t\t${BOLD}opensuse${RESET} (tumbleweed)"
	@echo -e "\t\t${BOLD}ubuntu${RESET} (latest)"
	@echo -e "\te.g. 'make test_ubuntu'"
	@echo
	@echo -e "\t${BOLD}NOCACHE=1${RESET}: use 'docker build --no-cache' when building container (default use cache)."
	@echo
	@echo -e "${BOLD}NOTES${RESET}"
	@echo -e "\tAll generated code will be located in the cache/ folder, use clean to remove it."
	@echo
	@echo -e "branch: $(BRANCH)"
	@echo -e "sha1: $(SHA1)"

# Need to add cmd_distro to PHONY otherwise target are ignored since they do not
# contain recipe (using FORCE do not work here)
.PHONY: all
all: build

# Delete all implicit rules to speed up makefile
.SUFFIXES:
# Remove some rules from gmake that .SUFFIXES does not remove.
SUFFIXES =
# Keep all intermediate files
# ToDo: try to remove it later
.SECONDARY:

# Docker image name prefix.
IMAGE := ${PROJECT}

ifdef NOCACHE
DOCKER_BUILD_CMD := docker build --no-cache
else
DOCKER_BUILD_CMD := docker build
endif

DOCKER_RUN_CMD := docker run -p ${HOST_PORT}:8080 --rm --init

DOCKER_SERVE_CMD := docker run -p ${HOST_PORT}:8080 --rm --init -it
DOCKER_TEST_CMD := docker run -p ${HOST_PORT}:8080 --rm --init -d

# Use host user id instead of default root:root
UID := $(shell id -u)
GID := $(shell id -g)
DOCKER_DEVEL_CMD := docker run --rm -it --init
#DOCKER_DEVEL_CMD := docker run --rm -it --init -v ${PWD}:/project -w /project --user ${UID}:${GID}

DOCKER_INSTALL_CMD := docker run --rm -it --init -e "NODE_ENV=production" -v ${PWD}/sample:/project -w /project

# Currently supported distro
DISTROS = node alpine archlinux centos debian fedora opensuse ubuntu

# $* stem
# $< first prerequist
# $@ target name

# DOCKER: Create devel image
targets = $(addprefix docker_, $(DISTROS))
.PHONY: docker $(targets)
docker: $(targets)
$(targets): docker_%: cache/%/docker_devel.tar
cache/%/docker_devel.tar: docker/%/Dockerfile ../package.json
	mkdir -p cache/$*
	@docker image rm -f ${IMAGE}_$*:devel 2>/dev/null
	@rm -f $@
	${DOCKER_BUILD_CMD} -t ${IMAGE}_$*:devel -f $< ..
	docker save ${IMAGE}_$*:devel -o $@

# DOCKER BASH: Inspect devel image (debug)
targets = $(addprefix bash_, $(DISTROS))
.PHONY: $(targets)
$(targets): bash_%: cache/%/docker_devel.tar
	${DOCKER_RUN_CMD} -it ${IMAGE}_$*:devel /bin/sh

# DOCKER SERVE
targets = $(addprefix serve_, $(DISTROS))
.PHONY: $(targets)
$(targets): serve_%: cache/%/docker_devel.tar
	-docker stop $*_server 2>/dev/null | true
	${DOCKER_RUN_CMD} -d --name $*_server ${IMAGE}_$*:devel

# TEST
targets = $(addprefix test_, $(DISTROS))
.PHONY: test $(targets)
test: $(targets)
$(targets): test_%: serve_%
	@until $$(curl --output /dev/null --silent --head --fail localhost:8080); do echo '.'; sleep 1; done
	@curl -i localhost:8080
	@docker stop $*_server 1>/dev/null

# BUILD
targets = $(addprefix build_, $(DISTROS))
.PHONY: build $(targets)
build: $(targets)
$(targets): build_%: cache/%/build.log
cache/%/build.log: cache/%/docker_devel.tar
	${DOCKER_DEVEL_CMD} ${IMAGE}_$*:devel /bin/sh -c \
		"yarn build"
	@date > $@

# CLEAN
targets = $(addprefix clean_, $(DISTROS))
.PHONY: clean $(targets)
$(targets): clean_%:
	-docker image rm -f ${IMAGE}_$*:devel 2>/dev/null
	-rm -f cache/$*/docker_devel.tar
	-rm -f cache/$*/build.log
	-rmdir cache/$*
clean: $(targets)
	docker container prune -f
	docker image prune -f
	rmdir cache

# DISTCLEAN
targets = $(addprefix distclean_, $(DISTROS))
.PHONY: distclean $(targets)
$(targets): distclean_%: clean_%
	-docker image rm -f ${IMAGE}_$*:install 2>/dev/null
	-rm -f cache/$*/docker_install.tar
distclean: $(targets)
