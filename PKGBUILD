# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Maintainer: Daniel M. Capella <polyzen@archlinux.org>

pkgname=npm
pkgver=9.8.0
pkgrel=2
pkgdesc='A package manager for JavaScript'
arch=('any')
url='https://www.npmjs.com/'
license=('custom:Artistic')
depends=('nodejs' 'node-gyp' 'nodejs-nopt' 'semver')
optdepends=("git: for dependencies using Git URL's")
# libgl: TODO
# libvips: for sharp (doc build) (disabled as current version of gatsby imports a broken sharp)
# libxi: for cwebp (doc build)
source=("https://registry.npmjs.org/npm/-/npm-$pkgver.tgz")
b2sums=('b2042e83b9fd249a15c84b0ab42cdcfdddc334f701ad2dcd91ec6116129158b8bc5dabd5dcd81d44d9d8c7204b35051e97ca8f18cf589adb94004693bcd55f8f')

prepare() {
  # Workaround for https://github.com/npm/cli/issues/780
  cd package/man
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
  local _npmdir=/usr/lib/node_modules/$pkgname
  install -d "$pkgdir"/{usr/bin,usr/share/bash-completion/completions,$_npmdir}
  ln -s $_npmdir/bin/npm-cli.js "$pkgdir"/usr/bin/npm
  ln -s $_npmdir/bin/npx-cli.js "$pkgdir"/usr/bin/npx
  echo 'globalconfig=/etc/npmrc' > "$pkgdir"/$_npmdir/npmrc

  cd package
  cp -r bin index.js lib man node_modules package.json "$pkgdir"/$_npmdir
  node bin/npm-cli.js completion > "$pkgdir"/usr/share/bash-completion/completions/npm
  install -Dm644 LICENSE -t "$pkgdir"/usr/share/licenses/$pkgname/

  cd "$pkgdir"
  rm ./$_npmdir/bin/np{m,x}.{cmd,ps1}

  # Experimental dedup
  rm -r ./$_npmdir/node_modules/{node-gyp,nopt,semver}

  # Support both `man` and `npm help`
  for mandir in ./"$_npmdir"/man/man?; do
    local dst=usr/share/man/"$(basename "$mandir")"
    gzip "$mandir"/*
    install -d "$dst"
    ln -r -s "$mandir"/* "$dst"
  done
}
