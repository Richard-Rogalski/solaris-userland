#
# CDDL HEADER START
#
# The contents of this file are subject to the terms of the
# Common Development and Distribution License (the "License").
# You may not use this file except in compliance with the License.
#
# You can obtain a copy of the license at usr/src/OPENSOLARIS.LICENSE
# or http://www.opensolaris.org/os/licensing.
# See the License for the specific language governing permissions
# and limitations under the License.
#
# When distributing Covered Code, include this CDDL HEADER in each
# file and include the License file at usr/src/OPENSOLARIS.LICENSE.
# If applicable, add the following below this CDDL HEADER, with the
# fields enclosed by brackets "[]" replaced with your own identifying
# information: Portions Copyright [yyyy] [name of copyright owner]
#
# CDDL HEADER END
#

#
# Copyright (c) 2013, 2022, Oracle and/or its affiliates.
#

include ../../../make-rules/shared-macros.mk

COMPONENT_NAME=		Django
COMPONENT_VERSION=	3.2.16
COMPONENT_SRC=		$(COMPONENT_NAME)-$(COMPONENT_VERSION)
COMPONENT_ARCHIVE=	$(COMPONENT_SRC).tar.gz
COMPONENT_ARCHIVE_HASH=	\
    sha256:9a8103d9a4d1ef168bc9bf81b09327aa2577faa68d8fbef46dfd0a60489c3dcc
COMPONENT_ARCHIVE_URL=	$(call pypi_url)
COMPONENT_PROJECT_URL=	http://www.djangoproject.com/
COMPONENT_BUGDB=	python-mod/django
COMPONENT_ANITYA_ID=	3828
COMPONENT_BAID=		130921


COMPONENT_NAME_22=	$(COMPONENT_NAME)
COMPONENT_VERSION_22=	2.2.28
COMPONENT_SRC_22=	$(COMPONENT_NAME)-$(COMPONENT_VERSION_22)
COMPONENT_ARCHIVE_22=	$(COMPONENT_SRC_22).tar.gz
COMPONENT_ARCHIVE_HASH_22= \
    sha256:378d9a3b524f2243fab24aa8f6427ce06f9b3a3ca43a7f9069af541b4373e47b
COMPONENT_ARCHIVE_URL_22=	$(call pypi_url,22)
COMPONENT_ANITYA_ID_22=	155462
COMPONENT_BAID_22=	120547


PYTHON_VERSIONS=	$(PYTHON3_VERSIONS)

# As per PSARC/2021/051, different LTS version of Django is to
# be delivered with each Python runtime.

include $(WS_MAKE_RULES)/prep.mk
include $(WS_MAKE_RULES)/setup.py.mk
include $(WS_MAKE_RULES)/ips.mk

# Convenience variables
TARGET_37 = $(BUILD_DIR)/$(MACH)-3.7/
SOURCE_DIR_22 = $(COMPONENT_DIR)/$(COMPONENT_SRC_22)

# Targets updated for older version build
$(TARGET_37).built: SOURCE_DIR = $(SOURCE_DIR_22)

$(TARGET_37).installed: SOURCE_DIR = $(SOURCE_DIR_22)

$(TARGET_37).tested-and-compared: COMPONENT_TEST_DIR = $(SOURCE_DIR_22)/tests

$(MANIFEST_BASE)-django-37.mogrified: COMPONENT_VERSION = $(COMPONENT_VERSION_22)
$(MANIFEST_BASE)-django-37.mogrified: COMPONENT_BAID = $(COMPONENT_BAID_22)

# Manifest convenience variables.
$(MANIFEST_BASE)-django-37.mogrified: 32_ONLY = \#
$(MANIFEST_BASE)-django-39.mogrified: 22_ONLY = \#

PKG_MACROS += 22_ONLY="$(22_ONLY)"
PKG_MACROS += 32_ONLY="$(32_ONLY)"

# Meta package must have versions of older runtimes fixed
FIX_MANIFEST_VERSIONS:
	echo "<transform depend fmri=(.*)django-37@(.*) \
		-> edit fmri $(COMPONENT_VERSION) $(COMPONENT_VERSION_22)>" | \
	$(PKGMOGRIFY) /dev/fd/0 $(MANIFEST_BASE)-django.mogrified \
		> $(MANIFEST_BASE)-django.mogrified-fixed ;
	$(MV) $(MANIFEST_BASE)-django.mogrified-fixed \
		$(MANIFEST_BASE)-django.mogrified

$(MANIFEST_BASE)-django.mangled: FIX_MANIFEST_VERSIONS

# Replace "#!/usr/bin/env ..." shebang lines with properly versioned ones.
COMPONENT_POST_BUILD_ACTION = \
    /usr/bin/find $(@D)/lib/django -name *.py \
	-exec $(GSED) -i -e 's/env python/python$(PYTHON_VERSION)/' "{}" \; ;

COMPONENT_POST_INSTALL_ACTION = \
    (cd $(PROTO_DIR)/usr/bin ; \
     $(MV) -f django-admin django-admin-$(PYTHON_VERSION)) ;

# add 1 extra transform to get rid of the facet created on
# locale/__init__.py
PUBLISH_TRANSFORMS +=	$(COMPONENT_DIR)/django-locale-transform

TEST_PYTHONPATH=$(SOURCE_DIR)
$(TARGET_37).tested-and-compared: TEST_PYTHONPATH=$(SOURCE_DIR_22)

COMPONENT_TEST_DIR =	$(SOURCE_DIR)/tests
COMPONENT_TEST_ARGS +=	--settings test_sqlite
# reset the test environment to only contain PATH, PYTHONPATH, and LC_ALL
# (because many tests need to be able to access non-ASCII codepoints).
COMPONENT_TEST_ENV =	PYTHONPATH=$(TEST_PYTHONPATH)
COMPONENT_TEST_ENV +=	LC_ALL=en_US.UTF-8

# Prevent HTTP 502: cannotconnect errors when unit testing on live server
COMPONENT_TEST_ENV += http_proxy=
COMPONENT_TEST_ENV += https_proxy=

ASLR_MODE = $(ASLR_NOT_APPLICABLE)

# common targets
build:		$(BUILD_NO_ARCH)

install:	$(INSTALL_NO_ARCH)

test:		$(TEST_NO_ARCH)

COMPONENT_TEST_ARGS += --parallel=1
COMPONENT_TEST_ARGS += --verbosity=3

# To run single test you can just add it's name like here:
# COMPONENT_TEST_ARGS += admin_scripts.tests.StartProject
COMPONENT_TEST_MASTER = $(COMPONENT_TEST_RESULTS_DIR)/results-all.master

COMPONENT_TEST_TRANSFORMS += \
	` : Disable normal test output ` \
	'-n ' \
	'-e "/^ERROR/p"' \
	'-e "/^FAIL/p"' \
	'-e "s/^Ran .* tests in.*/Tests were executed/g"' \
	'-e "/Tests were executed/p"'

system-test:	$(SYSTEM_TESTS_NOT_IMPLEMENTED)

REQUIRED_PACKAGES += $(PYVS:%=library/python/pytz-%)
REQUIRED_PACKAGES += $(PYVS:%=library/python/sqlparse-%)
REQUIRED_PACKAGES += library/python/asgiref-39
REQUIRED_PACKAGES += library/python/mock
REQUIRED_PACKAGES += library/python/pillow
REQUIRED_PACKAGES += library/python/pyyaml
REQUIRED_PACKAGES += library/python/setuptools
REQUIRED_PACKAGES += text/gnu-gettext
