# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Felix Yan <felixonmars@archlinux.org>
# Maintainer: Daniel M. Capella <polyzen@archlinux.org>
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>

pkgname=npm
pkgver=10.4.0
pkgrel=1
pkgdesc='A package manager for JavaScript'
arch=('any')
url='https://www.npmjs.com/'
license=('custom:Artistic')
depends=(
  'nodejs'
  'node-gyp'
  'nodejs-nopt'
  'semver'
)
makedepends=(
  'git'
)
optdepends=(
  "git: for dependencies using Git URL's"
)
_local="file://${HOME}/${pkgname}-cli"
_ns="${pkgname}"
_http="https://github.com"
_url="${_http}/${_ns}/cli"
source=(
  # "npm-cli::git+${_url}.git#tag=v${pkgver}"
  "npm-cli::git+${_local}#tag=v${pkgver}"
)
b2sums=(
  'SKIP'
)

build() {
  cd \
    npm-cli
  node \
    scripts/resetdeps.js
  node \
    . \
      run \
        build \
	  -w \
	    docs

  # Workaround for 
  # https://github.com/npm/cli/issues/780
  cd \
   kman
  local \
    f \
    name \
    sec \
    title
  for f \
    in man5/folders.5 \
       man5/install.5 \
       man7/*.7; do
    sec=${f##*.}
    name=$( \
      basename \
        "$f" \
	."$sec")
    title=$( \
      echo \
        "$name" | \
	tr \
	  '[:lower:]' \
	  '[:upper:]')
    sed \
      -Ei \
        "s/^\.TH \"$title\"/.TH \"NPM-$title\"/" \
      "$f"
    mv \
      "$f" \
      "${f%/*}/npm-$name.$sec"
  done
}

check() {
  cd \
    npm-cli
  node \
    . \
    run \
      test \
        --ignore-scripts
}

package() {
  local \
    mod_dir=/usr/lib/node_modules/$pkgname
  install \
    -d \
    "$pkgdir"/usr/share/{bash-completion/completions,licenses/$pkgname}
  ln \
    -s \
      $mod_dir/LICENSE \
      "$pkgdir"/usr/share/licenses/$pkgname/LICENSE
  cd \
    npm-cli
  node \
    . \
    install \
      -g \
      --prefix="$pkgdir/usr" \
      "$(\
        node \
          . \
	  pack \
	    --ignore-scripts | \
	  tail \
	    -1)"
  node \
    . \
    completion > \
    "$pkgdir"/usr/share/bash-completion/completions/npm
  echo \
    'globalconfig=/etc/npmrc' > \
    "$pkgdir"/$mod_dir/npmrc
  cd \
    "$pkgdir"
  # Remove superfluous scripts
  rm \
    -r \
      ./$mod_dir/{bin/{node-gyp-bin,np{m,x}{,.{cmd,ps1}}},node_modules/.bin}

  # Experimental dedup
  rm \
    -r \
    ./$mod_dir/node_modules/{node-gyp,nopt,semver}

  # Support both `man` and `npm help`
  for mandir \
    in ./"$mod_dir"/man/man?; do
    local \
      dst=usr/share/man/"$( \
        basename \
	  "$mandir")"
    gzip \
      "$mandir"/*
    install \
      -d \
        "$dst"
    ln \
      -r \
      -s \
      "$mandir"/* \
      "$dst"
  done
}

# vim:set sw=2 sts=-1 et:
