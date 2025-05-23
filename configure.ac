#                                               -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.
#
# This file is part of the aMule Project.
#
# Copyright (c) 2003-2011 aMule Team ( admin@amule.org / http://www.amule.org )
#
# Any parts of this program derived from the xMule, lMule or eMule project,
# or contributed by third-party developers are copyrighted by their
# respective authors.
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 2 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301, USA
#

AC_INIT([aMule],[SVN],[admin@amule.org])
AM_INIT_AUTOMAKE
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES])

AC_PREREQ(2.62)

AC_CONFIG_SRCDIR([src/amule.cpp])
AC_CONFIG_HEADERS([config.h])
AC_CONFIG_MACRO_DIR([m4])
AM_MAINTAINER_MODE

dnl Check host system.
MULE_CHECK_SYSTEM

dnl Checks for programs.
dnl Workaround for these checks may alter CFLAGS and CXXFLAGS, especially if they were empty
MULE_BACKUP([CFLAGS])
MULE_BACKUP([CXXFLAGS])
MULE_BACKUP([BUILD_CFLAGS])
AC_PROG_CXX
AC_PROG_CXXCPP
AC_PROG_CC
AC_PROG_CPP
AS_IF([test "$cross_compiling" = yes], [AC_PROG_BUILD_CC], [BUILD_CC="$CC" ; BUILD_EXEEXT=$EXEEXT])
MULE_RESTORE([CFLAGS])
MULE_RESTORE([CXXFLAGS])
MULE_RESTORE([BUILD_CFLAGS])
AC_PROG_AWK
AC_PROG_EGREP
AC_PROG_MAKE_SET
AC_PROG_INSTALL
AC_PROG_RANLIB
AC_PROG_SED

dnl Find the right ranlib, even when cross-compiling
dnl And for some other tools for cross compilation
AC_CHECK_TOOL(RANLIB, ranlib)
AC_CHECK_TOOL(STRIP, strip)
AC_CHECK_TOOL(AR, ar)
AC_CHECK_TOOL(LD, ld)

dnl Selectable components
MULE_ARG_ENABLE([monolithic],	[yes],	[disable building of the monolithic aMule app], [MONOLITHIC])
MULE_ARG_ENABLE([amule-daemon],	[no],	[compile aMule daemon version], [AMULE_DAEMON])
MULE_ARG_ENABLE([amulecmd],	[no],	[compile aMule command line client], [COMPILE_CMD])
MULE_ARG_ENABLE([webserver],	[no],	[compile aMule WebServer], [WEB])
MULE_ARG_ENABLE([amule-gui],	[no],	[compile aMule remote GUI], [AMULE_GUI])
MULE_ARG_ENABLE([cas],		[no],	[compile C aMule Statistics], [CAS])
MULE_ARG_ENABLE([wxcas],	[no],	[compile aMule GUI Statistics], [WXCAS])
MULE_ARG_ENABLE([ed2k],		[yes],	[don't compile aMule ed2k links handler], [ED2K])
MULE_ARG_ENABLE([alc],		[no],	[compile aMuleLinkCreator GUI version], [ALC])
MULE_ARG_ENABLE([alcc],		[no],	[compile aMuleLinkCreator for console], [ALCC])
MULE_ARG_ENABLE([xas],		[no],	[install xas XChat2 plugin], [XAS])
MULE_ARG_ENABLE([fileview],	[no],	[compile aMule file viewer for console], [FILEVIEW])
MULE_ARG_ENABLE([plasmamule],	[no],	[compile aMule plasma applet and engine])

# Run all the tests with static linkage if we want a statically linked binary
MULE_ARG_ENABLE([static], [no], [produce a statically linked executable])
MULE_IF_ENABLED([static], [MULE_APPEND([LDFLAGS], [-static])])

KDE_CONFIG_OPTIONS
QT_CONFIG_OPTIONS

# Default is yes, because they're most likely compatible.
# However, this is only used when cross-compiling.
AC_ARG_WITH(
	[gnu-malloc],
	[AS_HELP_STRING([--without-gnu-malloc],
		[Don't assume that we use GNU libc compatible malloc/realloc when cross-compiling])],
	[gnumalloc=$withval], [gnumalloc=yes])


# Checking for libs before all other stuff to break
# configure as soon as possible in case of a missing one
# to avoid losing too much time

# Check for gd
MULE_IF_ENABLED([cas], [
	MULE_CHECK_GDLIB([2.0.0], [
		CAS_DEFS="-D__GD__"
		GDLIB_LIBS="-lgd"
	], [CAS_DEFS=])
	AS_IF([test -z "$CAS_DEFS"],
		[MULE_WARNING(
			[gd-lib >= 2.0.0, or a functional gd.h (from gd-devel) not found.
			Please check that gdlib-config is in your default path, check out LD_LIBRARY_PATH or equivalent variable.
			Or this might also be that your gdlib-config has other name.
			Please try again with --with-gdlib-config=/usr/bin/gdlib-config (replace /usr/bin/gdlib-config with a valid path to your gdlib-config).
			If you use compiled packages check if you have devel pack installed.
			To download the latest version check http://www.boutell.com/gd for sources.])])
])
AC_SUBST([CAS_DEFS])dnl

dnl Tests explicitly requiring C. They test features of the C compiler.
AC_HEADER_STDBOOL
AC_C_CONST
AC_C_INLINE
AC_C_VOLATILE

dnl Use C++ for all other tests, since we use C++ to actually compile the
dnl sources.  There might be differences between the compilers, thus if
dnl something works in C it doesn't automatically mean it also works in C++.
dnl
dnl The only exception is cas which is written in pure C.
AC_LANG_PUSH([C++])


# Check for zlib
MULE_IF_ENABLED_ANY([monolithic, amule-daemon, amulecmd, webserver, amule-gui],
	[MULE_CHECK_ZLIB([1.1.4],, [AC_MSG_ERROR([zlib >= 1.1.4 is required for aMule])])])


# Check for libpng
MULE_IF_ENABLED([webserver],
	[MULE_CHECK_LIBPNG([1.2.0], [AMULEWEB_DEFS="-DWITH_LIBPNG"], [
		MULE_WARNING(
			[libpng >= 1.2.0 not found.
			amuleweb will be compiled but file download progress bar images will NOT be generated.])
		])
	])
AC_SUBST([AMULEWEB_DEFS])dnl


# Check for GeoIP (required for IP2Country capability)
MULE_IF_ENABLED_ANY([monolithic, amule-gui], [MULE_CHECK_GEOIP])


# Check for wx
MULE_IF_ENABLED_ANY([monolithic, amule-daemon, amule-gui, amulecmd, webserver, alc, alcc, wxcas, fileview],
	[WX_CONFIG_OPTIONS
	WX_STANDARD_OPTIONS([toolkit,wxshared,wxdebug,wxversion])
	# Force using a static wxWidgets library if the executable is to be linked statically.
	MULE_IF_ENABLED([static], [WX_SHARED=0])
	# Force using a unicode build of the library
	MULE_IF_ENABLED_ANY([monolithic, amule-daemon, amule-gui, amulecmd, webserver, fileview], [WX_UNICODE=1])
	DEBUG=$WX_DEBUG
	WX_CONVERT_STANDARD_OPTIONS_TO_WXCONFIG_FLAGS
	WX_CONFIG_CHECK([2.8.12], [wxWin=1],,, [$WXCONFIG_FLAGS])
	AS_IF([test "$wxWin" != 1],
		[AC_MSG_ERROR([
		wxWidgets must be installed on your system but wx-config
		script couldn't be found. Please check that wx-config is
		in path or specified by --with-wx-config=path flag, the
		directory where wxWidgets libraries are installed (returned
		by 'wx-config --libs' command) is in LD_LIBRARY_PATH or
		equivalent variable and wxWidgets version is 2.8.12 or above.
		])]
	)
	AS_IF([test "$WX_VERSION_MAJOR""$WX_VERSION_MINOR" -lt 32], [AC_MSG_ERROR([
		This version of wxWidgets is known to not work with aMule.
		Please upgrade to a newer version.]
		)]
	)
	WX_DETECT_STANDARD_OPTION_VALUES

	# We also need wxBase
	WX_LIBS=`$WX_CONFIG_WITH_ARGS --libs adv,core,net`
	WXBASE_LIBS=`$WX_CONFIG_WITH_ARGS --libs net`
	WXBASE_CPPFLAGS="$WX_CPPFLAGS -DwxUSE_GUI=0"

	MULERCFLAGS=`$WX_CONFIG_WITH_ARGS --cflags | $AWK ['{ for (i = 1; i <= NF; i++) if (index($i, "-I") != 1 && index($i, "-D") != 1) $i = ""; print }']`
])
AC_SUBST([WXBASE_LIBS])dnl
AC_SUBST([WXBASE_CPPFLAGS])dnl

MULE_IF_ENABLED_ANY([monolithic, amule-gui, wxcas, alc],
[AS_IF([test "${WX_PORT}" = base],
	[AC_MSG_ERROR([
		The selected components need a wxWidgets library with GUI,
		but your installation is base-only.  Please install a GUI
		enabled version of wxWidgets and then retry.
	])]
)])
AS_IF([(test "${WX_PORT}" = gtk1 || test "${WX_PORT}" = gtk2)],[AC_MSG_ERROR([${WX_PORT} isn't supported.])])


dnl Resource compiler for W32
AC_ARG_VAR([RC], [Resource Compiler])dnl
AC_ARG_VAR([RCFLAGS], [Resource Compiler Flags])dnl
AS_IF([test ${SYS:-unknown} = win32], [AC_CHECK_TOOL([RC], [windres], [:])])


# Check for libupnp
MULE_ARG_ENABLE([upnp], [yes], [do not compile UPnP code])
MULE_IF_ENABLED_ANY([monolithic, amule-daemon],, [MULE_ENABLEVAR([upnp])=no])
MULE_IF_ENABLED([upnp], [
	LIBUPNP_CHECK(
		[1.6.7],
		[MULE_APPEND([LIBUPNP_CPPFLAGS], [-DENABLE_UPNP=1])],
		[MULE_ENABLEVAR([upnp])=disabled
		MULE_WARNING([UPnP code has been disabled because ${libupnp_error}.])]
	)
])dnl


# Check for Crypto++ (requires C++) >= 5.5
MULE_IF_ENABLED_ANY([monolithic, amule-daemon, amule-gui, fileview],
[MULE_CHECK_CRYPTOPP([5.5],, [
	AC_MSG_ERROR([
	Could not find crypto++ installation or sources.
	Please try again with --with-crypto-prefix=/my_crypto_prefix
	(replace /my_crypto_prefix with a valid path to your crypto++
	installation or source directory).])
])])


MULE_COMPILATION_FLAGS


dnl Checks explicitly requiring C++
MULE_IF_ENABLED_ANY([monolithic, amule-daemon, amule-gui, amulecmd, webserver, ed2k, alc, alcc, wxcas, fileview],
[
dnl MULE_CHECK_GLIBCXX
MULE_CHECK_STRICT_ALIASING
MULE_IF_ENABLED_ANY([monolithic, amule-daemon, alc, alcc], [MULE_CHECK_WX_SUPPORTS_LARGEFILE])

# Check if the compiler is broken, wrt. exceptions.
MULE_CHECK_EXCEPTIONS

MULE_CHECK_CXXABI

MULE_IF_ENABLED_ANY([monolithic, amule-daemon, amule-gui, amulecmd, webserver], [MULE_CHECK_BOOST([1.47])])
])

# Ok, we have all needed libs, now checking for other stuff


# Checks for header files.
AC_FUNC_ALLOCA
AC_HEADER_DIRENT
AC_HEADER_STDC
AC_CHECK_HEADERS([argz.h arpa/inet.h errno.h fcntl.h inttypes.h langinfo.h libintl.h limits.h locale.h malloc.h mntent.h netdb.h netinet/in.h stddef.h nl_types.h signal.h stdint.h stdio_ext.h stdlib.h string.h strings.h sys/ioctl.h sys/mntent.h sys/mnttab.h sys/mount.h sys/param.h sys/resource.h sys/select.h sys/socket.h sys/statvfs.h sys/time.h sys/timeb.h sys/types.h unistd.h])
AC_HEADER_SYS_WAIT


# Checks for typedefs, structures, and compiler characteristics.
AC_TYPE_MODE_T
AC_TYPE_OFF_T
AC_TYPE_SIZE_T
AC_HEADER_TIME
AC_STRUCT_TM
AC_TYPE_UID_T
AC_CHECK_TYPES([ptrdiff_t])


# Checks for library functions.
AC_FUNC_CLOSEDIR_VOID
AC_PROG_GCC_TRADITIONAL
AC_FUNC_FORK
AS_IF([test x$SYS != xwin32], [
	AC_FUNC_GETMNTENT
	AC_FUNC_LSTAT
	AC_FUNC_LSTAT_FOLLOWS_SLASHED_SYMLINK
])
AC_FUNC_MEMCMP
AC_FUNC_SELECT_ARGTYPES
AC_FUNC_SETVBUF_REVERSED
AC_TYPE_SIGNAL
AC_FUNC_STAT
AC_FUNC_STRFTIME
AC_FUNC_STRTOD
AC_FUNC_VPRINTF
dnl Some other functions that worth checking deeper:
dnl AC_FUNC_CLOSEDIR_VOID
dnl AC_FUNC_MEMCMP
dnl AC_FUNC_MMAP
dnl AC_FUNC_STAT
dnl AC_FUNC_STRTOD
dnl AC_FUNC_SETVBUF_REVERSED
dnl Anyways, do we actually USE them?
AS_IF([test $cross_compiling = yes -a $gnumalloc = yes], [
	MULE_WARNING([configure cannot check whether malloc() and realloc() are GNU libc compatible. You have to be ABSOLUTELY sure that they are, otherwise run configure with the --without-gnu-malloc switch.])
	ac_cv_func_malloc_0_nonnull=yes
	ac_cv_func_realloc_0_nonnull=yes
])
AC_FUNC_MALLOC
AC_FUNC_REALLOC
AC_CHECK_FUNCS([__argz_count __argz_next __argz_stringify endpwent floor ftruncate getcwd gethostbyaddr gethostbyname gethostname getopt_long getpass getrlimit gettimeofday inet_ntoa localeconv memmove mempcpy memset mkdir nl_langinfo pow select setlocale setrlimit sigaction socket sqrt stpcpy strcasecmp strchr strcspn strdup strerror strncasecmp strstr strtoul])


dnl This must be *before* MULE_CHECK_NLS
MULE_IF_ENABLED_ANY([monolithic, amule-daemon], [MULE_CHECK_MMAP])


# Look for a way to preallocate disk space for files.
MULE_IF_ENABLED_ANY([monolithic, amule-daemon], [MULE_CHECK_FALLOCATE])


dnl Use the C compiler for the gettext library checks
AC_LANG_POP([C++])
# Checking Native Language Support
dnl Sets gettext version.
dnl AM_GNU_GETTEXT_VERSION *must not* be moved away from configure.ac!
AM_GNU_GETTEXT_VERSION(0.11.5)
MULE_CHECK_NLS
AS_IF([test x$USE_INCLUDED_LIBINTL = xyes], [AC_CONFIG_COMMANDS([intl], [[
	test -d intl || mkdir intl
	test -f intl/plural.c || cp "$srcdir"/intl/plural.c intl/
	touch intl/plural.c
]])])
AC_LANG_PUSH([C++])


# Check for bfd.h (to have proper backtraces without using wxExecute)
dnl This must be *after* MULE_CHECK_NLS
MULE_CHECK_BFD
MULE_CHECK_EXECINFO


# aMule plasma widget
MULE_IF_ENABLED([plasmamule], [PLASMAMULE_CHECKS])


# Check for readline library
MULE_IF_ENABLED_ANY([amulecmd, webserver], [VL_LIB_READLINE])


dnl Tests that should be run in C mode. These tests either test features of the
dnl C compiler or their results will be used in C sources only.
AC_LANG_POP([C++])

# Check for strerror_r
MULE_BACKUP([CPPFLAGS])
MULE_APPEND([CPPFLAGS], [-D_XOPEN_SOURCE=600])
AC_FUNC_STRERROR_R
MULE_RESTORE([CPPFLAGS])


# Other tests

# autoconf >= 2.60 already defines ${docdir}, but we always need it.
AC_MSG_CHECKING([for docdir])
AS_IF([test -z "$docdir"], [docdir="${datadir}/doc/${PACKAGE_NAME}-${PACKAGE_VERSION}"])
AC_SUBST([docdir])dnl
AC_MSG_RESULT([$docdir])


# Try to set the SVN checkout-date
AC_MSG_CHECKING([for SVNDATE])
modified=
MULE_IF(
dnl SVNDATE for tarballs
[test -f "$srcdir/.svn-revision"],
	[svndate="rev. `cat $srcdir/.svn-revision`"],

dnl SVNDATE for Subversion checkouts
[test -d "$srcdir"/.svn],
	[svndate="rev. `svnversion "$srcdir" | ${SED} -e 'h;s/[[0-9]][[0-9]]*:\(.*\)/\1/;s/\([[0-9]][[0-9]]*\).*/\1/;x;/.*M.*/{x;s/$/+/;b;};x'`"],

dnl SVNDATE for git/git-svn clones
[test -d "$srcdir"/.git],
[
	git_revision=`git --git-dir="$srcdir"/.git log --max-count=1 --pretty='tformat:%h'`
	if test -n "`git --git-dir="$srcdir"/.git --work-tree="$srcdir" status --porcelain --untracked-files=no`" ; then
		modified="+"
	fi
	if test -f $srcdir/.git/svn/.metadata ; then
		git_sha=`git --git-dir="$srcdir"/.git log --max-count=1 --pretty='tformat:%H'`
		svndate=`git --git-dir="$srcdir"/.git rev-list ${git_sha} | while read sha; do
			svn_revision=\`git --git-dir="$srcdir"/.git svn find-rev \${sha}\`
			if test -n "\${svn_revision}" ; then
				if test \${sha} = ${git_sha} ; then
					echo "rev. \${svn_revision}${modified}"
				else
					echo "rev. \${svn_revision}+g${git_revision}${modified}"
				fi
				break
			fi
		done`
	else
		svndate="rev. g${git_revision}${modified}"
	fi
],

dnl SVNDATE for mercurial/hgsubversion clones
[test -d "$srcdir"/.hg],
[
	hg_revision=`hg --cwd "$srcdir" identify --id`
	hg_revid=${hg_revision%+}
	if test ${hg_revision} != ${hg_revid} ; then
		modified="+"
	fi
	if test -f "$srcdir"/.hg/svn/uuid ; then
		svndate=`hg --cwd "$srcdir" log --follow --template='r{svnrev} {node|short}\n' | while read svn_revision hg_sha; do
			if test \${svn_revision} != r ; then
				if test \${hg_sha} = ${hg_revid} ; then
					echo "rev. \${svn_revision#r}${modified}"
				else
					echo "rev. \${svn_revision#r}+h${hg_revision}"
				fi
				break
			fi
		done`
	else
		svndate="rev. h${hg_revision}"
	fi
])

AC_MSG_RESULT(${svndate:-None required})
AS_IF([test -n "$svndate"], [
	    AC_DEFINE_UNQUOTED([SVNDATE], ["$svndate"], [The checkout date or svn revision number.])
	    MULE_ADDFLAG([RC], [-D__PRERELEASE__])
])
AC_SUBST([SVNDATE], [$svndate])dnl


dnl Set up the denoiser
MULE_DENOISER


# DO NOT MOVE UP... THERE'S A REASON TO BE HERE!!
MULE_CHECK_CCACHE


dnl ---------------------------------------------------------------------------
dnl End of checks, now write the results and report
dnl ---------------------------------------------------------------------------

AM_CONDITIONAL(SYS_WIN32, test x$SYS = xwin32)
AM_CONDITIONAL(NEED_RC, test x$SYS = xwin32 -a "$RC" != ":" -a x$WX_PORT = xmsw)
AM_CONDITIONAL(NEED_CORESERVICES, test x$SYS = xdarwin)
AM_CONDITIONAL(COMPILE_NLS, test x$USE_NLS = xyes)
AM_CONDITIONAL(INSTALL_SKINS, test MULE_IS_ENABLED_ANY([monolithic, amule-gui]))
AM_CONDITIONAL(PLASMAMULE, test MULE_IS_ENABLED([plasmamule]))

AM_CONDITIONAL([COMPILE_LIB_COMMON],	[test MULE_IS_ENABLED_ANY([monolithic, amule-daemon, amulecmd, webserver, amule-gui, fileview])])
AM_CONDITIONAL([COMPILE_LIB_EC],	[test MULE_IS_ENABLED_ANY([monolithic, amule-daemon, amulecmd, webserver, amule-gui])])

dnl Generate the Makefiles
AC_CONFIG_FILES([Makefile
		 docs/Makefile
		 docs/man/Makefile
		 intl/Makefile
		 po/Makefile.in
		 src/Makefile
		 src/pixmaps/Makefile
		 src/pixmaps/flags_xpm/Makefile
		 src/libs/Makefile
		 src/libs/ec/Makefile
		 src/libs/ec/cpp/Makefile
		 src/libs/common/Makefile
		 src/utils/Makefile
		 src/utils/aLinkCreator/Makefile
		 src/utils/aLinkCreator/docs/Makefile
		 src/utils/aLinkCreator/src/Makefile
		 src/utils/cas/Makefile
		 src/utils/cas/docs/Makefile
		 src/utils/fileview/Makefile
		 src/utils/plasmamule/Makefile
		 src/utils/wxCas/Makefile
		 src/utils/wxCas/docs/Makefile
		 src/utils/wxCas/src/Makefile
		 src/utils/xas/Makefile
		 src/utils/xas/autostart-xas
		 src/utils/xas/docs/Makefile
		 src/skins/Makefile
		 src/webserver/Makefile
		 src/webserver/src/Makefile
		 src/webserver/default/Makefile
		 unittests/Makefile
		 unittests/muleunit/Makefile
		 unittests/tests/Makefile])

AS_IF([test x$SYS = xwin32], [AC_CONFIG_FILES([version.rc])])
AC_OUTPUT

AS_IF([test -n "$svndate"], [SVN_REVISION=" ($svndate)"], [SVN_REVISION=])

echo
echo
echo "  Configure script has finished system check."
echo
echo "  Configured aMule ${PACKAGE_VERSION}${SVN_REVISION} for '${host}'."
echo
echo "  aMule enabled options:"
echo
echo "  **** aMule Core ****"
echo "  Prefix where aMule should be installed?                    ${prefix:-none}"
echo "  Should aMule be compiled with i18n support?                ${USE_NLS:-no}"
echo "  Should aMule be compiled in debug mode?                    MULE_STATUSOF([debug])"
echo "  Should aMule be compiled with profiling?                   MULE_STATUSOF([profile])"
echo "  Should aMule be compiled with optimizations?               MULE_STATUSOF([optimize])"
echo "  Should aMule be compiled with UPnP support?                MULE_STATUSOF([upnp])"
echo "  Should aMule be compiled with IP2country support?          MULE_STATUSOF([geoip])"
echo "  Should aMule monolithic application be built?              MULE_STATUSOF([monolithic])"
echo "  Should aMule daemon version be built?                      MULE_STATUSOF([amule-daemon])"
echo "  Should aMule remote gui be built?                          MULE_STATUSOF([amule-gui])"

echo
echo "  **** aMule TextClient ****"
echo "  Should aMule Command Line Client be built?                 MULE_STATUSOF([amulecmd])"
echo
echo "  **** aMule WebServer ****"
echo "  Should aMule WebServer be built?                           MULE_STATUSOF([webserver])"
echo
echo "  **** aMule ED2K Links Handler ****"
echo "  Should aMule ED2K Links Handler be built?                  MULE_STATUSOF([ed2k])"
echo
echo "  **** aMuleLinkCreator ****"
echo "  Should aMuleLinkCreator GUI version (alc) be built?        MULE_STATUSOF([alc])"
echo "  Should aMuleLinkCreator for console (alcc) be built?       MULE_STATUSOF([alcc])"
echo
echo "  **** aMule Statistics ****"
echo "  Should C aMule Statistics (CAS) be built?                  MULE_STATUSOF([cas])"
echo "  Should aMule GUI Statistics (wxCas) be built?              MULE_STATUSOF([wxcas])"
echo "  Should xas XChat2 plugin be installed?                     MULE_STATUSOF([xas])"
echo "  Should plasmaMule plasma-applet be built?                  MULE_STATUSOF([plasmamule])"

echo
echo "  **** General Libraries and Tools ****"
echo "  Should ccache support be enabled?                          MULE_STATUSOF([ccache])"
echo "  Should aMule file viewer for console be built?             MULE_STATUSOF([fileview])"
echo "  Libraries aMule will use to build:"
MULE_IF_ENABLED_ANY([monolithic, amule-daemon, amule-gui, amulecmd, webserver, alc, alcc, wxcas, fileview], [
	wxinfo="$WX_PORT"
	AS_IF([test $WX_DEBUG -eq 1], [wxinfo="$wxinfo,debug"])
	AS_IF([test $WX_SHARED -eq 1], [wxinfo="$wxinfo,shared"], [wxinfo="$wxinfo,static"])
	echo "                             wxWidgets             ${WX_VERSION} ($wxinfo)"
])
AS_IF([test ${with_boost:-no} != no],
[MULE_IF(
	[test ${with_boost:-no} = disabled],
		[echo "                             boost                 Not detected"],
	[test ${boost_cv_inc_path:-yes} != yes],
		[echo "                             boost                 `echo ${boost_cv_lib_version} | sed -e 's/_/./g'` (in ${boost_cv_inc_path})"],
		[echo "                             boost                 `echo ${boost_cv_lib_version} | sed -e 's/_/./g'`"],
)])
MULE_IF_ENABLED_ANY([monolithic, amule-daemon, amule-gui, fileview],
	[echo "                             crypto++              ${CRYPTOPP_VERSION_STRING} (in ${CRYPTOPP_PREFIX})"])
MULE_IF_ENABLED([upnp], [
	AS_IF([test -n "$with_libupnp_prefix"], [libupnp_place=" (in $with_libupnp_prefix)"])
	echo "                             libupnp               ${LIBUPNP_VERSION:-Not detected}${libupnp_place:-}"
])
if test ${enable_nls:-yes} = yes; then
AS_IF([test x$USE_NLS = xno],
	[libintl_found="Not detected"],
	[AS_IF([test x$USE_INCLUDED_LIBINTL = xyes],
		[libintl_found="included"],
		[libintl_found="system"])])
echo "                             libintl               $libintl_found"
fi
MULE_IF_ENABLED([geoip],
	[AS_IF([test -z "${with_geoip_headers:-}${with_geoip_lib:-}"], [geoip_style=system], [geoip_style=user])
	MULE_IF_ENABLED_ANY([monolithic, amule-gui],
		[echo "                             libGeoIP              ${geoip_style}"])])
MULE_IF_ENABLED([webserver],
	[echo "                             libpng                ${LIBPNG_VERSION:-Not detected}"])
MULE_IF_ENABLED([cas],
	[echo "                             libgd                 ${GDLIB_VERSION:-Not detected}"])
MULE_IF_ENABLED_ANY([monolithic, amule-daemon, amulecmd, webserver, amule-gui],
	[echo "                             zlib                  ${ZLIB_VERSION:-Not detected}"])
MULE_IF_ENABLED([plasmamule],
[echo "                             qt-moc                ${QT_MOC} ${QT_MOC_VERSION}"
echo "                             qt-core-libs          ${QT_CORE_LIBS}"
echo "                             qt-gui-libs           ${QT_GUI_LIBS}"
echo "                             kde4-config           ${KDE4_CONFIG} (${KDE_CONF_VER})"
echo "                             kde-headers           ${KDE_HEADER_DIR}"
echo "                             kde-services-path     ${KDE_SERVICE_PATH}"
echo "                             kde-module-path       ${KDE_MODULE_PATH}"
echo "                             kde-icon-path         ${KDE_ICON_PATH}"
echo "                             kde-mime-path         ${KDE_MIME_PATH}"
echo "                             kde-applinks-path     ${KDE_APPLNK_PATH}"
])
