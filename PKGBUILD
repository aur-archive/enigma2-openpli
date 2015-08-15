# Contributor: Arturo Martinez <martinez(at)embl.de>
pkgname=enigma2-openpli
pkgver=20120204
pkgrel=1
pkgdesc="Enigma2 - software for Linux-powered DVB satellite, terrestrial and cable digital television receivers (e.g. Dreambox). This is Openpli´s experimental version for PC with lirc support"
arch=('i686' 'x86_64')
url="https://github.com/nobody9/openpliPC"
license=('GPL2')
depends=('libnl-2' 'libjpeg-turbo' 'gettext' 'libdvdnav' 'freetype2' 'fribidi' 'giflib' 'gstreamer0.10' 'gstreamer0.10-base-plugins' 'libjpeg6' 'libpng' 'sdl' 'libsigc++' 'libxml2' 'libxslt' 'python2' 'swig' 'libdvbsi++' 'libxmlccwrap' 'libdreamdvd' 'libdvbcsa_4_enigma2' 'xine-lib_for_openpli-e2' 'twisted-web2' 'libsigcpp1.2')
makedepends=('autoconf' 'automake' 'bison' 'fakeroot' 'flex' 'libtool' 'm4' 'make' 'patch' 'pkg-config' 'git' 'linux-headers')
conflicts=()
install="${pkgname}.install"
source=()
#_commit='enigma2'
_gitroot='git://github.com/nobody9/openpliPC'
_gitname='enigma2'

build() {
    cd "${srcdir}"
    msg "Connecting to GIT server...."

    if [ -d "${_gitname}" ] ; then
        cd "${_gitname}" && git checkout "${_commit}" && git pull origin 
        [[ "${_commit}" ]] && git checkout "${_commit}"
        msg "The local files are updated."
        #msg "Running make distclean"
        #make distclean || :
    else
        git clone "${_gitroot}" "${_gitname}" -b "${_commit}"
        cd "${_gitname}"
        [[ "${_commit}" ]] && git checkout "${_commit}"
	fi

    if [ -d "$srcdir/$_gitname-build" ]; then
      rm -r "$srcdir/$_gitname-build"
    fi
    cp -r "$srcdir/$_gitname/enigma2" "$srcdir/$_gitname-build"
    cd "$srcdir/$_gitname-build"
#patch -p1 -i ${srcdir}/0002-support-for-archlinux.patch 
#patch -p1 -i ${srcdir}/enigma2/patches/enigma2_add_lirc_support.patch 

   autoreconf -i || return 1

    # remove "--as-needed" from LDFLAGS
    #unset LDFLAGS; LDFLAGS="-Wl,--hash-style=gnu"

    msg "Configuring Enigma2-openpli" 
    ./configure --without-debug --prefix=/usr/local/e2 --with-xlib || return 1
    cd main 
#    patch -p0 -i ${srcdir}/makefile.patch
    cd ..
    make || return 1
    make install DESTDIR=$pkgdir || return 1
     cp -fR lib/gdi/*.h $pkgdir/usr/local/e2/include/enigma2/lib/gdi
    cd dvbsoftwareca
    make || return 1
    install -D dvbsoftwareca.ko $pkgdir/lib/modules/`uname -r`/kernel/drivers/dvbsoftwareca.ko
    cd $srcdir
    # strip binary
strip $pkgdir/usr/local/e2/bin/enigma2

# removing pre-compiled py files
find $pkgdir/usr/local/e2/lib/enigma2/python/ -name "*.py[oc]" -exec rm {} \;

# copying needed files
mkdir -p $pkgdir/usr/local/e2/etc/enigma2
mkdir -p $pkgdir/usr/local/e2/etc/tuxbox
cp enigma2/share/fonts/* $pkgdir/usr/local/e2/share/fonts
cp -rf enigma2/etc/* $pkgdir/usr/local/e2/etc

ln -sf $pkgdir/usr/local/e2/bin/enigma2 ./e2bin
}
md5sums=()
