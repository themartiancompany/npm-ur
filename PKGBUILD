# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgname=npm
pkgver=6.14.9
pkgrel=1
pkgdesc='A package manager for javascript'
arch=('any')
url='https://www.npmjs.com/'
license=('custom:Artistic')
depends=('nodejs' 'node-gyp' 'semver')
# libgl: TODO
# libvips: for sharp (doc build) (disabled as current version of gatsby imports a broken sharp)
# libxi: for cwebp (doc build)
makedepends=('libgl' 'libxi' 'marked' 'marked-man' 'procps-ng' 'python')
options=('!emptydirs')
source=("$pkgname-$pkgver.tar.gz::https://github.com/npm/cli/archive/v$pkgver.tar.gz")
sha512sums=('ec9fa70158f8349a3c1c4613e84973e4ce2feaf5708e44b612409187fb54ab26aef2debbf150a0f07c0fe0d1f111cd7dfdad517b0253114bfc0c747d2a2a6568')

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
  make NPMOPTS="--prefix=\"$pkgdir/usr\"" install

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
  sed -i '/node-gyp.js/c\  exec /usr/bin/node-gyp "$@"' \
    "$_npmdir"/node_modules/npm-lifecycle/node-gyp-bin/node-gyp \
    "$_npmdir"/bin/node-gyp-bin/node-gyp

  install -dm755 "$pkgdir"/usr/share/bash-completion/completions
  node "$srcdir"/cli-$pkgver/bin/npm-cli.js completion > "$pkgdir"/usr/share/bash-completion/completions/npm

  install -Dm644 "$srcdir"/cli-$pkgver/LICENSE "$pkgdir"/usr/share/licenses/$pkgname/LICENSE
}
