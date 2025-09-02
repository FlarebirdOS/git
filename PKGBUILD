pkgname=git
pkgver=2.50.1
pkgrel=1
pkgdesc="the fast distributed version control system"
arch=('x86_64')
url="https://git-scm.com/"
license=('GPL-2.0-only')
depends=(
    'curl'
    'expat'
    'grep'
    'openssl'
    'pcre2'
    'perl'
    'perl-error'
    'perl-mailtools'
    'shadow'
    'zlib'
)
makedepends=(
    'python'
    'python-asciidoc'
    'xmlto'
)
source=(https://www.kernel.org/pub/software/scm/${pkgname}/${pkgname}-${pkgver}.tar.xz)
sha256sums=(7e3e6c36decbd8f1eedd14d42db6674be03671c2204864befa2a41756c5c8fc4)

build() {
    cd ${pkgname}-${pkgver}

    local configure_args=(
        --with-gitconfig=/etc/gitconfig
        --with-python=python3
        --with-expat
        --with-curl
        --with-editor=/usr/bin/vim
        ${configure_options}
    )

    export CFLAGS="${CFLAGS}"
    export LDFLAGS="${LDFLAGS}"
    export INSTALL_SYMLINKS=1
    export MAN_BOLD_LITERAL=1
    export NO_PERL_CPAN_FALLBACKS=1
    export USE_LIBPCRE2=1

    ./configure "${configure_args[@]}"

    make
    make html
    make man
}

package() {
    cd ${pkgname}-${pkgver}

    make DESTDIR=${pkgdir} perllibdir="$(perl -MConfig -e 'print "$Config{installsitearch}"')" install

    make DESTDIR=${pkgdir} install-man
    make DESTDIR=${pkgdir} htmldir=/usr/share/doc/${pkgname}-${pkgver} install-html

    # bash completion
    install -vdm755 ${pkgdir}/usr/share/bash-completion/completions/
    install -m 0644 ./contrib/completion/git-completion.bash ${pkgdir}/usr/share/bash-completion/completions/git

    # fancy git prompt
    install -vdm755 ${pkgdir}/usr/share/git/
    install -m 0644 ./contrib/completion/git-prompt.sh ${pkgdir}/usr/share/git/git-prompt.sh

    install -vdm755 ${pkgdir}/usr/share/doc/${pkgname}-${pkgver}/man-pages/{html,text}
    mv ${pkgdir}/usr/share/doc/${pkgname}-${pkgver}/{git*.adoc,man-pages/text}
    mv ${pkgdir}/usr/share/doc/${pkgname}-${pkgver}/{git*.,index.,man-pages/}html

    install -vdm755 ${pkgdir}/usr/share/doc/${pkgname}-${pkgver}/technical/{html,text}
    mv ${pkgdir}/usr/share/doc/${pkgname}-${pkgver}/technical/{*.adoc,text}
    mv ${pkgdir}/usr/share/doc/${pkgname}-${pkgver}/technical/{*.,}html

    install -vdm755 ${pkgdir}/usr/share/doc/${pkgname}-${pkgver}/howto/{html,text}
    mv ${pkgdir}/usr/share/doc/${pkgname}-${pkgver}/howto/{*.adoc,text}
    mv ${pkgdir}/usr/share/doc/${pkgname}-${pkgver}/howto/{*.,}html

    sed -i '/^<a href=/s|howto/|&html/|' ${pkgdir}/usr/share/doc/${pkgname}-${pkgver}/howto-index.html
    sed -i '/^\* link:/s|howto/|&html/|' ${pkgdir}/usr/share/doc/${pkgname}-${pkgver}/howto-index.adoc
}
