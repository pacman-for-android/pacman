# Maintainer: Aaron Griffin <aaron@archlinux.org>
# Maintainer: Dan McGee <dan@archlinux.org>

pkgname=pacman
pkgver=3.2.1
pkgrel=1
pkgdesc="A library-based package manager with dependency support"
arch=('i686' 'x86_64')
url="http://www.archlinux.org/pacman/"
license=('GPL')
groups=('base')
depends=('bash' 'libarchive>=2.5.5' 'libdownload>=1.3')
optdepends=('fakeroot: for makepkg usage as normal user')
backup=(etc/pacman.conf etc/makepkg.conf etc/pacman.d/mirrorlist)
install=pacman.install
options=(!libtool)
source=(ftp://ftp.archlinux.org/other/pacman/$pkgname-$pkgver.tar.gz
        pacman.conf
        mirrorlist)
md5sums=('5b133613cebd74aa8c199de049e25cc4'
         '49b799e923933a30cdefcac06f530fb0'
         'a16981c0fef957a95999550f68cdb0f8')

build() {
  cd $startdir/src/$pkgname-$pkgver
  
  ./configure --prefix=/usr --sysconfdir=/etc --localstatedir=/var
  make || return 1
  make DESTDIR=$startdir/pkg install || return 1

  # install arch specific stuff
  mkdir -p ${startdir}/pkg/etc/pacman.d
  install -m644 ${startdir}/src/pacman.conf ${startdir}/pkg/etc/
  install -m644 ${startdir}/src/mirrorlist ${startdir}/pkg/etc/pacman.d/

  # customize mirrorlist to architecture
  sed -i -e "s/@carch@/${CARCH}/g" ${startdir}/pkg/etc/pacman.d/mirrorlist

  # install completion files
  mkdir -p $startdir/pkg/etc/bash_completion.d/
  install -m644 contrib/bash_completion $startdir/pkg/etc/bash_completion.d/pacman
  mkdir -p $startdir/pkg/usr/share/zsh/site-functions/
  install -m644 contrib/zsh_completion $startdir/pkg/usr/share/zsh/site-functions/_pacman
}

# vim: set ts=2 sw=2 et:
