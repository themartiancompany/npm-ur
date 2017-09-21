# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgname=npm
pkgver=5.4.2
pkgrel=1
pkgdesc='A package manager for javascript'
arch=('any')
url='https://www.npmjs.com/'
license=('custom:Artistic')
depends=('nodejs' 'semver')
provides=('nodejs-node-gyp')
makedepends=('procps-ng' 'marked-man')
optdepends=('python2: for node-gyp')
options=('!emptydirs')
source=("$pkgname-$pkgver.tar.gz::https://github.com/npm/npm/archive/v$pkgver.tar.gz")
sha512sums=('c664550567d6f60f2c807355a890e58bbac9b82eb3730f77937b91fbf7f0e354b62533253fef829b3c4f9fd41bae8b46b24a7f286ec80f26a5948a0b79ffe637')

prepare() {
  cd npm-$pkgver
  ln -sf /usr/bin/marked{,-man} node_modules/.bin/
}

build() {
  cd npm-$pkgver
  make
}

package() {
  cd npm-$pkgver
  make NPMOPTS="--prefix=\"$pkgdir/usr\"" install

  # Provide node-gyp executable
  cp "$pkgdir"/usr/lib/node_modules/npm/bin/node-gyp-bin/node-gyp "$pkgdir"/usr/bin/node-gyp
  sed -i 's|"`dirname "$0"`/../../|"`dirname "$0"`/../lib/node_modules/npm/|' "$pkgdir"/usr/bin/node-gyp

  # Why 777? :/
  chmod -R u=rwX,go=rX "$pkgdir"

  # Experimental dedup
  for _d in "$pkgdir"/usr/lib/node_modules/$pkgname/node_modules \
            "$pkgdir"/usr/lib/node_modules/$pkgname/node_modules/node-gyp/node_modules; do
    cd "$_d"
    for dep in semver; do
      rm -r $dep;
    done
  done

  install -Dm644 "$srcdir"/npm-$pkgver/LICENSE "$pkgdir"/usr/share/licenses/$pkgname/LICENSE
}
