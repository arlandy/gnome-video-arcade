### Process this file with autoconf to produce a configure script

AC_PREREQ([2.54])

m4_define([gva_version], 0.8.4)

AC_INIT([GNOME Video Arcade], [gva_version],
        [mbarnes@redhat.com], gnome-video-arcade)

AC_CONFIG_MACRO_DIR(m4)
AC_CONFIG_HEADER(config.h)
AC_CONFIG_SRCDIR(src/main.c)

AM_INIT_AUTOMAKE([1.11 no-dist-gzip dist-xz])
AM_MAINTAINER_MODE

GVA_VERSION=gva_version
AC_SUBST(GVA_VERSION)

AC_SUBST(abs_top_builddir)

# Automake 1.11 - Silent Build Rules
m4_ifdef([AM_SILENT_RULES], [AM_SILENT_RULES([yes])])

# Dependencies
GLIB_MODULES="glib-2.0 gio-2.0 >= 2.28"
PKG_CHECK_MODULES(GLIB, $GLIB_MODULES)
AC_SUBST(GLIB_CFLAGS)
AC_SUBST(GLIB_LIBS)

GTK_MODULES="gtk+-3.0 >= 3.0"
PKG_CHECK_MODULES(GTK, $GTK_MODULES)
AC_SUBST(GTK_CFLAGS)
AC_SUBST(GTK_LIBS)

GIT_MODULES="gnome-icon-theme >= 2.18.0"
PKG_CHECK_MODULES(GIT, $GIT_MODULES)
AC_SUBST(GIT_CFLAGS)
AC_SUBST(GIT_LIBS)

SOUP_MODULES="libsoup-2.4 >= 2.4.0"
PKG_CHECK_MODULES(SOUP, $SOUP_MODULES)
AC_SUBST(SOUP_CFLAGS)
AC_SUBST(SOUP_LIBS)

SQLITE_MODULES="sqlite3 >= 3.0.0"
PKG_CHECK_MODULES(SQLITE, $SQLITE_MODULES)
AC_SUBST(SQLITE_CFLAGS)
AC_SUBST(SQLITE_LIBS)

# Needed for the monospace font name.
GDS_MODULES="gsettings-desktop-schemas"
PKG_CHECK_MODULES(GSETTINGS_DESKTOP_SCHEMAS, $GDS_MODULES)

# --with-gstreamer=[yes|no]
AC_ARG_WITH([gstreamer],
        [AC_HELP_STRING([--with-gstreamer],
        [use GStreamer to stream music [default=yes]])],
        with_gstreamer="$withval", with_gstreamer="yes")
if test "$with_gstreamer" = "yes"; then
GSTREAMER_MODULES="gstreamer-plugins-base-0.10"
PKG_CHECK_MODULES(GSTREAMER, $GSTREAMER_MODULES)
AC_DEFINE_UNQUOTED(HAVE_GSTREAMER, 1,
        [Define to 1 if you are using GStreamer])
else
GSTREAMER_CFLAGS=
GSTREAMER_LIBS=
fi
AM_CONDITIONAL(HAVE_GSTREAMER, test $with_gstreamer = yes)
AC_SUBST(GSTREAMER_CFLAGS)
AC_SUBST(GSTREAMER_LIBS)

# --with-wnck=[yes|no]
AC_ARG_WITH([wnck],
        [AC_HELP_STRING([--with-wnck],
        [manage game windows with libwnck [default=yes]])],
        with_wnck="$withval", with_wnck="yes")
if test "$with_wnck" = "yes"; then
WNCK_MODULES="libwnck-3.0 >= 2.91.6"
PKG_CHECK_MODULES(WNCK, $WNCK_MODULES)
AC_DEFINE_UNQUOTED(HAVE_WNCK, 1,
        [Define to 1 if you are using libwnck])
else
WNCK_CFLAGS=
WNCK_LIBS=
fi
AC_SUBST(WNCK_CFLAGS)
AC_SUBST(WNCK_LIBS)

# --with-glade-catalog=[yes|no]
AC_ARG_WITH([glade-catalog],
        [AC_HELP_STRING([--with-glade-catalog],
                [install the catalog files for Glade 3 ]
                [(for maintainers only) [default=no]])],
        with_catalog="$withval", with_catalog="no")
if test "$with_catalog" = "yes"; then
        GLADEUI_MODULES="gladeui-2.0 >= 3.10.0"
        PKG_CHECK_MODULES(GLADEUI, $GLADEUI_MODULES)
fi
AM_CONDITIONAL(GLADE_CATALOG, test $with_catalog = yes)

# Header Checks
AC_HEADER_STDC
AC_CHECK_HEADERS(wordexp.h)

# Program Checks
AC_PROG_CC
AC_PROG_INSTALL
AC_PROG_INTLTOOL
AC_PROG_LIBTOOL
AC_PATH_PROG(BASENAME, basename)
AC_PATH_PROG(GCONFTOOL, gconftool-2)

# MAME Program
AC_ARG_VAR([MAME], [MAME command])
AC_PATH_PROGS([MAME], [mame sdlmame],, [$PATH:/usr/local/games:/usr/games])
if test "x$MAME" != "x"; then
        MAME_PROGRAM=$MAME
        MAME_BACKEND='gva-mame-sdlmame.$(OBJEXT)'
else
        AC_ARG_VAR([XMAME], [XMAME command])
        AC_PATH_PROG([XMAME], [xmame],, [$PATH:/usr/local/games:/usr/games])
        if test "x$XMAME" != "x"; then
                MAME_PROGRAM=$XMAME
                MAME_BACKEND='gva-mame-xmame.$(OBJEXT)'
        fi
fi
if test "x$MAME" = "x"; then
        AC_MSG_ERROR([

        Unable to find a MAME program.

        Please install either sdlmame or xmame, or specify the
        MAME command with a MAME or XMAME environment variable.

        e.g. XMAME=/path/to/program/xmame.x11 ./configure
])
fi
AC_SUBST([MAME_BACKEND])
AC_DEFINE_UNQUOTED(MAME_PROGRAM, "$MAME_PROGRAM", [Location of the MAME program])

# Localization
AH_TEMPLATE(GETTEXT_PACKAGE, [Package name for gettext])
GETTEXT_PACKAGE=gnome-video-arcade
AC_DEFINE_UNQUOTED(GETTEXT_PACKAGE, "$GETTEXT_PACKAGE")
AC_SUBST(GETTEXT_PACKAGE)
AM_GLIB_GNU_GETTEXT

# Miscellaneous
GLIB_GSETTINGS
GTK_DOC_CHECK(1.6)
YELP_HELP_INIT

# Optional Features

# --with-category-file=PATH
AC_MSG_CHECKING([for category file])
AC_ARG_WITH([category-file],
        [AC_HELP_STRING([--with-category-file=PATH],
        [path to category file (usually named 'catver.ini')])],
        with_category="$withval", with_category="no")
if test "$with_category" = "yes"; then
        AC_MSG_ERROR([--with-category-file missing PATH argument])
fi
if test "$with_category" != "no"; then
        AC_DEFINE_UNQUOTED(CATEGORY_FILE, "$with_category",
                [Location of the category file])
fi
AC_MSG_RESULT($with_category)

# --with-history-file=PATH
AC_MSG_CHECKING([for arcade history file])
AC_ARG_WITH([history-file],
        [AC_HELP_STRING([--with-history-file=PATH],
        [path to arcade history file (usually named 'history.dat')])],
        with_history="$withval", with_history="no")
if test "$with_history" = "yes"; then
        AC_MSG_ERROR([--with-history-file missing PATH argument])
fi
if test "$with_history" != "no"; then
        AC_DEFINE_UNQUOTED(HISTORY_FILE, "$with_history",
                [Location of the arcade history file])
fi
AC_MSG_RESULT($with_history)

# --with-nplayers-file=PATH
AC_MSG_CHECKING([for number of players file])
AC_ARG_WITH([nplayers-file],
        [AC_HELP_STRING([--with-nplayers-file=PATH],
        [path to number of players file (usually named 'nplayers.ini')])],
        with_nplayers="$withval", with_nplayers="no")
if test "$with_nplayers" != "no"; then
        AC_DEFINE_UNQUOTED(NPLAYERS_FILE, "$with_nplayers",
                [Location of the number of players file])
fi
AC_MSG_RESULT($with_nplayers)

AC_CONFIG_FILES([
        Makefile
        data/Makefile
        docs/Makefile
        docs/reference/Makefile
        docs/reference/version.xml
        help/Makefile
        maint/Makefile
        po/Makefile.in
        src/Makefile
])
AC_OUTPUT

echo
echo "    Configuration Summary"
echo "    ---------------------"
echo
echo "    MAME Program    : $MAME_PROGRAM"
echo "    Category File   : $with_category"
echo "    History File    : $with_history"
echo "    NPlayers File   : $with_nplayers"
echo "    Use GStreamer   : $with_gstreamer"
echo "    Use libwnck     : $with_wnck"
echo
