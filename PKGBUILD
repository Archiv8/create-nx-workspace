#!/bin/bash

# Based on original PKGBUILD by Felix Yan <felixonmars@archlinux.org>

# Disable various shellcheck rules that produce false positives in this file.
# Repository rules should be added to the .shellcheckrc file located in the
# repository root directory, see https://github.com/koalaman/shellcheck/wiki
# and https://archiv8.github.io for further information.
# shellcheck disable=SC2034,SC2154
# [ToDo]: Add files: User documentation
# [ToDo]: Add files: Tooling
# [FixMe]: Namcap warnings and errors

# Maintainer: Ross Clark <https://github.com/Archiv8/create-nx-workspace/discussions>
# Contributor: Ross Clark <https://github.com/Archiv8/create-nx-workspace/discussions>

# pkgbase=
pkgname="create-nx-workspace"
pkgver=15.0.4
pkgrel=1
# epoch=
pkgdesc="A next generation build system with monorepo support and powerful integrations."
arch=(
  "any"
)
url="https://blog.nrwl.io/nx/home"
license=(
  "MIT"
)
# groups=()
depends=(
  "nodejs"
)
# optdepends=()
makedepends=(
  "jq"
  "npm"
)
# checkdepends=()
# provides=()
# conflicts=()
# replaces=()
# backup=()
# options=()
# install=
# changelog="CHANGELOG.md"
source=(
  "https://registry.npmjs.org/${pkgname}/-/${pkgname}-${pkgver}.tgz"
  # "CC-by-SA-v4.md"
  # "CHANGELOG.md"
  # "ISSUES.md"
  # "LICENSE"
  # "LICENSE.md"
  # "MIT.md"
  # "README.md"
)
noextract=(
  "${pkgname}-${pkgver}.tgz"
)
# validpgpkeys=()
sha512sums=(
  "821bfa927804fcc5617c4c620a29d2b7ea52daf35be005c84fb3577c59e4f51ba6a50b8f46fb056b42603438660b3409bd56e532aeded02522f9dd8d0b7e2335"
)

# prepare () {}

# build() {}

# check() {}

package() {
  # [Fixes]:  Caching in user's home directory. Ensure cache is set when install is run in order to avoid littering user's home directory
  printf "\e[1;32m==>\e[0m \e[38;5;248mBuilding nodejs package... \e[0m\n"
  printf "    This may take some time depending on the size of the package and number of dependencies to download... \e[0m\n"
  npm install --cache "$srcdir/npm-cache" -g --user root --prefix "$pkgdir"/usr "$srcdir"/${pkgname}-${pkgver}.tgz

  # [Fixes]: Incorrect user / group as highlighted by namcap
  printf "\e[1;32m==>\e[0m \e[38;5;248mCorrecting possible ownership issues\e[0m\n"
  while IFS= read -r fsitem; do
    chown -h root:root "$fsitem"
  done < <(find "$pkgdir" -not -group root -not -user root)

  # [Fixes]: Non-deterministic race in npm gives 777 permissions to random directories.
  # See https://github.com/npm/npm/issues/9359 for details.
  printf "\e[1;32m==>\e[0m \e[38;5;248mCorrecting possible permission issues on directories\e[0m\n"
  find "$pkgdir/usr" -type d -exec chmod 755 '{}' +

  # [Fixes]: References to $pkgdir
  printf "\e[1;32m==>\e[0m \e[38;5;248mRemoving references to \$pkgdir\e[0m\n"
  find "$pkgdir" -type f -name package.json -print0 | xargs -0 sed -i "/_where/d"

  # [Fixes]: References to $srcdir
  printf "\e[1;32m==>\e[0m \e[38;5;248mRemoving references to \$srcdir \e[0m\n"
  local tmppackage="$(mktemp)"
  local pkgjson="$pkgdir/usr/lib/node_modules/${pkgname}/package.json"
  jq '.|=with_entries(select(.key|test("_.+")|not))' "$pkgjson" >"$tmppackage"
  mv "$tmppackage" "$pkgjson"
  chmod 644 "$pkgjson"

  rm -rf "$pkgdir/usr/lib/node_modules/root"

  # Install license
  install -dm755 "${pkgdir}/usr/share/licenses/${pkgname}"
  ln -s "../../../lib/node_modules/${pkgname}/LICENSE" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"

  # Create Archiv8 documentation folder
  #  install -dvm 755 "$pkgdir/usr/share/doc/$pkgname/packaging/"

  # Install Archiv8 Documentation
  #  install -Dm 644 "CC-by-SA-v4.md" "$pkgdir/usr/share/licenses/$pkgname/packaging/CC-by-SA-v4.md"
  #  install -Dm 644 "CHANGELOG.md" "$pkgdir/usr/share/doc/$pkgname/packaging/CHANGELOG.md"
  #  install -Dm 644 "ISSUES.md" "$pkgdir/usr/share/doc/$pkgname/packaging/ISSUES.md"
  # install -Dm 644 "LICENSE.md" "$pkgdir/usr/share/licenses/$pkgname/packaging/LICENSE.md"
  # install -Dm 644 "MIT.md" "$pkgdir/usr/share/licenses/$pkgname/packaging/MIT.md"
  # install -Dm 644 "README.md" "$pkgdir/usr/share/doc/$pkgname/packaging/README.md"
}
