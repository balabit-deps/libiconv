dnl Copyright (C) 1999-2007 Free Software Foundation, Inc.
dnl This file is part of the GNU LIBICONV Library.
dnl
dnl The GNU LIBICONV Library is free software; you can redistribute it
dnl and/or modify it under the terms of the GNU Library General Public
dnl License as published by the Free Software Foundation; either version 2
dnl of the License, or (at your option) any later version.
dnl
dnl The GNU LIBICONV Library is distributed in the hope that it will be
dnl useful, but WITHOUT ANY WARRANTY; without even the implied warranty of
dnl MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the GNU
dnl Library General Public License for more details.
dnl
dnl You should have received a copy of the GNU Library General Public
dnl License along with the GNU LIBICONV Library; see the file COPYING.LIB.
dnl If not, write to the Free Software Foundation, Inc., 51 Franklin Street,
dnl Fifth Floor, Boston, MA 02110-1301, USA.

AC_PREREQ([2.60])
AC_INIT
AC_CONFIG_SRCDIR(lib/iconv.c)
AC_CONFIG_AUX_DIR(build-aux)
AM_INIT_AUTOMAKE(libiconv, 1.12)
AC_CONFIG_HEADERS(config.h lib/config.h)
AC_PROG_MAKE_SET
AC_CONFIG_MACRO_DIR([m4])

dnl           checks for basic programs

AC_PROG_CC
AC_PROG_CPP
AC_PROG_INSTALL

dnl           check for host type

AC_CANONICAL_HOST

dnl           hack

dnl Hack to work around limitations of automake and error.m4 regarding LIBOBJS.
dnl "srclib/Makefile.am:32: automatically discovered file `error.h' should not be explicitly mentioned"
AC_DEFUN([my_SRCLIB_OBJ],[SRCLIBOBJS="$SRCLIBOBJS $1.$ac_objext"])
AC_DEFUN([my_SRCLIB_REPLACE_FUNCS], [AC_CHECK_FUNCS([$1], , [my_SRCLIB_OBJ($ac_func)])])
m4_pushdef([AC_LIBOBJ], m4_defn([my_SRCLIB_OBJ]))
m4_pushdef([AC_REPLACE_FUNCS], m4_defn([my_SRCLIB_REPLACE_FUNCS]))

dnl           checks for UNIX variants that set DEFS

gl_EARLY

dnl           checks for installer options

gl_RELOCATABLE([srclib])

AC_ARG_ENABLE([extra-encodings],
  [AC_HELP_STRING([--enable-extra-encodings],
                  [add support for a few rarely used encodings])],
  [AC_DEFINE([ENABLE_EXTRA], 1,
             [Define to 1 to enable a few rarely used encodings.])
   USE_EXTRA_ENCODINGS=yes],
  [USE_EXTRA_ENCODINGS=no])
AC_SUBST([USE_EXTRA_ENCODINGS])

dnl           checks for programs

CL_PROG_CP
CL_PROG_LN
AC_PROG_LN_S

dnl           checks for compiler output filename suffixes

AC_OBJEXT
AC_EXEEXT

dnl           check for build configuration

AC_DISABLE_STATIC
AC_PROG_LIBTOOL
gl_VISIBILITY
AM_ICONV
AM_GNU_GETTEXT([external], [need-ngettext])

dnl           checks for typedefs

AC_TYPE_MBSTATE_T
gt_TYPE_WCHAR_T

dnl           checks for header files, functions and declarations

if test "$am_cv_func_iconv" = yes -a -n "$am_cv_proto_iconv_arg1"; then
  ICONV_CONST="const"
else
  ICONV_CONST=""
fi
AC_SUBST(ICONV_CONST)

if test $gt_cv_c_wchar_t = yes; then
  HAVE_WCHAR_T=1
else
  HAVE_WCHAR_T=0
fi
AC_SUBST(HAVE_WCHAR_T)

AC_CHECK_FUNCS([getc_unlocked mbrtowc wcrtomb mbsinit setlocale])
AC_REPLACE_FUNCS([memmove])

AM_LANGINFO_CODESET

AC_EILSEQ

CL_WORDS_LITTLEENDIAN

gl_INIT

dnl           hack

dnl End of hack to work around limitations of automake and error.m4.
m4_popdef([AC_REPLACE_FUNCS])
m4_popdef([AC_LIBOBJ])
AC_SUBST([SRCLIBOBJS])

dnl           check whether OS supports plug libraries

OS=""
case "$host_os" in
  linux*)   OS="linux" ;;
  solaris*) OS="solaris" ;;
  osf*)     OS="osf" ;;
esac
if test -n "$OS"; then
  AC_SUBST(OS)
  AC_SUBST(GCC)
  PLUGLIB="preloadable_libiconv.so"
else
  PLUGLIB=""
fi
AC_SUBST(PLUGLIB)

dnl Compilation on mingw and Cygwin needs special Makefile rules, because
dnl 1. when we install a shared library, we must arrange to export
dnl    auxiliary pointer variables for every exported variable,
dnl 2. when we install a shared library and a static library simultaneously,
dnl    the include file specifies __declspec(dllimport) and therefore we
dnl    must arrange to define the auxiliary pointer variables for the
dnl    exported variables _also_ in the static library.
if test "$enable_shared" = yes; then
  case "$host_os" in
    mingw* | cygwin*) is_woe32dll=yes ;;
    *) is_woe32dll=no ;;
  esac
else
  is_woe32dll=no
fi
WOE32DLL=$is_woe32dll
AC_SUBST([WOE32DLL])
if test $is_woe32dll = yes; then
  DLL_VARIABLE='__declspec (dllimport)'
else
  DLL_VARIABLE=
fi
AC_SUBST([DLL_VARIABLE])

dnl On mingw and Cygwin, we can activate special Makefile rules which add
dnl version information to the shared libraries and executables.
case "$host_os" in
  mingw* | cygwin*) is_woe32=yes ;;
  *) is_woe32=no ;;
esac
WOE32=$is_woe32
AC_SUBST([WOE32])
if test $WOE32 = yes; then
  dnl Check for a program that compiles Windows resource files.
  AC_CHECK_TOOL([WINDRES], [windres])
fi

AH_BOTTOM([
/* On Windows, variables that may be in a DLL must be marked specially.  */
#if defined _MSC_VER && defined _DLL
# define DLL_VARIABLE __declspec (dllimport)
#else
# define DLL_VARIABLE
#endif
])

AC_CONFIG_SUBDIRS(libcharset)
AC_CONFIG_FILES([Makefile])
AC_CONFIG_FILES([lib/Makefile])
AC_CONFIG_FILES([srclib/Makefile])
AC_CONFIG_FILES([src/Makefile])
AC_CONFIG_FILES([po/Makefile.in])
AC_CONFIG_FILES([man/Makefile])
if test -d "${srcdir}/tests"; then
  AC_CONFIG_FILES([tests/Makefile])
fi
AC_CONFIG_FILES([include/iconv.h:include/iconv.h.build.in])
AC_CONFIG_FILES([include/iconv.h.inst:include/iconv.h.in])
AC_OUTPUT
