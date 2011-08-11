# Maintainer: Dan McGee <dan@archlinux.org>

pkgname=pacman
pkgver=3.5.4
pkgrel=3
pkgdesc="A library-based package manager with dependency support"
arch=('i686' 'x86_64')
url="http://www.archlinux.org/pacman/"
license=('GPL')
groups=('base')
depends=('bash' 'glibc>=2.14' 'libarchive>=2.8.4' 'libfetch>=2.28' 'pacman-mirrorlist')
optdepends=('fakeroot: for makepkg usage as normal user'
            'curl: for rankmirrors usage')
backup=(etc/pacman.conf etc/makepkg.conf)
install=pacman.install
options=(!libtool)
source=(ftp://ftp.archlinux.org/other/pacman/$pkgname-$pkgver.tar.gz
        pacman.conf
        pacman.conf.x86_64
        makepkg.conf
        0001-makepkg-fix-removing-symbolic-link.patch)
md5sums=('2dd3a85d55ee3dd89abd349aa837447c'
         'e99eb721b6b704f68c5f47468507c102'
         'f8b939d9b2beb79a0436961a2d707d7c'
         'a8684989d3dfad5a6e1bcf95af3e571b'
         '749a4ddc6d8418d8d031f1c9b4e09d6f')

# keep an upgrade path for older installations
PKGEXT='.pkg.tar.gz'

build() {
  cd $srcdir/$pkgname-$pkgver

  patch -Np1 < "$srcdir/0001-makepkg-fix-removing-symbolic-link.patch"

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
