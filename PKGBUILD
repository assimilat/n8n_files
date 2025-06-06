# Maintainer: Yakov Till <yakov.till at gmail dot com>
# Contributor: devome <evinedeng@hotmail.com>

pkgname="n8n"
pkgver=1.94.1
pkgrel=1
pkgdesc="Free and source-available fair-code licensed workflow automation tool. Easily automate tasks across different services."
arch=('i686' 'x86_64' 'arm' 'armv7h' 'armv6h' 'aarch64')
url="https://n8n.io"
license=("custom:Sustainable Use License")
backup=("etc/default/${pkgname}")
depends=("nodejs>=18.17.0")
conflicts=("nodejs>=23")
makedepends=("npm" "jq" "curl")
source=("${pkgname}.env"
"${pkgname}.service"
"${pkgname}.sysusers"
"${pkgname}.tmpfiles"
"${pkgname}.user.service")
sha256sums=('faae87f26ac2bca25d98aa02564876742e7ed5dd4146342062196ed12d97385f'
            '9356600848545416c490e3d74aff8494946afca187dd409a9f768db7a364f9da'
            '6139ae944272cdc50715fd82a3c062ebf1b7fd73f7f789cba0d27f5a0436c688'
            'faa4145ec8723700c5f8f75ae2dd3d78c931597b67e200050b55fec9d73c3f06'
            '03cb79cddc04a0303be6d60ba2e7801106b6d4405d33953a2c508c5825c66a7c')

pkgver() {
  # Fetch latest version from npm registry
  curl -s "https://registry.npmjs.org/${pkgname}" | jq -r '.["dist-tags"].latest'
}

build() {
  # Download the correct version after pkgver() has run
  curl -L -o "${srcdir}/${pkgname}-${pkgver}.tgz" "https://registry.npmjs.org/${pkgname}/-/${pkgname}-${pkgver}.tgz"
}

package() {
  install -Dm644 "${pkgname}.env"          "${pkgdir}/etc/default/${pkgname}"
  install -Dm644 "${pkgname}.service"      "${pkgdir}/usr/lib/systemd/system/${pkgname}.service"
  install -Dm644 "${pkgname}.sysusers"     "${pkgdir}/usr/lib/sysusers.d/${pkgname}.conf"
  install -Dm644 "${pkgname}.tmpfiles"     "${pkgdir}/usr/lib/tmpfiles.d/${pkgname}.conf"
  install -Dm644 "${pkgname}.user.service" "${pkgdir}/usr/lib/systemd/user/${pkgname}.service"

  npm install --cache "${srcdir}/npm-cache" --prefix="${pkgdir}/usr" --global --ignore-scripts "${srcdir}/${pkgname}-${pkgver}.tgz"
  npm rebuild --cache "${srcdir}/npm-cache" --prefix="${pkgdir}/usr/lib/node_modules/${pkgname}" sqlite3
exit
  find "${pkgdir}/usr/lib/node_modules/${pkgname}" -type f -name "*.ts" -o -name "*.js.map" -o -name "*.vue" | xargs rm -f
  find "${pkgdir}/usr/lib/node_modules/${pkgname}" -type f -name "*.node" | xargs -I {} strip {}

  grep -rl "${pkgdir}" "${pkgdir}" | xargs -I {} sed -i "s|${pkgdir}||g" '{}'
  grep -rl "${srcdir}" "${pkgdir}" | xargs -I {} sed -i "s|${srcdir}||g" '{}'

  install -dm755 "${pkgdir}/usr/share/"{licenses,doc}"/${pkgname}"
  ln -s "/usr/lib/node_modules/${pkgname}/LICENSE.md" "${pkgdir}/usr/share/licenses/${pkgname}/LICENSE"
  ln -s "/usr/lib/node_modules/${pkgname}/README.md"  "${pkgdir}/usr/share/doc/${pkgname}/README.md"
}
