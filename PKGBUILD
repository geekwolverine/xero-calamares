# Maintainer: Philip Müller <philm[at]manjaro[dog]org>

pkgname=xero-calamares
_pkgname=calamares
pkgver=3.2.61
pkgrel=4
pkgdesc='Distribution-independent installer framework'
arch=('i686' 'x86_64')
license=(GPL)
url="https://github.com/calamares/calamares/releases"
license=('LGPL')
depends=('kconfig' 'kcoreaddons' 'kiconthemes' 'ki18n' 'kio' 'solid' 'yaml-cpp' 'kpmcore>=4.2.0' 'mkinitcpio-openswap'
         'boost-libs' 'ckbcomp' 'hwinfo' 'qt5-svg' 'polkit-qt5' 'gtk-update-icon-cache' 'plasma-framework'
         'qt5-xmlpatterns' 'squashfs-tools' 'libpwquality' 'efibootmgr' 'icu' 'qt5-quickcontrols2')
conflicts=()
makedepends=('extra-cmake-modules' 'qt5-tools' 'qt5-translations' 'git' 'boost')
backup=('usr/share/calamares/modules/bootloader.conf'
        'usr/share/calamares/modules/displaymanager.conf'
        'usr/share/calamares/modules/initcpio.conf'
        'usr/share/calamares/modules/unpackfs.conf')

source=("$_pkgname-$pkgver::$url/download/v$pkgver/$_pkgname-$pkgver.tar.gz"
#	"calamares.desktop"
	"dm_main.py"
	"calamares_polkit"
	"49-nopasswd-calamares.rules")

sha256sums=('7591b9b60738bdba7b9de2b8da5462ab21006db06a006f0dd21ac5b832711dd2'
            '45ae3cdb243e1109a6abd6ed803e39eaec7cf313ed8a88b516a5667f149fdea5'
            'e61245ff7e4c3af6f05a9fe9a3fcf47f2780d9aa88c11eab02a35fac446cf1e1'
            '56d85ff6bf860b9559b8c9f997ad9b1002f3fccc782073760eca505e3bddd176'
            '3457ab03e46dcbb4e5e42c078c3cf0349e1eace31ca4ae6ee6f030810f57c6b8')

pkgver() {
	cd ${srcdir}/$_pkgname-$pkgver
	_ver="$(cat CMakeLists.txt | grep -m3 -e "  VERSION" | grep -o "[[:digit:]]*" | xargs | sed s'/ /./g')"
	_git=".r$(git rev-list --count HEAD).$(git rev-parse --short HEAD)"
	printf '%s%s' "${_ver}" #"${_git}"
}

prepare() {

	sed -i -e 's/"Install configuration files" OFF/"Install configuration files" ON/' "$srcdir/${_pkgname}-${pkgver}/CMakeLists.txt"
	sed -i -e 's/# DEBUG_FILESYSTEMS/DEBUG_FILESYSTEMS/' "$srcdir/${_pkgname}-${pkgver}/CMakeLists.txt"
	sed -i -e 's/Install packages/Updating System (Might Take a While...)/' "$srcdir/${_pkgname}-${pkgver}/src/modules/packages/main.py"
	#sed -i 's/,\ self.line_cb//g'  "$srcdir/${_pkgname}-${pkgver}/src/modules/packages/main.py"

	# add pkgrelease to patch-version
	cd ${_pkgname}-${pkgver}
	_patchver="$(cat CMakeLists.txt | grep -m3 -e CALAMARES_VERSION_PATCH | grep -o "[[:digit:]]*" | xargs)"
	sed -i -e "s|CALAMARES_VERSION_PATCH $_patchver|CALAMARES_VERSION_PATCH $_patchver-$pkgrel|g" CMakeLists.txt
	install -Dm644 "${srcdir}/dm_main.py" "${srcdir}/$_pkgname-$pkgver/src/modules/displaymanager/main.py"

}

build() {
	cd $_pkgname-$pkgver

	mkdir -p build
	cd build
	cmake .. \
	-DCMAKE_BUILD_TYPE=Release \
	-DCMAKE_INSTALL_PREFIX=/usr \
	-DCMAKE_INSTALL_LIBDIR=lib \
	-DWITH_PYTHONQT=OFF \
	-DWITH_KF5DBus=OFF \
	-DBoost_NO_BOOST_CMAKE=ON \
	-DWEBVIEW_FORCE_WEBKIT=OFF \
	-DSKIP_MODULES="webview tracking interactiveterminal initramfs \
	initramfscfg dracut dracutlukscfg \
	dummyprocess dummypython dummycpp \
	dummypythonqt services-openrc"
	make
}

package() {
	cd ${_pkgname}-${pkgver}/build
	make DESTDIR="$pkgdir" install
#	install -Dm644 "${srcdir}/calamares.desktop" "$pkgdir/etc/xdg/autostart/calamares.desktop"
#	install -Dm755 "${srcdir}/calamares.desktop" "$pkgdir/home/liveuser/Desktop/calamares.desktop"
	install -Dm755 "${srcdir}/calamares_polkit" "$pkgdir/usr/bin/calamares_polkit"
	install -Dm644 "${srcdir}/49-nopasswd-calamares.rules" "$pkgdir/etc/polkit-1/rules.d/49-nopasswd-calamares.rules"
	chmod 750 "$pkgdir"/etc/polkit-1/rules.d
}
