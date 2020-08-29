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
	@echo -e "\t${BOLD}<stage>${RESET}: build all <stage> docker images."
	@echo -e "\t${BOLD}<distro>_<stage>${RESET}: build the <stage> docker image for a specific distro."
	@echo -e "\t${BOLD}save_<stage>${RESET}: Save all <stage> docker images."
	@echo -e "\t${BOLD}save_<distro>_<stage>${RESET}: Save the <stage> docker image for a specific distro."
	@echo -e "\t${BOLD}sh_<distro>_<stage>${RESET}: run a container using the <stage> docker image (debug purpose)."
	@echo
	@echo -e "\t${BOLD}<stage>${RESET}:"
	@echo -e "\t\t${BOLD}env${RESET}"
	@echo -e "\t\t${BOLD}devel${RESET}"
	@echo -e "\t\t${BOLD}build${RESET}"
	@echo
	@echo -e "\t${BOLD}<step>${RESET}:"
	@echo -e "\t\t${BOLD}serve${RESET}"
	@echo -e "\t\t${BOLD}test${RESET}"
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
	@echo -e "\te.g. 'make ubuntu_test'"
	@echo
	@echo -e "\t${BOLD}clean${RESET}: Remove log files and docker image for all distros."
	@echo -e "\t${BOLD}clean_<distro>${RESET}: Remove log files and docker image for the distro specified."
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
MAKEFLAGS += --no-builtin-rules
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

STAGES = env devel build
define make-stage-target
#$$(info STAGE: $1)
#$$(info Create targets: $1 $(addsuffix _$1, $(DISTROS)).)
targets_$1 = $(addsuffix _$1, $(DISTROS))
.PHONY: $1 $(targets_$1)
$1: $$(targets_$1)
$$(targets_$1): %_$1: docker/%/Dockerfile
	#@docker image rm -f ${IMAGE}:$$*_$1 2>/dev/null
	${DOCKER_BUILD_CMD} --target=$1 --tag ${IMAGE}:$$*_$1 -f $$< ..

#$$(info Create targets: save_$1 $(addprefix save_, $(addsuffix _$1, $(DISTROS))) (debug).)
save_targets_$1 = $(addprefix save_, $(addsuffix _$1, $(DISTROS)))
.PHONY: save_$1 $(save_targets_$1)
save_$1: $$(save_targets_$1)
$$(save_targets_$1): save_%_$1: cache/%/docker_$1.tar
cache/%/docker_$1.tar: %_$1
	@rm -f $$@
	mkdir -p cache/$$*
	docker save ${IMAGE}:$$*_$1 -o $$@

#$$(info Create targets: $(addprefix sh_, $(addsuffix _$1, $(DISTROS))) (debug).)
sh_targets_$1 = $(addprefix sh_, $(addsuffix _$1, $(DISTROS)))
.PHONY: $(sh_targets_$1)
$$(sh_targets_$1): sh_%_$1: %_$1
	${DOCKER_RUN_CMD} -it --name ${IMAGE}_$$*_$1 ${IMAGE}:$$*_$1

#$$(info Create targets: $(addprefix clean_, $(addsuffix _$1, $(DISTROS))).)
clean_targets_$1 = $(addprefix clean_, $(addsuffix _$1, $(DISTROS)))
.PHONY: clean_$1 $(clean_targets_$1)
clean_$1: $$(clean_targets_$1)
$$(clean_targets_$1): clean_%_$1:
	docker image rm -f ${IMAGE}:$$*_$1 2>/dev/null
	rm -f cache/$$*/docker_$1.tar
endef

$(foreach stage,$(STAGES),$(eval $(call make-stage-target,$(stage))))

# SERVE
targets = $(addsuffix _serve, $(DISTROS))
.PHONY: $(targets)
$(targets): %_serve: %_build
	-docker stop $*_server 2>/dev/null | true
	${DOCKER_RUN_CMD} -d --name $*_server ${IMAGE}:$*_build

# TEST
targets = $(addsuffix _test, $(DISTROS))
.PHONY: test $(targets)
test: $(targets)
$(targets): %_test: %_serve
	@timeout 10 sh -c \
 'until $$(curl --output /dev/null --silent --head --fail localhost:${HOST_PORT}); do \
   echo "."; \
   sleep 1; \
 done'
	@curl -i localhost:${HOST_PORT}
	@docker stop $*_server 1>/dev/null

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
