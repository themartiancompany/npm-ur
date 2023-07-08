# Maintainer: Felix Yan <felixonmars@archlinux.org>

pkgname=npm
pkgver=8.19.2
pkgrel=3
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
sha512sums=('5133c15d6564d3eee8f0dba2ca95a535c3c9096111f2c0c00cfd830d3b3915b906624ffe3d6c4de02c2c06c4c8e4f8b5f435a8697e843eb6c29eb7b758e403b7')

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

  # Workaround for https://github.com/npm/cli/issues/780
  cd man
  local f name sec title
  for f in man5/folders.5 man5/install.5 man7/*.7; do
    sec=${f##*.}
    name=$(basename "$f" ."$sec")
    title=$(echo "$name" | tr '[:lower:]' '[:upper:]')

    sed -Ei "s/^\.TH \"$title\"/.TH \"NPM-$title\"/" "$f"
    mv "$f" "${f%/*}/npm-$name.$sec"
  done
}

package() {
  cd cli-$pkgver
  node bin/npm-cli.js install -g -f --prefix="$pkgdir/usr" "$(node bin/npm-cli.js pack | tail -1)"

  # npm installs package.json owned by build user
  # https://bugs.archlinux.org/task/63396
  chown -R root:root "$pkgdir"

  # Experimental dedup
  local _npmdir="$pkgdir"/usr/lib/node_modules/$pkgname
  rm -r "$_npmdir"/node_modules/{,.bin/}semver
  rm -r "$_npmdir"/node_modules/{,.bin/}node-gyp
  rm -r "$_npmdir"/node_modules/{,.bin/}nopt
  sed -i 's|../../node_modules/node-gyp/bin/node-gyp.js|../../../node-gyp/bin/node-gyp.js|' \
    "$_npmdir"/bin/node-gyp-bin/node-gyp

  install -d "$pkgdir"/usr/share/bash-completion/completions
  node bin/npm-cli.js completion > "$pkgdir"/usr/share/bash-completion/completions/npm

  # Support both `man` and `npm help`
  for mandir in "$pkgdir"/usr/lib/node_modules/npm/man/man?; do
    local dst="$pkgdir"/usr/share/man/"$(basename "$mandir")"
    gzip "$mandir"/*
    install -d "$dst"
    ln -r -s "$mandir"/* "$dst"
  done

  install -Dm644 LICENSE -t "$pkgdir"/usr/share/licenses/$pkgname/
}
