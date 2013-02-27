# vim: set ts=2 sw=2 et:
# Maintainer: Dan McGee <dan@archlinux.org>
# Maintainer: Dave Reisner <dreisner@archlinux.org>

pkgname=pacman
pkgver=4.0.3
pkgrel=6
pkgdesc="A library-based package manager with dependency support"
arch=('i686' 'x86_64')
url="http://www.archlinux.org/pacman/"
license=('GPL')
groups=('base' 'base-devel')
depends=('bash' 'glibc>=2.15' 'libarchive>=3.0.2' 'curl>=7.19.4'
         'gpgme' 'pacman-mirrorlist' 'archlinux-keyring')
makedepends=('asciidoc')
optdepends=('fakeroot: for makepkg usage as normal user')
backup=(etc/pacman.conf etc/makepkg.conf)
install=pacman.install
options=(!libtool)
source=(ftp://ftp.archlinux.org/other/pacman/$pkgname-$pkgver.tar.gz{,.sig}
        0001-Add-conflict-for-replacing-owned-empty-directory.patch
        0002-Check-empty-subdirectory-ownership.patch
        0003-Ensure-pre_upgrade-scriptlet-gets-old-package-version.patch
        pacman.conf
        pacman.conf.x86_64
        makepkg.conf)
md5sums=('387965c7125e60e5f0b9ff3b427fe0f9'
         '1a70392526c8768470da678b31905a6e'
         '1a9b79788640907a2b34e8671cacc94a'
         'a9ddd43891bed364e1e97d27b2887bf1'
         '2e8cbf55a94b1954b167c5dee6b62317'
         '99734ea46795f466d41c503e9e23b6d4'
         '556d49489e82b5750cf026d3b18c8f4f'
         '589cd34eb9d5b678455e8289394f523e')

build() {
  cd $srcdir/$pkgname-$pkgver

  patch -p1 -i $srcdir/0001-Add-conflict-for-replacing-owned-empty-directory.patch
  patch -p1 -i $srcdir/0002-Check-empty-subdirectory-ownership.patch
  patch -p1 -i $srcdir/0003-Ensure-pre_upgrade-scriptlet-gets-old-package-version.patch

  ./configure --prefix=/usr --sysconfdir=/etc \
    --localstatedir=/var --enable-doc
  make
}

check() {
  make -C "$pkgname-$pkgver" check
}

package() {
  cd $srcdir/$pkgname-$pkgver
  make DESTDIR=$pkgdir install

  # install Arch specific stuff
  mkdir -p $pkgdir/etc
  case "$CARCH" in
    i686)
      install -m644 $srcdir/pacman.conf $pkgdir/etc/pacman.conf
      mycarch="i686"
      mychost="i686-pc-linux-gnu"
      myflags="-march=i686"
      ;;
    x86_64)
      install -m644 $srcdir/pacman.conf.x86_64 $pkgdir/etc/pacman.conf
      mycarch="x86_64"
      mychost="x86_64-unknown-linux-gnu"
      myflags="-march=x86-64"
      ;;
  esac
  install -m644 $srcdir/makepkg.conf $pkgdir/etc/
  # set things correctly in the default conf file
  sed -i $pkgdir/etc/makepkg.conf \
    -e "s|@CARCH[@]|$mycarch|g" \
    -e "s|@CHOST[@]|$mychost|g" \
    -e "s|@CARCHFLAGS[@]|$myflags|g"

  # install completion files
  install -Dm644 contrib/bash_completion "$pkgdir/usr/share/bash-completion/completions/pacman"
  for f in makepkg pacman-key; do
    ln -s pacman "$pkgdir/usr/share/bash-completion/completions/$f"
  done

  install -Dm644 contrib/zsh_completion $pkgdir/usr/share/zsh/site-functions/_pacman
}
