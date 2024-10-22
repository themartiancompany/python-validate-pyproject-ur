# SPDX-License-Identifier: AGPL-3.0
#
# Maintainer: Felix Yan <felixonmars@archlinux.org>
#
_git="false"
_py="python"
_pyver="$( \
  "${_py}" \
    -V | \
    awk \
      '{print $2}')"
_pymajver="${_pyver%.*}"
_pkg=validate-pyproject
pkgname="${_py}-${_pkg}"
pkgver=0.13
_commit=b752273289ecf6d81dbbe6fc8284f2689ef8514d
pkgrel=1
_pkgdesc=(
  "Validation library and CLI tool for checking"
  "on 'pyproject.toml' files using JSON Schema"
)
pkgdesc="${_pkgdesc[*]}"
_http="https://github.com"
_ns="abravalheri"
url="${_http}/${_ns}/${_pkg}"
license=(
  'MPL'
)
arch=(
  'any'
)
depends=(
  "${_py}>=${_pymajver}"
  "${_py}-fastjsonschema"
  "${_py}-packaging"
  "${_py}-trove-classifiers"
)
makedepends=(
  "${_py}-build"
  "${_py}-installer"
  "${_py}-wheel"
  "${_py}-setuptools-scm"
)
checkdepends=(
  "${_py}-pytest"
)
_tag="${_commit}"
_tarname="${_pkg}-${_tag}"
if [[ "${_git}"  == "true" ]]; then
  makedepends+=(
    "git"
  )
  _src="${_tarname}::git+${url}.git#commit=${_tag}"
  _sum="SKIP"
elif [[ "${_git}"  == "false" ]]; then
  _src="${_tarname}.zip::${url}/archive/${_tag}.zip"
  _sum="902e61f9f3ac12667c4da904f43a73f1cf1cb1fc6f1a3f41906cd05b19ad9bae"
fi
source=(
  "${_src}"
)
sha256sums=(
  "${_sum}"
)

prepare() {
  cd \
    "${_pkg}-${_commit}"
  sed \
    -i \
    '/--cov/d' \
    setup.cfg
}

build() {
  cd \
    "${_pkg}-${_commit}"
  "${_py}" \
    -m build \
    -wn
}

check() {
  local \
    _site_packages \
    _site_packages_cmd
  _site_packages_cmd="import site; print(site.getsitepackages()[0])"
  _site_packages=$( \
    "${_py}" \
      -c \
        "${_site_packages_cmd}")
  cd \
    "${_pkg}-${_commit}"
  "${_py}" \
    -m \
      installer \
    --destdir=tmp_install \
    dist/*.whl
  PYTHONPATH="$PWD/tmp_install/${_site_packages}:${PYTHONPATH}" \
  pytest \
    --doctest-modules \
    --ignore \
      src/validate_pyproject/_vendor src
  # Deselected tests requiring a installed validate-pyproject
  PYTHONPATH="$PWD/tmp_install/${_site_packages}:${PYTHONPATH}" \
  pytest \
    --deselect \
      tests/test_pre_compile.py \
    --deselect \
      tests/test_vendoring.py
}

package() {
  cd \
    "${_pkg}-${_commit}"
  "${_py}" \
    -m \
      installer \
    --destdir="${pkgdir}" \
    dist/*.whl
}

# vim:set sw=2 sts=-1 et:
