# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgname=npm
pkgver=5.8.0
pkgrel=1
pkgdesc='A package manager for javascript'
arch=('any')
url='https://www.npmjs.com/'
license=('custom:Artistic')
depends=('nodejs' 'node-gyp' 'semver')
makedepends=('procps-ng' 'marked-man')
options=('!emptydirs')
source=("$pkgname-$pkgver.tar.gz::https://github.com/npm/npm/archive/v$pkgver.tar.gz")
sha512sums=('63864ad8e6a6204f010bbe1ce728df00f97f1ef0c7e6bff9b59faec1d200eb1ea8687ffb42d032d040fe6e12a50756dacb50191e8d03687ed9e2f4d38013f35b')

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

  # Why 777? :/
  chmod -R u=rwX,go=rX "$pkgdir"

  # Experimental dedup
  rm -r "$pkgdir"/usr/lib/node_modules/$pkgname/node_modules/semver
  rm -r "$pkgdir"/usr/lib/node_modules/$pkgname/node_modules/npm-lifecycle/node_modules/node-gyp

  install -dm755 "$pkgdir"/usr/share/bash-completion/completions
  node "$srcdir"/npm-$pkgver/bin/npm-cli.js completion > "$pkgdir"/usr/share/bash-completion/completions/npm

  install -Dm644 "$srcdir"/npm-$pkgver/LICENSE "$pkgdir"/usr/share/licenses/$pkgname/LICENSE
}
