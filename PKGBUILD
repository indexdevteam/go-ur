# SPDX-License-Identifier: AGPL-3.0

#    -----------------------------------------------------
#    Copyright © 2024, 2025, 2026  Pellegrino Prevete
#
#    All rights reserved
#    -----------------------------------------------------
#
#    This program is free software: you can redistribute
#    it and/or modify it under the terms of the
#    GNU Affero General Public License as published by
#    the Free Software Foundation, either version 3 of
#    the License, or (at your option) any later version.
#
#    This program is distributed in the hope that it
#    will be useful, but WITHOUT ANY WARRANTY;
#    without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.
#    See the GNU Affero General Public License for
#    more details.
#
#    You should have received a copy of the
#    GNU Affero General Public License
#    along with this program.
#    If not, see <https://www.gnu.org/licenses/>.

# Maintainers:
#   Truocolo
#     <truocolo@aol.com>
#     <truocolo@0x6E5163fC4BFc1511Dbe06bB605cc14a3e462332b>
#   Pellegrino Prevete (dvorak)
#     <pellegrinoprevete@gmail.com>
#     <dvorak@0x87003Bd6C074C713783df04f36517451fF34CBEf>
# Contributors:
#   Morten Linderud
#     <foxboron@archlinux.org>
#   Daniel Martí
#     <mvdan@mvdan.cc>
#   Bartłomiej Piotrowski
#     <bpiotrowski@archlinux.org>
#   Alexander F. Rødseth
#     <xyproto@archlinux.org>
#   Pierre Neidhardt
#     <ambrevar@gmail.com>
#   Vesa Kaihlavirta
#     <vegai@iki.fi>
#   Rémy Oudompheng
#     <remy@archlinux.org>
#   Andres Perera
#     <andres87p gmail>
#   Matthew Bauer
#     <mjbauer95@gmail.com>
#   Christian Himpel
#     <chressie@gmail.com>
#   Mike Rosset
#     <mike.rosset@gmail.com>
#   Daniel YC Lin
#     <dlin.tw@gmail.com>
#   John Luebs
#     <jkluebs@gmail.com>

# TODO
# Remember to rebuild go-tools, delve, gopls,
# golangci-lint, staticcheck on new go versions
# pkgctl \
#   build \
#     --offload \
#     --rebuild \
#     --testing \
#     --release \
#       "go-tools" \
#       "delve" \
#       "gopls" \
#       "golangci-lint"
#       "staticcheck"
# pkgctl \
#   db \
#     move \
#       "extra-testing" \
#       "extra" \
#       "go" \
#       "go-tools" \
#       "delve" \
#       "gopls" \
#       "golangci-lint" \
#       "staticcheck"

_os="$(
  uname \
    -o)"
if [[ ! -v "_bootstrap" ]]; then
  _bootstrap="false"
  if [[ "${_os}" == "Android" ]]; then
    _bootstrap="true"
  fi
fi 
_pkg=go
_pkg_alt="${_pkg}lang"
_go_pkg="${_pkg}"
if [[ "${_bootstrap}" == "true" ]]; then
  _go_pkg="${_pkg_alt}"
fi
if [[ ! -v "_docs" ]]; then
  _docs="true"
fi
if [[ ! -v "_git" ]]; then
  _git="true"
fi
_git="true"
pkgbase="${_pkg}"
pkgname=(
  "${_pkg}"
)
epoch=2
pkgver=1.27.1
pkgrel=14
pkgdesc='Core compiler tools for the Go programming language'
arch=(
  "aarch64"
  "arm"
  "armv6h"
  "armv7l"
  "armv8l"
  "i686"
  "pentium4"
  "powerpc"
  "x86_64"
)
url="https://${_pkg}.dev"
license=(
  "BSD-3-Clause"
)
makedepends=(
  # An apparent self-dependency
  "${_go_pkg}"
)
if [[ "${_git}" == "true" ]]; then
  makedepends+=(
    "git"
  )
fi
replaces=(
  "${_pkg}-pie"
)
provides=(
  "${_pkg}-pie=${pkgver}"
  "${_pkg}lang=${pkgver}"
)
conflicts=(
  "${_pkg}lang"
)
options=(
  "!strip"
  "staticlibs"
)
_tarname="${_pkg}"
source=(
  "https://${_pkg}.dev/dl/${_pkg}${pkgver}.src.tar.gz"{,.asc}
)
validpgpkeys=(
  # Google Inc. (Linux Packages Signing Authority)
  #   <linux-packages-keymaster@google.com>
  'EB4C1BFD4F042F6DDDCCEC917721F63BD38B4796'
)
sha256sums=(
  '4e408abae126d916b6164627193f2c54f0e3ca1312d693b86db45f862ab238b1'
  'SKIP'
)

_usr_get() {
  local \
    _bin
  _bin="$(
    dirname \
      "$(command \
           -v \
	         "env")")"
  dirname \
    "${_bin}"
}

_android_fix_shebang() {
  local \
    _file="${1}" \
    _msg=() \
    _pattern \
    _patterns=() \
    _repl
  if [[ ! -e "${_file}" ]]; then
    _msg=(
      "File '${_file}' does"
      "not exist."
    )
    echo \
      "${_msg[*]}" \
      1>&2
  fi
  _pattern=(
    "^#!/usr/bin/env"
    "^#! /usr/bin/env"
  )
  _repl="#!/data/data/com.termux/files/usr/bin/env bash"
  for _pattern in "${_patterns[@]}"; do
    sed \
      "s%${_pattern}%${_repl}%g" \
      -i \
      "${_file}"
  done
  _termux_fix_shebang="$(
    command \
      -v \
      "termux-fix-shebang")"
  if [[ "${_termux_fix_shebang}" != "" ]]; then
    termux-fix-shebang \
      "${_file}"
  fi
}

build() {
  local \
    _arch \
    _msg=() \
    _make_bash \
    _usr
  local \
    _go_flags=()
  _go_flags=(
    -buildmode=pie
    -trimpath
    -ldflags=-linkmode=external
    -mod=vendor
    -modcacherw
  )
  _make_bash="${srcdir}/${_tarname}/src/make.bash"
  _usr="$(
    _usr_get)"
  _arch="$(
    uname \
      -m)"
  if [[ "${_arch}" == "aarch64" ]]; then
    _msg=(
      "Do not specify any architecture"
      "on aarch64."
    )
    echo \
      "${_msg[*]}" \
      1>&2
  elif [[ "${_arch}" == "x86_64" ]]; then
    # make sure we're building for the right x86-64 version
    export \
      GOARCH="amd64" \
      GOAMD64="v1"
  fi
  export \
    GOROOT_FINAL="${_usr}/lib/go"
    GOROOT_BOOTSTRAP="${_usr}/lib/go"
  # Disable dwarf5 until debugedit catches up
  export \
    GOEXPERIMENT="nodwarf5"
  cd \
    "${_tarname}/src"
  if [[ "${_os}" == "Android" ]]; then
    export \
      CGO_CPPFLAGS="${CPPFLAGS}" \
      CGO_CFLAGS="${CFLAGS}" \
      CGO_CXXFLAGS="${CXXFLAGS}" \
      CGO_LDFLAGS="${LDFLAGS}" \
      GOFLAGS="${_go_flags[*]}"
    _android_fix_shebang \
      "${_make_bash}"
  fi
  "${_make_bash}" \
    -v
}

check() {
  export \
    GO_TEST_TIMEOUT_SCALE=3
  cd \
    "${_pkg}/src"
  # TODO:
  #   Disable LSAN tests as it's crashing
  #   and we don't want to wait for upstream.
  #   See: https://github.com/golang/go/issues/74476
  "./run.bash" \
    --no-rebuild \
    -v \
    -v \
    -v \
    -k \
    -run \
      "!cmd/cgo/internal/testsanitizers"
}

package() {
  local \
    _arch \
    _usr
  _usr="$(
    _usr_get)"
  _arch="$(
    uname \
      -m)"
  cd \
    "${_tarname}"
  install \
    -vdm755 \
    "${pkgdir}/usr/bin" \
    "${pkgdir}/usr/lib/${_pkg}" \
    "${pkgdir}/usr/share/doc/${_pkg}"
  if [[ "${_arch}" == "x86_64" ]]; then
    if [[ "${_os}" == "Android" ]]; then
      install \
        -vdm755 \
        "${pkgdir}/usr/lib/${_pkg}/pkg/android_amd64_"{"dynlink","race"}
    fi
    if [[ "${_os}" == "Msys2" ]]; then
      echo \
        "boh"
    else
      install \
        -vdm755 \
        "${pkgdir}/usr/lib/go/pkg/linux_amd64_"{"dynlink","race"}
    fi
  elif [[ "${_arch}" == "aarch64" ]]; then
    if [[ "${_os}" == "Android" ]]; then
      install \
        -vdm755 \
        "${pkgdir}/usr/lib/${_pkg}/pkg/linux_aarch64_"{"dynlink","race"} \
        "${pkgdir}/usr/lib/${_pkg}/pkg/linux_arm64_"{"dynlink","race"} \
        "${pkgdir}/usr/lib/${_pkg}/pkg/linux_arm64_"{"dynlink","race"}
    fi
    if [[ "${_os}" == "Msys2" ]]; then
      echo \
        "boh"
    else
      install \
        -vdm755 \
        "${pkgdir}/usr/lib/${_pkg}/pkg/linux_aarch64_"{"dynlink","race"} \
        "${pkgdir}/usr/lib/${_pkg}/pkg/linux_arm64_"{"dynlink","race"} \
        "${pkgdir}/usr/lib/${_pkg}/pkg/linux_arm64_"{"dynlink","race"}
    fi
  elif [[ "${_arch}" == "arm" ]]; then
    if [[ "${_os}" == "Android" ]]; then
      install \
        -vdm755 \
        "${pkgdir}/usr/lib/${_pkg}/pkg/android_arm_"{"dynlink","race"} \
        "${pkgdir}/usr/lib/${_pkg}/pkg/android_armv7l_"{"dynlink","race"} \
        "${pkgdir}/usr/lib/${_pkg}/pkg/android_armv8l_"{"dynlink","race"}
    fi
    if [[ "${_os}" == "Msys2" ]]; then
      echo \
        "boh"
    else
      install \
        -vdm755 \
        "${pkgdir}/usr/lib/${_pkg}/pkg/linux_arm_"{"dynlink","race"} \
        "${pkgdir}/usr/lib/${_pkg}/pkg/linux_armv7l_"{"dynlink","race"} \
        "${pkgdir}/usr/lib/${_pkg}/pkg/linux_armv8l_"{"dynlink","race"}
    fi
  fi
  cp \
    -a \
    "bin" \
    "pkg" \
    "src" \
    "lib" \
    "misc" \
    "api" \
    "test" \
    "${pkgdir}/usr/lib/${_pkg}"
  # We can't strip all binaries and libraries,
  # as that also strips some testdata directories and breaks the tests.
  # Just strip the packaged binaries as a compromise.
  strip \
    ${STRIP_BINARIES} \
    "${pkgdir}/usr/lib/${_pkg}"{"/bin/"*,"/pkg/tool/"*"/"*}
  if [[ "${_docs}" == "true" ]]; then
    cp \
      -r \
      "doc/"* \
      "${pkgdir}/usr/share/doc/${_pkg}"
  fi
  ln \
    -sf \
    "${_usr}/lib/${_pkg}/bin/${_pkg}" \
    "${pkgdir}/usr/bin/${_pkg}"
  ln \
    -sf \
    "${_usr}/lib/${_pkg}/bin/${_pkg}fmt" \
    "${pkgdir}/usr/bin/${_pkg}fmt"
  ln \
    -sf \
    "${_usr}/share/doc/${_pkg}" \
    "${pkgdir}/usr/lib/${_pkg}/doc"
  install \
    -vDm644 \
    "VERSION" \
    "${pkgdir}/usr/lib/${_pkg}/VERSION"
  rm \
    -rf \
    "${pkgdir}/usr/lib/${_pkg}/pkg/bootstrap"
  # TODO: Figure out if really needed
  rm \
    -rf \
    "${pkgdir}/usr/lib/${_pkg}/pkg/obj/${_pkg}-build"
  # https://github.com/golang/go/issues/57179
  install \
    -vDm644 \
    "${_pkg}.env" \
    "${pkgdir}/usr/lib/${_pkg}/${_pkg}.env"
  install \
    -vDm644 \
    "LICENSE" \
    "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
}

# vim: ts=2 sw=2 et
