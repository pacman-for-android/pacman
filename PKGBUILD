# vim: set ts=2 sw=2 et:
# Maintainer: Levente Polyak <anthraxx[at]archlinux[dot]org>
# Maintainer: Morten Linderud <foxboron@archlinux.org>

pkgname=pacman
pkgver=6.0.2
pkgrel=7.4
pkgdesc="A library-based package manager with dependency support"
arch=('x86_64' 'aarch64')
url="https://www.archlinux.org/pacman/"
license=('GPL')
depends=('bash' 'glibc' 'libarchive' 'curl' 'gpgme' 'pacman-mirrorlist'
         'gettext' 'gawk' 'coreutils' 'gnupg' 'grep')
makedepends=('meson' 'asciidoc' 'doxygen' 'git')
checkdepends=('python' 'fakechroot')
optdepends=('perl-locale-gettext: translation support in makepkg-template')
provides=('libalpm.so')
backup=(data/etc/pacman.conf
        data/etc/makepkg.conf)
options=('strip')
validpgpkeys=('6645B0A8C7005E78DB1D7864F99FFE0FEAE999BD'  # Allan McRae <allan@archlinux.org>
              'B8151B117037781095514CA7BBDFFC92306B1121') # Andrew Gregory (pacman) <andrew@archlinux.org>
source=(git+https://gitlab.archlinux.org/pacman/pacman#commit=2c45e854ab405101fc13f6bd553a0ce099ecab6f
        pacman.conf
        makepkg.conf
        "0001-change-prefix.patch" "0002-no-etc-mtab.patch" "0003-makepkg-fixes.patch" "0004-use-data-local-tmp-instead-of-tmp.patch")
sha256sums=('SKIP'
            '95a7a6c69387199f283cd00ee71238574b2c1c9e3dc83871c261e4003d323797'
            '74cbbdd0adcde4b5b329081c4a6ca84b0bf553b64f779840e143e470551dbaf7'
            '92c769a573bb659721bdb382a1ca84950e53feed79d562a749bdc3b63b849bc4'
            '61f906b61f7346425a2b0fd5c3f16e66646aa939f98944a6d5bbefb6d4a5fa5a'
            'bef592168aeb771097ba1b107365d2729ae442e00b930857b29d554d09e1bf23'
            'd68ba4a58e67aa5745a848e9cb10020535455bb7a7170d8e383df8426f359360')

prepare() {
  cd "${pkgname}"
  # we backport way too often in pacman
  # lets at least make it more convenient
  local src
  for src in "${source[@]}"; do
    src="${src%%::*}"
    src="${src##*/}"
    [[ $src = *.patch ]] || continue
    msg2 "Applying patch $src..."
    patch -Np1 < "../$src"
  done

  sed -i '1s|.*|#!/data/usr/bin/sh|' build-aux/{update-copyright,meson-make-symlink.sh}
}

build() {
  cd "$pkgname"

  meson --prefix=/data/usr \
        --buildtype=plain \
        -Ddoc=enabled \
        -Ddoxygen=enabled \
        -Dscriptlet-shell=/data/usr/bin/bash \
        -Dldconfig=/data/usr/bin/ldconfig \
        -Droot-dir=/ \
        -Dsysconfdir=/data/etc \
        -Dlocalstatedir=/data/var \
        build

  meson compile -C build
}

check() {
  cd "$pkgname"

  meson test -C build
}

package() {
  cd "$pkgname"

  DESTDIR="$pkgdir" meson install -C build

  # install Arch specific stuff
  install -Ddm755 "$pkgdir/data/etc"
  install -m644 "$srcdir/pacman.conf" "$pkgdir/data/etc"
  install -m644 "$srcdir/makepkg.conf" "$pkgdir/data/etc"
}
