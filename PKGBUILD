pkgname=git
pkgver=2.53.0
pkgrel=3
pkgdesc="the fast distributed version control system"
arch=('x86_64')
url="https://git-scm.com/"
license=('GPL-2.0-only')
depends=(
    'curl'
    'expat'
    'perl'
    'perl-error'
    'perl-mailtools'
    'openssl'
    'pcre2'
    'grep'
    'shadow'
    'zlib-ng'
)
makedepends=(
    'python'
    'xmlto'
    'python-asciidoc'
    'git'
    'rust'
)
source=(https://www.kernel.org/pub/software/scm/${pkgname}/${pkgname}-${pkgver}.tar.xz)
sha256sums=(5818bd7d80b061bbbdfec8a433d609dc8818a05991f731ffc4a561e2ca18c653)

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
    export ZLIB_NG=1
    export WITH_RUST=1

    ./configure "${configure_args[@]}"

    make all man
    make -C contrib/credential/libsecret
    make -C contrib/subtree all man
    make -C contrib/diff-highlight
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

    # libsecret credentials helper
    install -m 0755 contrib/credential/libsecret/git-credential-libsecret ${pkgdir}/usr/libexec/git-core/git-credential-libsecret

    make -C contrib/credential/libsecret clean
    # subtree installation
    make -C contrib/subtree DESTDIR=${pkgdir} install install-man
    # the rest of the contrib stuff
    find contrib/ -name '.gitignore' -delete
    cp -a ./contrib/* ${pkgdir}/usr/share/git/

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
