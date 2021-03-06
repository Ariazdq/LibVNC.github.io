# Process this file with autoconf to produce a configure script.
AC_INIT(LibVNCServer, 0.8pre, http://sourceforge.net/projects/libvncserver)
AM_INIT_AUTOMAKE(LibVNCServer, 0.8pre)
AM_CONFIG_HEADER(rfbconfig.h)
AX_PREFIX_CONFIG_H([rfb/rfbconfig.h])

# Checks for programs.
AC_PROG_CC
if test -z "$CC"; then
	CCLD="\$(CC)"
else
	CCLD="$CC"
fi
AC_PROG_MAKE_SET
AC_PROG_RANLIB

# Options
AH_TEMPLATE(BACKCHANNEL, [Enable BackChannel communication])
AC_ARG_WITH(backchannel,
	[  --without-backchannel		disable backchannel method],
	, [ with_backchannel=yes ])
if test "x$with_backchannel" = "xyes"; then
	AC_DEFINE(BACKCHANNEL)
fi
AH_TEMPLATE(ALLOW24BPP, [Enable 24 bit per pixel in native framebuffer])
AC_ARG_WITH(24bpp,
	[  --without-24bpp			disable 24 bpp framebuffers],
	, [ with_24bpp=yes ])
if test "x$with_24bpp" = "xyes"; then
	AC_DEFINE(ALLOW24BPP)
fi
AH_TEMPLATE(FFMPEG, [Use ffmpeg (for vnc2mpg)])
AC_ARG_WITH(ffmpeg,
	[ --with-ffmpeg=dir		set ffmpeg home directory],,)
AC_SUBST(with_ffmpeg)
AM_CONDITIONAL(WITH_FFMPEG, test ! -z "$with_ffmpeg")

# Checks for X libraries
HAVE_X="false"
AC_PATH_XTRA
AH_TEMPLATE(HAVE_XSHM, [MIT-SHM extension build environment present])
AH_TEMPLATE(HAVE_XTEST, [XTEST extension build environment present])
AH_TEMPLATE(HAVE_XKEYBOARD, [XKEYBOARD extension build environment present])
AH_TEMPLATE(HAVE_LIBXINERAMA, [XINERAMA extension build environment present])
AH_TEMPLATE(HAVE_LIBXRANDR, [XRANDR extension build environment present])
AH_TEMPLATE(HAVE_LIBXFIXES, [XFIXES extension build environment present])
AH_TEMPLATE(HAVE_LIBXDAMAGE, [XDAMAGE extension build environment present])
if test "$X_CFLAGS" != "-DX_DISPLAY_MISSING"; then
	AC_CHECK_LIB(X11, XGetImage, HAVE_X="true",
		HAVE_X="false",
		$X_LIBS $X_PRELIBS -lX11 $X_EXTRA_LIBS)

	if test $HAVE_X = "true"; then
		X_PRELIBS="$X_PRELIBS -lXext"

		AC_CHECK_LIB(Xext, XShmGetImage, 
			[AC_DEFINE(HAVE_XSHM)], ,
			$X_LIBS $X_PRELIBS -lX11 $X_EXTRA_LIBS)

		AC_CHECK_LIB(Xtst, XTestFakeKeyEvent, 
			X_PRELIBS="$X_PRELIBS -lXtst"
			[AC_DEFINE(HAVE_XTEST)], ,
			$X_LIBS $X_PRELIBS -lX11 $X_EXTRA_LIBS)

		saved_CPPFLAGS="$CPPFLAGS"
		CPPFLAGS="$CPPFLAGS $X_CFLAGS"
		AC_CHECK_HEADER(X11/XKBlib.h, HAVE_XKBLIB_H="true",
			HAVE_XKBLIB_H="false", [#include <X11/Xlib.h>])
		CPPFLAGS="$saved_CPPFLAGS"
		if test $HAVE_XKBLIB_H = "true"; then
			AC_CHECK_LIB(X11, XkbSelectEvents,
				[AC_DEFINE(HAVE_XKEYBOARD)], ,
				$X_LIBS $X_PRELIBS -lX11 $X_EXTRA_LIBS)
		fi

		AC_CHECK_LIB(Xinerama, XineramaQueryScreens,
			X_PRELIBS="$X_PRELIBS -lXinerama"
			[AC_DEFINE(HAVE_LIBXINERAMA)], ,
			$X_LIBS $X_PRELIBS -lX11 $X_EXTRA_LIBS)

		AC_CHECK_LIB(Xrandr, XRRSelectInput,
			X_PRELIBS="$X_PRELIBS -lXrandr"
			[AC_DEFINE(HAVE_LIBXRANDR)], ,
			$X_LIBS $X_PRELIBS -lX11 $X_EXTRA_LIBS)

		AC_CHECK_LIB(Xfixes, XFixesGetCursorImage,
			X_PRELIBS="$X_PRELIBS -lXfixes"
			[AC_DEFINE(HAVE_LIBXFIXES)], ,
			$X_LIBS $X_PRELIBS -lX11 $X_EXTRA_LIBS)

		AC_CHECK_LIB(Xdamage, XDamageQueryExtension,
			X_PRELIBS="$X_PRELIBS -lXdamage"
			[AC_DEFINE(HAVE_LIBXDAMAGE)], ,
			$X_LIBS $X_PRELIBS -lX11 $X_EXTRA_LIBS)

		X_LIBS="$X_LIBS $X_PRELIBS -lX11 $X_EXTRA_LIBS"
	fi
fi
AC_SUBST(X_LIBS)
AM_CONDITIONAL(HAVE_X, test $HAVE_X != "false")

# Checks for libraries.
AC_ARG_WITH(jpeg,
 	[  --without-jpeg			disable support for jpeg],
 	, [ with_jpeg=yes ])
if test "x$with_jpeg" = "xyes"; then
	AC_CHECK_HEADER(jpeglib.h, HAVE_JPEGLIB_H="true")
fi
AC_CHECK_HEADER(pthread.h, HAVE_PTHREAD_H="true")
AC_ARG_WITH(zlib,
 	[  --without-zlib			disable support for deflate],
 	, [ with_zlib=yes ])
AC_ARG_WITH(libz,
 	[  --without-libz			disable support for deflate],
 	, [ with_libz=yes ])
if test "x$with_zlib" = "xyes" -a "x$with_libz" = "xyes"; then
	AC_CHECK_HEADER(zlib.h, HAVE_ZLIB_H="true")
fi
if test ! -z "$HAVE_ZLIB_H"; then
	AC_CHECK_LIB(z, deflate, , HAVE_ZLIB_H="")
	if test ! -z "$HAVE_JPEGLIB_H" -a ! -z "$HAVE_ZLIB_H"; then
		AC_CHECK_LIB(jpeg, jpeg_CreateCompress, , HAVE_JPEGLIB_H="")
	fi
fi
if test ! -z "$HAVE_PTHREAD_H"; then
	AC_CHECK_LIB(pthread, pthread_mutex_lock)
	AC_CHECK_LIB(pthread, pthread_mutex_lock, HAVE_LIBPTHREAD="true")
fi
AM_CONDITIONAL(HAVE_LIBPTHREAD, test ! -z "$HAVE_LIBPTHREAD")
AM_CONDITIONAL(HAVE_LIBZ, test ! -z "$HAVE_ZLIB_H")
AM_CONDITIONAL(HAVE_LIBJPEG, test ! -z "$HAVE_JPEGLIB_H")
if test -z "$with_sdl"; then
	if sdl-config --version >/dev/null 2>&1; then
		with_sdl=yes
		SDL_CFLAGS=`sdl-config --cflags`
		SDL_LIBS=`sdl-config --libs`
	else
		with_sdl=no
	fi
fi
AM_CONDITIONAL(HAVE_LIBSDL, test "x$with_sdl" = "xyes")
AC_SUBST(SDL_CFLAGS)
AC_SUBST(SDL_LIBS)

MINGW=`uname -s | grep MINGW 2>/dev/null`
AM_CONDITIONAL(MINGW, test ! -z "$MINGW" )
if test ! -z "$MINGW"; then
	WSOCKLIB="-lws2_32"
fi
AC_SUBST(WSOCKLIB)

# Checks for header files.
AC_HEADER_STDC
AC_CHECK_HEADERS([arpa/inet.h fcntl.h netdb.h netinet/in.h stdlib.h string.h sys/socket.h sys/time.h sys/timeb.h syslog.h unistd.h])

# Checks for typedefs, structures, and compiler characteristics.
AC_C_CONST
AC_C_INLINE
AC_C_BIGENDIAN
AC_TYPE_SIZE_T
AC_HEADER_TIME
AC_HEADER_SYS_WAIT
AC_TYPE_SOCKLEN_T
AC_CREATE_STDINT_H(rfb/rfbint.h)

# Checks for library functions.
AC_FUNC_MALLOC
AC_FUNC_MEMCMP
AC_FUNC_STAT
AC_FUNC_STRFTIME
AC_FUNC_VPRINTF
AC_FUNC_FORK
AC_CHECK_LIB(nsl,gethostbyname)
AC_CHECK_LIB(socket,socket)
AC_CHECK_FUNCS([ftime gethostbyname gethostname gettimeofday inet_ntoa memmove memset mkfifo select socket strchr strcspn strdup strerror strstr setsid])

# check, if shmget is in cygipc.a
AC_CHECK_LIB(cygipc,shmget)
AM_CONDITIONAL(CYGIPC, test "$HAVE_CYGIPC" = "true")

# Check if /dev/vcsa1 exists, if so, define LINUX
AM_CONDITIONAL(LINUX, test -c /dev/vcsa1)

# Check for OS X specific header
AC_CHECK_HEADER(ApplicationServices/ApplicationServices.h, HAVE_OSX="true")
AM_CONDITIONAL(OSX, test "$HAVE_OSX" = "true")

# Check for rpm SOURCES path
echo -n "checking for rpm sources path... "
RPMSOURCEDIR="NOT-FOUND"
for directory in packages OpenLinux redhat RedHat rpm RPM "" ; do
	if test -d /usr/src/${directory}/SOURCES; then
		RPMSOURCEDIR="/usr/src/${directory}/SOURCES/"
	fi
done
echo "$RPMSOURCEDIR"
AM_CONDITIONAL(HAVE_RPM, test "$RPMSOURCEDIR" != "NOT-FOUND")
AC_SUBST(RPMSOURCEDIR)

AC_CONFIG_FILES([Makefile
                 libvncserver/Makefile
                 contrib/Makefile
                 x11vnc/Makefile
                 examples/Makefile
		 vncterm/Makefile
		 classes/Makefile
		 libvncclient/Makefile
		 client_examples/Makefile
		 test/Makefile
		 LibVNCServer.spec
		 libvncserver-config])
AC_OUTPUT
chmod a+x ./libvncserver-config

