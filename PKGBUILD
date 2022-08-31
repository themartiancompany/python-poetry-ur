# Maintainer: Caleb Maclennan <caleb@alerque.com>
# Contributor: Sven-Hendrik Haase <svenstaro@gmail.com>
# Contributor: Eli Schwartz <eschwartz@archlinux.org>

BUILDENV+=(!check)

_pkgname=poetry
pkgname=python-poetry
pkgver=1.2.0
pkgrel=1
pkgdesc='Python dependency management and packaging made easy'
arch=(any)
url=https://python-poetry.org
license=(MIT)
_deps=(cachecontrol
       cachy
       cleo
       html5lib
       keyring
       lockfile
       packaging
       pexpect
       pkginfo
       poetry-core
       requests
       requests-toolbelt
       shellingham
       tomlkit
       virtualenv)
depends=("${_deps[@]/#/python-}")
makedepends=(python-{build,installer})
checkdepends=(git
              python-httpretty
              python-pytest
              python-pytest-mock)
provides=(poetry)
_archive="$_pkgname-$pkgver"
source=("https://github.com/$pkgname/$_pkgname/archive/$pkgver/$_archive.tar.gz"
        poetry-completions-generator)
sha256sums=('a5a9531e52bcfc3f93d1c378e1b41c2256e9b1378a044f0b4787e91744d35f78'
            '970225289188ea8dc49fbec8a2bfe0c891aee80ff56ba6e69bdd8afef8bccab6')

prepare() {
	cd "$_archive"
	install -m0755 -t ./ ../poetry-completions-generator
}

build() {
	cd "$_archive"
	python -m build -wn
}

check() {
	cd "$_archive/src"
	export PYTHONPATH="$PWD"
	pytest
}

package() {
	cd "$_archive"
	python -m installer -d "$pkgdir" dist/*.whl
	install -Dm0644 -t "$pkgdir/usr/share/licenses/$pkgname/" LICENSE
	# install completions, which for some crazy reason hardcode the filename
	# used to invoke which is __main__.py if we use python -m poetry, and also
	# adds the full directory path???
	./poetry-completions-generator completions bash |
		sed "#$srcdir#d" |
		install -Dm644 /dev/stdin "$pkgdir/usr/share/bash-completion/completions/poetry"
	./poetry-completions-generator completions zsh |
		sed "#$srcdir#d" |
		install -Dm644 /dev/stdin "$pkgdir/usr/share/zsh/site-functions/_poetry"
	./poetry-completions-generator completions fish |
		install -Dm644 /dev/stdin "$pkgdir/usr/share/fish/vendor_completions.d/poetry.fish"
}
