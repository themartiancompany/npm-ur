# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgname=npm
pkgver=5.5.1
pkgrel=2
pkgdesc='A package manager for javascript'
arch=('any')
url='https://www.npmjs.com/'
license=('custom:Artistic')
depends=('nodejs' 'semver')
provides=('nodejs-node-gyp')
makedepends=('procps-ng' 'marked-man')
optdepends=('python2: for node-gyp')
options=('!emptydirs')
source=("$pkgname-$pkgver.tar.gz::https://github.com/npm/npm/archive/v$pkgver.tar.gz"
        https://github.com/nodejs/node/commit/9f33a248b37ed5acb31cffe2483d5dfc3db89521.patch
        https://github.com/nodejs/node/commit/b8888f5aa2deb41b7ab03d3085124eef398e461e.patch)
sha512sums=('e1839f089784f0d40b94720fb06dafd8a84247e69d65b45d79e5e67ecd69ee8c9fbb5284bcc7ea9104c91267a069c8e5c8f5abb8f6061448faeddd2c70c0bb3c'
            '1e504615ef37c3d83f1ee76995a7ebc35a31e45db55f38e0f807cd900d4acd9dac6ae439368b723caa5820d3807b028a966beb092f265eece6961478cbb65a37'
            '17c22de3636978cf49722aca793d7660225bf1bbcc11e45ed1287de475023d6f032d74f2ca000ae9a92d308e6faa99a3c62e2be625f9f92da8883996ab6f5134')

prepare() {
  cd npm-$pkgver
  ln -sf /usr/bin/marked{,-man} node_modules/.bin/

  # backported nodejs 9.x support from node repo
  patch -p3 -i ../9f33a248b37ed5acb31cffe2483d5dfc3db89521.patch
  patch -p3 -i ../b8888f5aa2deb41b7ab03d3085124eef398e461e.patch
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
