# $FreeBSD$

PORTNAME=		sensu-go
DISTVERSION=	5.11.1
CATEGORIES=		sysutils

MAINTAINER=		jp+ports@supplntr.io
COMMENT=		Installs sensu-go components

LICENSE=		APACHE20

ONLY_FOR_ARCHS=		amd64 i386
BUILD_DEPENDS=		bash:shells/bash

USES=			go
USE_GITHUB=		yes
GH_PROJECT=		sensu-go
GH_ACCOUNT=		sensu

GO_PKGNAME=		github.com/${GH_ACCOUNT}/${GH_PROJECT}

OPTIONS_DEFINE=		SENSU_AGENT SENSU_CLI SENSU_BACKEND
OPTIONS_DEFAULT=	SENSU_AGENT
OPTIONS_SUB=		yes

SENSU_AGENT_DESC=	Install sensu-agent daemon
SENSU_AGENT_VARS=	GO_TARGETS+=agent GO_BIN_TARGETS+=sensu-agent
SENSU_AGENT_PLIST_FILES=	bin/sensu-agent \
				etc/rc.d/sensu-agent \
				etc/sensu-agent.yml.example
USE_RC_SUBR+=	sensu-agent

SENSU_CLI_DESC=		Install sensuctl binary
SENSU_CLI_VARS=		GO_TARGETS+=cli GO_BIN_TARGETS+=sensuctl
SENSU_CLI_PLIST_FILES=	bin/sensuctl

SENSU_BACKEND_DESC=	Install sensu-backend daemon (unsupported)
SENSU_BACKEND_VARS=	GO_TARGETS+=backend GO_BIN_TARGETS+=sensu-backend
SENSU_BACKEND_PLIST_FILES=	bin/sensu-backend \
				etc/rc.d/sensu-backend \
				etc/sensu-backend.yml.example
SENSU_BACKEND_BROKEN=	Currently not tested under FreeBSD

.include <bsd.port.options.mk>

.if ${PORT_OPTIONS:MSENSU_BACKEND}
USE_RC_SUBR+=	sensu-backend
.endif

do-build:
.for GO_TARGET in ${GO_TARGETS}
	@(cd ${GO_WRKSRC}; ${SETENV} ${MAKE_ENV} ${GO_ENV} ./build.sh build_${GO_TARGET})
.endfor

do-install:
.for GO_BIN_TARGET in ${GO_BIN_TARGETS}
	${INSTALL_PROGRAM} ${GO_WRKSRC}/target/freebsd-${ARCH}/${GO_BIN_TARGET} ${STAGEDIR}${PREFIX}/bin
.endfor

.if ${PORT_OPTIONS:MSENSU_AGENT}
	${INSTALL_DATA} ${PATCHDIR}/sensu-agent.yml.example \
		${STAGEDIR}${PREFIX}/etc/sensu-agent.yml.example
.endif

.if ${PORT_OPTIONS:MSENSU_BACKEND}
	${INSTALL_DATA} ${PATCHDIR}/sensu-backend.yml.example \
		${STAGEDIR}${PREFIX}/etc/sensu-backend.yml.example
.endif

.include <bsd.port.mk>
