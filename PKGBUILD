# Maintainer: Dan McGee <dan@archlinux.org>
# Maintainer: Dave Reisner <dave@archlinux.org>

pkgname=pacman
pkgver=4.0.0
pkgrel=1
pkgdesc="A library-based package manager with dependency support"
arch=('i686' 'x86_64')
url="http://www.archlinux.org/pacman/"
license=('GPL')
groups=('base')
depends=('bash' 'glibc>=2.14' 'libarchive>=2.8.4' 'curl>=7.19.4'
         'gpgme' 'pacman-mirrorlist')
optdepends=('fakeroot: for makepkg usage as normal user'
            'curl: for rankmirrors usage')
backup=(etc/pacman.conf etc/makepkg.conf)
install=pacman.install
options=(!libtool)
source=(ftp://ftp.archlinux.org/other/pacman/$pkgname-$pkgver.tar.gz
        pacman.conf
        pacman.conf.x86_64
        makepkg.conf)
md5sums=('8c97f4804d2a0847956c45888b0ea517'
         'ae4abf6df12483d26b39529ab27c0837'
         '99d8fae7a3c761e3ebad800f6a216e83'
         'db051afbd12993b7743ccd4d58668499')

# keep an upgrade path for older installations
PKGEXT='.pkg.tar.gz'

build() {
  cd $srcdir/$pkgname-$pkgver

  ./configure --prefix=/usr --sysconfdir=/etc \
    --localstatedir=/var --enable-doc
  make
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
      myflags="-march=i686 "
      ;;
    x86_64)
      install -m644 $srcdir/pacman.conf.x86_64 $pkgdir/etc/pacman.conf
      mycarch="x86_64"
      mychost="x86_64-unknown-linux-gnu"
      myflags="-march=x86-64 "
      ;;
  esac
  install -m644 $srcdir/makepkg.conf $pkgdir/etc/
  # set things correctly in the default conf file
  sed -i $pkgdir/etc/makepkg.conf \
    -e "s|@CARCH[@]|$mycarch|g" \
    -e "s|@CHOST[@]|$mychost|g" \
    -e "s|@CARCHFLAGS[@]|$myflags|g"

  # install completion files
  mkdir -p $pkgdir/etc/bash_completion.d/
  install -m644 contrib/bash_completion $pkgdir/etc/bash_completion.d/pacman
  mkdir -p $pkgdir/usr/share/zsh/site-functions/
  install -m644 contrib/zsh_completion $pkgdir/usr/share/zsh/site-functions/_pacman
}

# vim: set ts=2 sw=2 et:
