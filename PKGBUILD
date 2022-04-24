# Maintainer: Jose Riha <jose1711 gmail com>
# Maintainer: Sebastian J. Bronner <waschtl@sbronner.com>
# Contributor: Patrick Jackson <PatrickSJackson gmail com>
# Contributor: Christoph Vigano <mail@cvigano.de>

pkgname=st
pkgver=0.8.5
pkgrel=3
pkgdesc='A simple virtual terminal emulator for X.'
arch=('i686' 'x86_64' 'armv7h' 'aarch64')
license=('MIT')
depends=(libxft)
url=https://st.suckless.org
source=(https://dl.suckless.org/$pkgname/$pkgname-$pkgver.tar.gz
		terminfo.patch
		README.terminfo.rst
		https://st.suckless.org/patches/xresources/st-xresources-20200604-9ba7ecf.diff
		https://st.suckless.org/patches/alpha/st-alpha-20220206-0.8.5.diff
		https://st.suckless.org/patches/dynamic-cursor-color/st-dynamic-cursor-color-0.8.4.diff
		https://st.suckless.org/patches/anysize/st-anysize-0.8.4.diff
		https://st.suckless.org/patches/scrollback/st-scrollback-$pkgver.diff
)
sha256sums=('ea6832203ed02ff74182bcb8adaa9ec454c8f989e79232cb859665e2f544ab37'
            'f9deea445a5c6203a0e8e699f3c3b55e27275f17fb408562c4dd5d649edeea23'
            '0ebcbba881832adf9c98ce9fe7667c851d3cc3345077cb8ebe32702698665be2'
            '5be9b40d2b51761685f6503e92028a7858cc6571a8867b88612fce8a70514d5b'
            '42e4803ce2a67835f7e533a707a8a28e3804a26ced163145108970b9aee5fb81'
            'c942f73cd576c2d275dea21a733bc8bcfe66fb186b86563b03d42a123fbe93b8'
            '3851f7919e788cc6667ffdb28ca743188e2869a15f3fc34a8c0b39108d946ef0'
            'dc7f5223b26fc813d91d4ae35bdaa54d63024cae9f18afd9b3594ba3399dfa55')
_sourcedir=$pkgname-$pkgver
_makeopts="--directory=$_sourcedir"

prepare() {
	cd "$srcdir/$pkgname-$pkgver"

	patch --input "$srcdir/terminfo.patch" --strip=0

	patch --input "$srcdir/st-scrollback-$pkgver.diff" || echo

	patch --input "$srcdir/st-alpha-20220206-0.8.5.diff" || echo
	patch --input "$srcdir/st-xresources-20200604-9ba7ecf.diff" || echo
	patch --input "$srcdir/st-dynamic-cursor-color-0.8.4.diff" || echo
	patch --input "$srcdir/st-anysize-0.8.4.diff" || echo

	# This package provides a mechanism to provide a custom config.h. Multiple
	# configuration states are determined by the presence of two files in
	# $BUILDDIR:
	#
	# config.h  config.def.h  state
	# ========  ============  =====
	# absent    absent        Initial state. The user receives a message on how
	#                         to configure this package.
	# absent    present       The user was previously made aware of the
	#                         configuration options and has not made any
	#                         configuration changes. The package is built using
	#                         default values.
	# present                 The user has supplied his or her configuration. The
	#                         file will be copied to $srcdir and used during
	#                         build.
	#
	# After this test, config.def.h is copied from $srcdir to $BUILDDIR to
	# provide an up to date template for the user.
	if [ -e "$BUILDDIR/config.h" ]
	then
		cp "$BUILDDIR/config.h" .
	elif [ ! -e "$BUILDDIR/config.def.h" ]
	then
		msg='This package can be configured in config.h. Copy the config.def.h '
		msg+='that was just placed into the package directory to config.h and '
		msg+='modify it to change the configuration. Or just leave it alone to '
		msg+='continue to use default values.'
		echo "$msg"
	fi
	echo $BUILDDIR
	cp config.def.h "$BUILDDIR"
}

build() {
	make $_makeopts X11INC=/usr/include/X11 X11LIB=/usr/lib/X11
}

package() {
	local installopts='--mode 0644 -D --target-directory'
	local shrdir="$pkgdir/usr/share"
	local licdir="$shrdir/licenses/$pkgname"
	local docdir="$shrdir/doc/$pkgname"
	make $_makeopts PREFIX=/usr DESTDIR="$pkgdir" install
	install $installopts "$licdir" "$_sourcedir/LICENSE"
	install $installopts "$docdir" "$_sourcedir/README"
	install $installopts "$docdir" README.terminfo.rst
	install $installopts "$shrdir/$pkgname" "$_sourcedir/st.info"
}
