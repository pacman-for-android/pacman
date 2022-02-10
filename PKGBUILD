# vim: set ts=2 sw=2 et:
# Maintainer: 

pkgname=pacman
pkgver=6.0.1
pkgrel=3
pkgdesc="A library-based package manager with dependency support"
arch=('x86_64')
url="https://www.archlinux.org/pacman/"
license=('GPL')
groups=('base-devel')
depends=('bash' 'glibc' 'libarchive' 'curl'
         'gpgme' 'pacman-mirrorlist' 'archlinux-keyring')
makedepends=('meson' 'asciidoc' 'doxygen')
checkdepends=('python' 'fakechroot')
optdepends=('perl-locale-gettext: translation support in makepkg-template')
provides=('libalpm.so')
backup=(etc/pacman.conf
        etc/makepkg.conf)
options=('strip' 'debug')
validpgpkeys=('6645B0A8C7005E78DB1D7864F99FFE0FEAE999BD'  # Allan McRae <allan@archlinux.org>
              'B8151B117037781095514CA7BBDFFC92306B1121') # Andrew Gregory (pacman) <andrew@archlinux.org>
source=(https://sources.archlinux.org/other/pacman/$pkgname-$pkgver.tar.xz{,.sig}
        add-flto-to-LDFLAGS-for-clang.patch
        pacman.conf
        makepkg.conf)
sha256sums=('0db61456e56aa49e260e891c0b025be210319e62b15521f29d3e93b00d3bf731'
            'SKIP'
            '82ff91b85f4c6ceba19f9330437e2a22aabc966c2b9e2a20a53857f98a42c223'
            '606e55f06c297d2b508bc4438890b229a1abaa68b0374a2d7f94c8e7be6792d7'
            'a47f9732c295a17cfc02463e7404c8e17a99175a09da15b1e42770d6bd571be7')


prepare() {
  cd "$pkgname-$pkgver"
  patch -Np1 -i ../add-flto-to-LDFLAGS-for-clang.patch
}

build() {
  cd "$pkgname-$pkgver"

  meson --prefix=/usr \
        --buildtype=plain \
        -Ddoc=enabled \
        -Ddoxygen=enabled \
        -Dscriptlet-shell=/usr/bin/bash \
        -Dldconfig=/usr/bin/ldconfig \
        build

  meson compile -C build
}

check() {
  cd "$pkgname-$pkgver"

  meson test -C build
}

package() {
  cd "$pkgname-$pkgver"

  DESTDIR="$pkgdir" meson install -C build

  # install Arch specific stuff
  install -dm755 "$pkgdir/etc"
  install -m644 "$srcdir/pacman.conf" "$pkgdir/etc"
  install -m644 "$srcdir/makepkg.conf" "$pkgdir/etc"
}
