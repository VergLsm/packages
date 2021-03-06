include $(TOPDIR)/rules.mk

PKG_NAME:=containerd
PKG_VERSION:=1.2.10
PKG_RELEASE:=2
PKG_LICENSE:=Apache-2.0
PKG_LICENSE_FILES:=LICENSE

PKG_SOURCE:=$(PKG_NAME)-$(PKG_VERSION).tar.gz
PKG_SOURCE_URL:=https://codeload.github.com/containerd/containerd/tar.gz/v${PKG_VERSION}?
PKG_HASH:=a0965e1492fca558629826f1aa89a9675de3d451cec67540400b30c0bf6ac387
PKG_SOURCE_VERSION:=b34a5c8af56e510852c35414db4c1f4fa6172339

PKG_MAINTAINER:=Gerard Ryan <G.M0N3Y.2503@gmail.com>

PKG_BUILD_DEPENDS:=golang/host
PKG_BUILD_PARALLEL:=1
PKG_INSTALL:=1

GO_PKG:=github.com/containerd/containerd

include $(INCLUDE_DIR)/package.mk
include ../../lang/golang/golang-package.mk

define Package/containerd/config
config CONTAINERD_SECCOMP
	depends on PACKAGE_containerd
	bool "Enable support for seccomp in containerd"
	default DOCKER_SECCOMP
	select KERNEL_SECCOMP
	select PACKAGE_libseccomp
	help
	  Build containerd with support for seccomp filters.
	  Select libseccomp which also pulls-in the needed kernel features.
endef

define Package/containerd
  SECTION:=utils
  CATEGORY:=Utilities
  TITLE:=containerd container runtime
  URL:=https://containerd.io/
  DEPENDS:=$(GO_ARCH_DEPENDS) @(aarch64||arm||x86_64) +btrfs-progs +runc +CONTAINERD_SECCOMP:libseccomp
  MENU:=1
endef

define Package/containerd/description
An industry-standard container runtime with an emphasis on simplicity, robustness and portability
endef

GO_PKG_INSTALL_ALL:=1
MAKE_PATH:=$(GO_PKG_WORK_DIR_NAME)/build/src/$(GO_PKG)
MAKE_VARS += \
	GOPATH=$(GO_PKG_BUILD_DIR) \
	GOCACHE=$(GO_PKG_CACHE_DIR) \
	GOTMPDIR=$(GO_PKG_TMP_DIR) \
	GOROOT_FINAL=$(GO_TARGET_ROOT) \
	CC=$(TARGET_CC) \
	CXX=$(TARGET_CXX) \
	$(call GoPackage/Environment)
MAKE_FLAGS += \
	DESTDIR="$(PKG_INSTALL_DIR)" \
	VERSION=$(PKG_VERSION) \
	REVISION=$(PKG_SOURCE_VERSION)

ifeq ($(CONFIG_CONTAINERD_SECCOMP),y)
MAKE_FLAGS += BUILDTAGS='seccomp'
else
MAKE_FLAGS += BUILDTAGS=''
endif

define Build/Compile
	$(call Build/Compile/Default)
endef

# Avoid installing binaries
define Build/InstallDev
	$(call Build/Compile/Default,clean)
	$(call GoPackage/Build/InstallDev,$(1))
endef

define Package/containerd/install
	$(INSTALL_DIR) $(1)/usr/bin/
	$(INSTALL_BIN) $(PKG_INSTALL_DIR)/bin/{ctr,containerd,containerd-stress,containerd-shim} $(1)/usr/bin/
endef

$(eval $(call BuildPackage,containerd))
