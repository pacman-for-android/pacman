# vim: set ts=2 sw=2 et:
# Maintainer: Dan McGee <dan@archlinux.org>
# Maintainer: Dave Reisner <dreisner@archlinux.org>

pkgname=pacman
pkgver=4.1.1
pkgrel=1
pkgdesc="A library-based package manager with dependency support"
arch=('i686' 'x86_64')
url="http://www.archlinux.org/pacman/"
license=('GPL')
groups=('base' 'base-devel')
depends=('bash>=4.2.042-2' 'glibc>=2.17-2' 'libarchive>=3.1.2' 'curl>=7.19.4'
         'gpgme' 'pacman-mirrorlist' 'archlinux-keyring')
checkdepends=('python2' 'fakechroot')
optdepends=('fakeroot: for makepkg usage as normal user')
provides=('pacman-contrib')
conflicts=('pacman-contrib')
replaces=('pacman-contrib')
backup=(etc/pacman.conf etc/makepkg.conf)
install=pacman.install
options=(!libtool)
source=(ftp://ftp.archlinux.org/other/pacman/$pkgname-$pkgver.tar.gz{,.sig}
        pacman.conf.i686
        pacman.conf.x86_64
        makepkg.conf)
md5sums=('72a1688ff73c1c0b79d8f87b3793d446'
         'SKIP'
         '2db6c94709bb30cc614a176ecf8badb1'
         'de74a13618347f08ae4a9637f74471c4'
         '5f360e003e95f1a5cbd6e60d937dfd25')

build() {
  cd $srcdir/$pkgname-$pkgver

  ./configure --prefix=/usr --sysconfdir=/etc \
    --localstatedir=/var --enable-doc \
    --with-scriptlet-shell=/usr/bin/bash \
    --with-ldconfig=/usr/bin/ldconfig
  make
  make -C contrib
}

check() {
  make -C "$pkgname-$pkgver" check
}

package() {
  cd $srcdir/$pkgname-$pkgver
  make DESTDIR=$pkgdir install
  make DESTDIR=$pkgdir -C contrib install

  # install Arch specific stuff
  install -dm755 $pkgdir/etc
  install -m644 $srcdir/pacman.conf.$CARCH $pkgdir/etc/pacman.conf
  
  case "$CARCH" in
    i686)    
      mycarch="i686"
      mychost="i686-pc-linux-gnu"
      myflags="-march=i686"
      ;;
    x86_64)
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
    
  # put bash_completion in the right location
  install -dm755 ${pkgdir}/usr/share/bash-completion/completions
  mv ${pkgdir}/etc/bash_completion.d/pacman \
    ${pkgdir}/usr/share/bash-completion/completions
  rmdir ${pkgdir}/etc/bash_completion.d

  for f in makepkg pacman-key; do
    ln -s pacman "$pkgdir/usr/share/bash-completion/completions/$f"
  done
}
