# -*- mode: m4; -*-
# -*- Autoconf -*-
# Process this file with autoconf to produce a configure script.
# $Id$

AC_PREREQ(2.59)
AC_INIT([liblcfg], [0.2.2], [pbaecher_removeme@gmail.com])
AM_INIT_AUTOMAKE([liblcfg], [0.2.2])
AC_REVISION([$Id$])

AC_PREFIX_DEFAULT(/opt/liblcfg)
AC_CONFIG_SRCDIR([include/lcfg/lcfg.h])
AM_CONFIG_HEADER([config.h])
# AM_MAINTAINER_MODE

AM_CONDITIONAL(NDEBUG, test "$disable_debug" = yes)
AM_CONDITIONAL(PROFILE, test "$enable_profile" = yes)

AC_CANONICAL_HOST
AC_C_CONST
AC_C_BIGENDIAN

dnl Check for some target-specific stuff
case "$host" in
*-*-freebsd*)
  CPPFLAGS="$CPPFLAGS -I/usr/local/include -I/usr/src/contrib/file/"
  LDFLAGS="$LDFLAGS -L/usr/local/lib -Wl,-rpath,/usr/local/lib"
  ;;
*-*-linux*)
  CPPFLAGS="$CPPFLAGS -D _GNU_SOURCE -I/usr/local/include"
  LDFLAGS="$LDFLAGS -L/usr/local/lib -Wl,-rpath,/usr/local/lib"
  ;;
*-*-darwin*)
  CPPFLAGS="$CPPFLAGS -I/opt/local/include"
  LDFLAGS="$LDFLAGS -L/opt/local/lib"
  if test "$GCC" = "yes"; then
          CFLAGS="$CFLAGS -no-cpp-precomp"
  fi
  ;;
esac

# Checks for programs.
AC_PROG_CC
AC_PROG_MAKE_SET
AC_PROG_LIBTOOL

AC_CHECK_HEADERS([stdint.h stdlib.h string.h strings.h unistd.h])

AC_ARG_ENABLE(check, [  --enable-check      enable check unit testing],
	[enable_check="$enableval"],[enable_check="no"])

AM_CONDITIONAL(CHECK, test x$enable_check = "xyes")

# check unit testing
if test x$enable_check = "xyes" ; then
	PKG_CHECK_MODULES([CHECK], [check >= 0.9.4])
	AM_PROG_CC_C_O
fi

dnl   We ought not to have to worry about getopt.h, getopt{,1}.c,
dnl   since they can always be unconditionally compiled and linked.  See
dnl   http://mail.gnu.org/archive/html/autoconf/2000-12/msg00049.html
dnl   and surrounding thread for discussion.  However that doesn't
dnl   appear to work in fact, and the function in getopt_long.c has
dnl   different magic to avoid getting horribly tangled up.
AC_CHECK_FUNCS(getopt)
AC_CHECK_DECLS([getopt,getsubopt,getopt_long,setenv,putenv])
AC_REPLACE_FUNCS(getopt_long getsubopt)

dnl   getopt_long.c uses (and provides a replacement for)
dnl   this glibc utility function
AC_CHECK_FUNCS(__strchrnul)


# Checks for typedefs, structures, and compiler characteristics.
AC_C_INLINE
AC_TYPE_UID_T
AC_STRUCT_TM
AC_TYPE_SIZE_T
AC_TYPE_SIGNAL


AC_CHECK_SIZEOF(long)
AC_CHECK_SIZEOF(off_t)

# Checks for library functions.
AC_FUNC_ERROR_AT_LINE
AC_TYPE_SIGNAL
AC_CHECK_FUNCS([strndup inet_ntoa  memmove memset strdup strerror])

# library soname
# check http://sources.redhat.com/autobook/autobook/autobook_91.html#SEC91
liblcfg_current=1
liblcfg_revision=0
liblcfg_age=0
liblcfg_soname=$liblcfg_current:$liblcfg_revision:$liblcfg_age
AC_SUBST(liblcfg_soname)



dnl **************************************************
dnl * large filesystem Support                       *
dnl **************************************************

AC_MSG_CHECKING(for Large File System support)
AC_ARG_ENABLE(lfs,
 AC_HELP_STRING([--enable-lfs],[Turn on Large File System (default)]),
 [case "$host" in
 *-*-linux*)
 case "${enableval}" in
    yes) CPPFLAGS="${CPPFLAGS} -D_FILE_OFFSET_BITS=64 -D_LARGEFILE_SOURCE -D_LARGE_FILES" ;;
     no) ;;
      *) AC_MSG_ERROR(bad value ${enableval} for --enable-lfs) ;;
   esac
   ;;
 esac],[
 CPPFLAGS="${CPPFLAGS} -D_FILE_OFFSET_BITS=64 -D_LARGEFILE_SOURCE -D_LARGE_FILES"
 enable_lfs=yes]
 ,enable_lfs=no,)
AC_MSG_RESULT($enableval)



AC_DEFUN([AC_DEFINE_DIR], [
  prefix_NONE=
  exec_prefix_NONE=
  test "x$prefix" = xNONE && prefix_NONE=yes && prefix=$ac_default_prefix
  test "x$exec_prefix" = xNONE && exec_prefix_NONE=yes && exec_prefix=$prefix
dnl In Autoconf 2.60, ${datadir} refers to ${datarootdir}, which in turn
dnl refers to ${prefix}.  Thus we have to use `eval' twice.
  eval ac_define_dir="\"[$]$2\""
  eval ac_define_dir="\"$ac_define_dir\""
  AC_SUBST($1, "$ac_define_dir")
  AC_DEFINE_UNQUOTED($1, "$ac_define_dir", [$3])
  test "$prefix_NONE" && prefix=NONE
  test "$exec_prefix_NONE" && exec_prefix=NONE
])

AC_DEFINE_DIR(PREFIX, prefix, [default working directory])
AC_DEFINE_DIR(LOCALESTATEDIR, localstatedir, [where to put logs etc])
AC_DEFINE_DIR(LIBDIR, libdir, [where to look for plugins])
AC_DEFINE_DIR(SYSCONFDIR, [sysconfdir], [System configuration dir])
AC_DEFINE_DIR(DATADIR, [datadir], [The directory for installing idiosyncratic read-only architecture-independent data.])
AC_DEFINE_DIR(DATAROOTDIR, [datarootdir], [The root of the directory tree for read-only architecture-independent data files.])

lcfg_data_dir=$datadir/lcfg

AC_SUBST(lcfg_data_dir)

AC_CONFIG_FILES([Makefile
		src/Makefile
		include/Makefile
		include/lcfg/Makefile
		include/lcfgx/Makefile
		tools/Makefile
		tests/Makefile])

                 
AC_OUTPUT

