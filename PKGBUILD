# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Truocolo <truocolo@aol.com>
# Maintainer: Pellegrino Prevete (tallero) <pellegrinoprevete@gmail.com>
# Maintainer: Caleb Maclennan <caleb@alerque.com>
# Contributor: moson <moson@archlinux.org>
# Contributor: David Runge <dvzrv@archlinux.org>
# Contributor: David Joaquín Shourabi Porcel <david@djsp.eu>
# Contributor: Sven-Hendrik Haase <svenstaro@gmail.com>
# Contributor: Eli Schwartz <eschwartz@archlinux.org>

_py="python"
_pkg=poetry
_pkgname="${_pkg}"
pkgname="${_py}-${_pkg}"
pkgver=1.8.3
pkgrel=1
pkgdesc='Python dependency management and packaging made easy'
arch=(
  any
)
url="https://${pkgname}.org"
license=(
  MIT
)
_deps=(
  cachecontrol
  cachy
  cleo
  crashtest
  dulwich
  filelock
  html5lib
  installer
  jsonschema
  keyring
  lockfile
  packaging
  pexpect
  pkginfo
  platformdirs
  poetry-core
  poetry-plugin-export
  pyproject-hooks
  requests
  requests-toolbelt
  shellingham
  tomlkit
  trove-classifiers
  urllib3
  virtualenv
)
depends=(
  "${_py}"
  "${_py}-build"
  "${_deps[@]/#/python-}"
)
makedepends=(
  "${_py}-build"
)
checkdepends=(
  python-deepdiff  # not mentioned but required
  python-psutil  # for python-pytest-xdist
  python-httpretty
  python-pip  # not mentioned but required
  python-pytest
  python-pytest-mock
  python-pytest-randomly
  python-pytest-xdist)
optdepends=(
  'python-pip: to use pip with virtual environments')
provides=(
  poetry
)
_archive="${_pkg}-${pkgver}"
_url="https://github.com/${pkgname}/${_pkg}"
source=(
  "${_url}/archive/${pkgver}/${_archive}.tar.gz"
)
sha256sums=(
  '4da8d1b19cfb50536c6b54e984b88cec3bc1203f9749d5f4958db5cbb0c7b7bc'
)

prepare() {
  cd \
    "$_archive"
  # Unpin crashtest which we have packaged at 0.4.0
  # https://bugs.archlinux.org/task/75733
  # Also unpin requests-toolbelt
  # https://github.com/python-poetry/poetry/pull/7893
  sed \
    -i \
    -e \
    '/^crashtest/s/\^/>=/' \
    -e '/^requests-toolbelt/s/,<0.11.0//' \
    pyproject.toml
}

build() {
  local \
    _poetry \
    _site_packages
  site_packages=$( \
    python \
      -c \
      "import site; print(site.getsitepackages()[0])")
  _poetry="${PWD}/test_dir/usr/bin"
  cd \
    "${_archive}"
  python \
    -m \
      build \
    -wn
  # install to tmp dir for tests and generate completions
  python \
    -m \
      installer \
    --destdir=test_dir \
    dist/*.whl
  export \
    PYTHONPATH="$PWD/test_dir/$site_packages:$PYTHONPATH"
  export \
    PATH="$PWD/test_dir/usr/bin:$PATH"
  # TODO: python-poetry-completions
  # poetry completions bash > poetry.bash
  # poetry completions zsh > poetry.zsh
  # poetry completions fish > poetry.fish
}

check() {
  local \
    site_packages
  site_packages=$( \
    python \
      -c \
        "import site; print(site.getsitepackages()[0])")
  cd \
    "${_archive}"
  export \
    PYTHONPATH="${PWD}/test_dir/${site_packages}:${PYTHONPATH}"
  pytest \
    -vv \
    tests
}

package() {
  cd \
    "${_archive}"
  python \
    -m \
      installer \
    --destdir="${pkgdir}" \
    dist/*.whl
  install \
    -Dm0644 \
    -t "${pkgdir}/usr/share/licenses/${pkgname}/" \
    LICENSE
  # TODO: python-poetry-completions
  # install \
  #   -vDm 644 \
  #   "${_pkgname}.bash" \
  #   "${pkgdir}/usr/share/bash-completion/completions/${_pkgname}"
  # install \
  #   -vDm 644 \
  #   "${_pkgname}.zsh" \
  #   "${pkgdir}/usr/share/zsh/site-functions/_${_pkgname}"
  # install \
  #   -vDm 644 \
  #   "${_pkgname}.fish" \
  #   -t \
  #   "${pkgdir}/usr/share/fish/vendor_completions.d/"
}
