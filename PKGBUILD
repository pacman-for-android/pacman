# vim: set ts=2 sw=2 et:
# Maintainer: 

pkgname=pacman
pkgver=5.2.2
pkgrel=4
pkgdesc="A library-based package manager with dependency support"
arch=('x86_64')
url="https://www.archlinux.org/pacman/"
license=('GPL')
groups=('base-devel')
depends=('bash' 'glibc' 'libarchive' 'curl'
         'gpgme' 'pacman-mirrorlist' 'archlinux-keyring')
makedepends=('asciidoc')
checkdepends=('python' 'fakechroot')
optdepends=('perl-locale-gettext: translation support in makepkg-template')
provides=('libalpm.so')
backup=(etc/pacman.conf
        etc/makepkg.conf)
options=('strip' 'debug')
validpgpkeys=('6645B0A8C7005E78DB1D7864F99FFE0FEAE999BD'  # Allan McRae <allan@archlinux.org>
              'B8151B117037781095514CA7BBDFFC92306B1121') # Andrew Gregory (pacman) <andrew@archlinux.org>
source=(https://sources.archlinux.org/other/pacman/$pkgname-$pkgver.tar.gz{,.sig}
        pacman.conf
        makepkg.conf
        pacman-5.2.2-fix-strip-messing-up-file-attributes.patch::'https://git.archlinux.org/pacman.git/patch/?id=88d054093c1c99a697d95b26bd9aad5bc4d8e170'
	pacman-5.2.2-fix-debug-packages-with-gcc11.patch::'https://git.archlinux.org/pacman.git/patch/?id=bdf6aa3fb757a2363a4e708174b7d23a4997763d')  
sha256sums=('bb201a9f2fb53c28d011f661d50028efce6eef2c1d2a36728bdd0130189349a0'
            'SKIP'
            '3353f363088c73f1f86a890547c0f87c7473e5caf43bbbc768c2e9a7397f2aa2'
            '46a7fda0f2ebd2957db1c0264ed8a659df0916ba30e1353ecc70ef47d2b118ff'
            '871fd97b3f13f1718358e4b8e046a56c0262c9042b5e3b5d60835606735798bd'
            '6be31dd7f4e1645e58c26fafaf1d9df4ba5e31b629fc3e8f4070d771571d0011')


prepare() {
  cd "$pkgname-$pkgver"
  patch -Np1 < "$srcdir"/pacman-5.2.2-fix-strip-messing-up-file-attributes.patch
  patch -Np1 < "$srcdir"/pacman-5.2.2-fix-debug-packages-with-gcc11.patch
}

build() {
  cd "$pkgname-$pkgver"

  ./configure --prefix=/usr --sysconfdir=/etc \
    --localstatedir=/var --enable-doc \
    --with-scriptlet-shell=/usr/bin/bash \
    --with-ldconfig=/usr/bin/ldconfig
  make V=1
}

check() {
  make -C "$pkgname-$pkgver" check
}

package() {
  cd "$pkgname-$pkgver"

  make DESTDIR="$pkgdir" install

  # install Arch specific stuff
  install -dm755 "$pkgdir/etc"
  install -m644 "$srcdir/pacman.conf" "$pkgdir/etc"
  install -m644 "$srcdir/makepkg.conf" "$pkgdir/etc"
}
