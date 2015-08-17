# Maintainer: XZS <d.f.fischer at web dot de>
# This PKGBUILD is maintained on GitHub <https://github.com/dffischer/mozilla-extensions>.
# You may find it convenient to file issues and pull requests there.

pkgname=thunderbird-extension-gnome-integration
pkgver=0.4.4
pkgrel=1
pkgdesc='Provides better integration with Gnome using standard notifications and other features'
url='http://davidmartinez.net/proyectos/gnomeintegration'
license=('GPLv3')
depends=('libnotify')
md5sums=('ffb9ab265624ff94af1595fb448b9f1d')

[ "$arch" ] || arch=('any')

source+=("${pkgname}.zip::https://addons.mozilla.org/firefox/downloads/latest/${pkgname#*-*-}/platform:2/")

prepare() {
  rm ${pkgname}.zip
}

makedepends+=(rasqal)

sparql() {
  roqet -e "PREFIX em: <http://www.mozilla.org/2004/em-rdf#> SELECT ?x WHERE { $1 }" \
    -D "${2:-install.rdf}" -r csv 2>/dev/null | tr -d '\r' | tail -n 1 | head -c -1
}

pkgver() {
  sparql '<urn:mozilla:install-manifest> em:version ?x' | tr - .
}

version-range() {
  local emid=$(emid $1)
  echo "$1>$(version min $emid)" "$1<$(version max $emid)"
}

emid() {
  case $1 in
    firefox)     echo '{ec8030f7-c20a-464f-9b0e-13a3a9e97384}' ;;
    thunderbird) echo '{3550f703-e582-4d05-9a08-453d09bdfdc6}' ;;
    *) return 1 ;;
  esac
}

version() {
  local version;
  version=$(sparql "[] em:id '$2' ; em:${1}Version ?x" \
    "$srcdir/install.rdf" )
  if [[ $version =~ ([[:digit:]]+).\* ]]; then
    if [[ $1 = max ]]; then
      echo $(( ${BASH_REMATCH[1]} + 1 ))
    else
      echo "=${BASH_REMATCH[1]}"
    fi
  else
    echo "=$version"
  fi
}

prepare_target() {
  local target=${pkgname%%-*}
  id="$(sparql '<urn:mozilla:install-manifest> em:id ?x')"
  destdir="$pkgdir/usr/lib/${target/firefox/firefox\/browser}/extensions"
  install -d "$destdir"
}

depends=(${pkgname%%-*})

package() {
  depends=($(version-range ${pkgname%%-*}))
  prepare_target
  cp --no-preserve=ownership,mode -r . "$destdir/$id"
}
