# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgname=npm
pkgver=8.14.0
pkgrel=1
pkgdesc='A package manager for javascript'
arch=('any')
url='https://www.npmjs.com/'
license=('custom:Artistic')
depends=('nodejs' 'node-gyp' 'nodejs-nopt' 'semver')
# libgl: TODO
# libvips: for sharp (doc build) (disabled as current version of gatsby imports a broken sharp)
# libxi: for cwebp (doc build)
makedepends=('libgl' 'libxi' 'procps-ng' 'python')
options=('!emptydirs')
source=("https://github.com/npm/cli/archive/v$pkgver/$pkgname-$pkgver.tar.gz")
sha512sums=('596e167f712fde7b172a8a91bd69c108dc4675e1cb08ae80783284b3b190ae478e790e149b8fea763b6f83e36603d481b4e72a42b020db7cb5ccdcad475cb8a1')

prepare() {
  cd cli-$pkgver
  # 'deps' was added as a dep for many Makefile targets since 8.6 but it's not easy to fix.
  # Let's skip and do it ourselves instead.
  sed -i 's|node bin/npm-cli.js run resetdeps|true|' Makefile
}

build() {
  cd cli-$pkgver
  node . i --ignore-scripts --no-audit --no-fund
  NODE_PATH=/usr/lib/node_modules make
}

package() {
  cd cli-$pkgver
  node bin/npm-cli.js install -g -f --prefix="$pkgdir/usr" $(node bin/npm-cli.js pack | tail -1)

  # Non-deterministic race in npm gives 777 permissions to random directories.
  # See https://github.com/npm/npm/issues/9359 for details.
  chmod -R u=rwX,go=rX "$pkgdir"

  # npm installs package.json owned by build user
  # https://bugs.archlinux.org/task/63396
  chown -R root:root "$pkgdir"

  # Experimental dedup
  _npmdir="$pkgdir"/usr/lib/node_modules/$pkgname
  rm -r "$_npmdir"/node_modules/{,.bin/}semver
  rm -r "$_npmdir"/node_modules/{,.bin/}node-gyp
  rm -r "$_npmdir"/node_modules/{,.bin/}nopt
  sed -i 's|../../node_modules/node-gyp/bin/node-gyp.js|../../../node-gyp/bin/node-gyp.js|' \
    "$_npmdir"/bin/node-gyp-bin/node-gyp

  install -dm755 "$pkgdir"/usr/share/bash-completion/completions
  node "$srcdir"/cli-$pkgver/bin/npm-cli.js completion > "$pkgdir"/usr/share/bash-completion/completions/npm

  mv "$pkgdir"/usr/lib/node_modules/npm/man "$pkgdir"/usr/share/

  install -Dm644 "$srcdir"/cli-$pkgver/LICENSE -t "$pkgdir"/usr/share/licenses/$pkgname/
}
