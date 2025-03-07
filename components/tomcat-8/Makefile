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
# Copyright (c) 2011, 2022, Oracle and/or its affiliates.
#
BUILD_BITS= NO_ARCH
include ../../make-rules/shared-macros.mk

COMPONENT_NAME=		tomcat
COMPONENT_VERSION=	8.5.82
COMPONENT_BAID=		128515
COMPONENT_PROJECT_URL=	https://tomcat.apache.org/
COMPONENT_SRC_NAME=	apache-tomcat
COMPONENT_SRC=		$(COMPONENT_SRC_NAME)-$(COMPONENT_VERSION)-src
COMPONENT_ARCHIVE_HASH=	\
    sha256:f2046c15f6f1c0bd55ddfdcf7f5eef0ca437b693d6caff0dae2ad888bd42b532
COMPONENT_ARCHIVE_URL=	https://archive.apache.org/dist/tomcat/tomcat-8/v$(COMPONENT_VERSION)/src/$(COMPONENT_ARCHIVE)
COMPONENT_SIG_URL=	$(COMPONENT_ARCHIVE_URL).asc
COMPONENT_ANITYA_ID=	4987

# Tomcat subcomponents
COMPONENT_ARCHIVE_1= ecjsrc-4.6.3.jar
COMPONENT_ARCHIVE_HASH_1= \
    sha256:f4be12aeaa50d54e883e75ee524c69ec9ed6a7c74bce4ef70d4adaff9a7e8e85
COMPONENT_ARCHIVE_URL_1= http://archive.eclipse.org/eclipse/downloads/drops4/R-4.6.3-201703010400/$(COMPONENT_ARCHIVE_1)

BUILD_STYLE= ant
TEST_TARGET= $(SKIP_TEST)
SYSTEM_TEST_TARGET= $(SKIP_TEST)
include $(WS_MAKE_RULES)/common.mk

COMPONENT_BUILD_ARGS=-Dbase.path=$(BUILD_DIR_NO_ARCH) -Dtomcat.build=$(PROTO_DIR)

# Tomcat tests doesn't like cloning.
CLONEY=echo
COMPONENT_PRE_BUILD_ACTION= \
	$(CP) -r $(SOURCE_DIR)/* $(@D);

# Build ECJ jar from sources.
COMPONENT_PRE_BUILD_ACTION+= \
	($(MKDIR) $(@D)/ECJ/scripts/binary/META-INF; cd $(@D)/ECJ ; \
	    $(JAVA_HOME)/bin/jar xf $(COMPONENT_DIR)/$(COMPONENT_ARCHIVE_1); \
	    $(CP) META-INF/MANIFEST.MF scripts/binary/META-INF/; \
	    JAVA_HOME="$(JAVA_HOME)" LC_ALL="en_US.UTF-8" $(ANT) )

# Create configuration backup archive for custom user's deployments.
COMPONENT_POST_BUILD_ACTION= \
	(cd $(PROTO_DIR) ; $(RM) conf/CONF.tar.gz ; \
	    gtar cfz conf/CONF.tar.gz conf/* )

# Used by this Makefile
REQUIRED_PACKAGES += archiver/gnu-tar

REQUIRED_PACKAGES += developer/java/junit
REQUIRED_PACKAGES += runtime/java/jre-8
REQUIRED_PACKAGES += shell/ksh93
REQUIRED_PACKAGES += system/core-os
REQUIRED_PACKAGES += system/network
