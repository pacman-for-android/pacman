# vim: set ts=2 sw=2 et:
# Maintainer: Dan McGee <dan@archlinux.org>
# Maintainer: Dave Reisner <dreisner@archlinux.org>

pkgname=pacman
pkgver=4.1.0
pkgrel=1
pkgdesc="A library-based package manager with dependency support"
arch=('i686' 'x86_64')
url="http://www.archlinux.org/pacman/"
license=('GPL')
groups=('base' 'base-devel')
depends=('bash' 'glibc>=2.15' 'libarchive>=3.1.2' 'curl>=7.19.4'
         'gpgme' 'pacman-mirrorlist' 'archlinux-keyring')
makedepends=('asciidoc')
checkdepends=('fakechroot')
optdepends=('fakeroot: for makepkg usage as normal user')
backup=(etc/pacman.conf etc/makepkg.conf)
install=pacman.install
options=(!libtool)
source=(ftp://ftp.archlinux.org/other/pacman/$pkgname-$pkgver.tar.gz{,.sig}
        pacman.conf.i686
        pacman.conf.x86_64
        makepkg.conf)
md5sums=('a0f2b3148bee4784f21cf373cf59a0bc'
         '4959b8d00056398195f4e549e7bdd346'
         '2db6c94709bb30cc614a176ecf8badb1'
         'de74a13618347f08ae4a9637f74471c4'
         '2ea6f0a5badef735ec7d30a372f5e9c6')

build() {
  cd $srcdir/$pkgname-$pkgver

  ./configure --prefix=/usr --sysconfdir=/etc \
    --localstatedir=/var --enable-doc
  make
  make -C contrib
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
      install -m644 $srcdir/pacman.conf.i686 $pkgdir/etc/pacman.conf
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
