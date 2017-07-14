# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgname=npm
pkgver=5.3.0
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
sha512sums=('a48f1becc8bf75142aadc1aea55f9dbb686ebdd764feec06bdcf305575662c9bf2300d3e24bab129738ec8102eb92a94d44cd55997b79c655f435bccc7deda56')

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
  for _d in "$pkgdir"/usr/lib/node_modules/$pkgname/node_modules; do
    cd "$_d"
    for dep in semver; do
      rm -r $dep;
      node "$srcdir"/npm-$pkgver/bin/npm-cli.js link $dep;
    done
  done

  install -Dm644 "$srcdir"/npm-$pkgver/LICENSE "$pkgdir"/usr/share/licenses/$pkgname/LICENSE
}
