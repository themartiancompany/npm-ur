# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgname=npm
pkgver=8.1.0
pkgrel=1
pkgdesc='A package manager for javascript'
arch=('any')
url='https://www.npmjs.com/'
license=('custom:Artistic')
depends=('nodejs' 'node-gyp' 'nodejs-nopt' 'semver')
# libgl: TODO
# libvips: for sharp (doc build) (disabled as current version of gatsby imports a broken sharp)
# libxi: for cwebp (doc build)
makedepends=('libgl' 'libxi' 'marked' 'marked-man' 'procps-ng' 'python')
options=('!emptydirs')
source=("$pkgname-$pkgver.tar.gz::https://github.com/npm/cli/archive/v$pkgver.tar.gz")
sha512sums=('151d65f04e25720109fe8033e85350f807bf4e29431a739a2dda86a0b60bce4142d202a2656da66e4718763443b9acdcc94184f5c626d94e53be9581357b0a8f')

prepare() {
  cd cli-$pkgver
  mkdir -p node_modules/.bin
  ln -sf /usr/bin/marked{,-man} node_modules/.bin/

  # Use local marked/marked-man
  sed -i 's|node bin/npm-cli.js install marked|true |' Makefile

  # Don't build twice
  sed -i 's/install: all/install:/' Makefile

  mkdir -p man/man1
}

build() {
  cd cli-$pkgver
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

  install -Dm644 "$srcdir"/cli-$pkgver/LICENSE "$pkgdir"/usr/share/licenses/$pkgname/LICENSE
}
