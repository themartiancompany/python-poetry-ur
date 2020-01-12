# Maintainer: Eli Schwartz <eschwartz@archlinux.org>
# Maintainer: Sven-Hendrik Haase <svenstaro@gmail.com>

_pkgname=poetry
pkgname=python-poetry
pkgver=1.0.2
pkgrel=1
pkgdesc="Python dependency management and packaging made easy"
arch=('any')
url="https://poetry.eustace.io/"
license=('MIT')
_deps=('cachecontrol' 'cachy' 'cleo' 'html5lib' 'jsonschema' 'lockfile'
       'pkginfo' 'pyparsing' 'pyrsistent' 'requests' 'requests-toolbelt'
       'shellingham' 'tomlkit' 'keyring' 'pexpect')
depends=("${_deps[@]/#/python-}")
checkdepends=('git' 'python-pytest' 'python-pytest-mock' 'python-httpretty')
provides=('poetry')
conflicts=('poetry')
replaces=('poetry')
source=("${_pkgname}-${pkgver}.tar.gz::https://github.com/sdispater/${_pkgname}/archive/${pkgver}.tar.gz"
        "poetry-completions-generator")
sha256sums=('837c429e70851af65c761af55ae509a8114d4acba5b6559b904d82ee3b230566'
            '970225289188ea8dc49fbec8a2bfe0c891aee80ff56ba6e69bdd8afef8bccab6')
b2sums=('853fcae57f6b8e205bc1cc6d95b9373ea3fd893e3aa49836d9f14725c4bf7a1f7e364188891b2b120697dc97c3964636698e15dd9fb16abace64621a252dcac6'
        '3fd62e7936d7547dcd06a1a7519f176a1597553ecc959144a4be799bb7e2d688e187f14604d3e359ed10c128c722ebd588c07ca318e0be0a31d276dcb388e2e1')

prepare() {
    cd "${srcdir}"/${_pkgname}-${pkgver}

    python -m poetry build -f sdist
    tar xf dist/${_pkgname}-${pkgver}.tar.gz
}

build() {
    cd "${srcdir}"/${_pkgname}-${pkgver}/${_pkgname}-${pkgver}

    python setup.py build
}

check() {
    cd "${srcdir}"/${_pkgname}-${pkgver}

    # only works inside git repositories
    pytest \
        -k 'not test_default_with_excluded_data ' \
        --ignore tests/console/commands/test_add.py \
        --ignore tests/console/commands/test_export.py \
        --ignore tests/console/commands/test_show.py
}

package() {
    cd "${srcdir}"/${_pkgname}-${pkgver}/${_pkgname}-${pkgver}

    python setup.py install --root="${pkgdir}" --optimize=1 --skip-build
    install -Dm644 LICENSE "${pkgdir}"/usr/share/licenses/${pkgname}/LICENSE

    # install completions, which for some crazy reason hardcode the filename
    # used to invoke which is __main__.py if we use python -m poetry, and also
    # adds the full directory path???
    install -m755 "${srcdir}"/poetry-completions-generator ./poetry-completions-generator
    ./poetry-completions-generator completions bash | sed "\|${srcdir}|d" | \
        install -Dm644 /dev/stdin "${pkgdir}"/usr/share/bash-completion/completions/poetry
    ./poetry-completions-generator completions zsh | sed "\|${srcdir}|d" | \
        install -Dm644 /dev/stdin "${pkgdir}"/usr/share/zsh/site-functions/_poetry
    ./poetry-completions-generator completions fish | \
        install -Dm644 /dev/stdin "${pkgdir}"/usr/share/fish/vendor_completions.d/poetry.fish

    sed -i \
        -e "s/pyrsistent.*/pyrsistent/g" \
        -e "s/requests-toolbelt.*/requests-toolbelt/g" \
        -e "s/keyring.*/keyring/g" \
        "${pkgdir}"/usr/lib/python3.8/site-packages/poetry-${pkgver}-py3.8.egg-info/requires.txt
}
