# Zonemaster LDNS packaging

How to build and upload deb packages to packages1.


## Setup variables

This instruction uses two environment variables.

 * VERSION - Upstream version.
 * REV - Packaging revision. Reset to "iis1" for each new VERSION.

For releases from the develop branch a suffix must be appended to the
VERSION variable. Use the pattern "V-N-C" where V is the last upstream
release version, N is the number of commits since that release and C is
the shortsha of the latest commit. E.g. "1.0.2-13-8e093fc".

    VERSION=1.0.2-13-8e093fc
    REV=iis2


## Download dist tarball

This step is only applicable when packaging a proper upstream release.
Otherwise follow the upstream instructions for building the dist tarball.

    wget http://search.cpan.org/CPAN/authors/id/Z/ZN/ZNMSTR/Zonemaster-LDNS-${VERSION}.tar.gz


## Create original tarball

    tar xzf Zonemaster-LDNS-${VERSION/%-*/}.tar.gz
    mv Zonemaster-LDNS-${VERSION/%-*/} libzonemaster-ldns-perl-${VERSION}
    tar czf ../libzonemaster-ldns-perl_${VERSION}.orig.tar.gz libzonemaster-ldns-perl-${VERSION}
    rm -rf libzonemaster-ldns-perl-${VERSION}
    rm -f Zonemaster-LDNS-${VERSION/%-*/}.tar.gz


## Extract original tarball

    tar xzf ../libzonemaster-ldns-perl_${VERSION}.orig.tar.gz --strip-components 1 --exclude .gitignore


## Build source package

    dpkg-buildpackage -S -d -k92388F75


## Build binary packages

    sudo DIST=precise cowbuilder --build ../libzonemaster-ldns-perl_${VERSION}-${REV}.dsc


## Upload packages

    sudo -ubuild dput packages1-pdt ~/packages/libzonemaster-ldns-perl_${VERSION}-${REV}_amd64.changes


## Tag release

    git tag ${VERSION}-${REV}
    git push --tags
