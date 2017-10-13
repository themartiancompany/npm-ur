# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgname=npm
pkgver=5.5.1
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
sha512sums=('e1839f089784f0d40b94720fb06dafd8a84247e69d65b45d79e5e67ecd69ee8c9fbb5284bcc7ea9104c91267a069c8e5c8f5abb8f6061448faeddd2c70c0bb3c')

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
