# Tacit ideas / Plans / System install

* Let multiple Tacit versions coexist
* Let system package managers install an unversioned `tacit` if they want to
* Use [Repology](https://repology.org) to track system packaging status
* Maybe go through the process for an Arch Linux [community](https://wiki.archlinux.org/index.php/AUR_Trusted_User_Guidelines#Rules_for_Packages_Entering_the_.5Bcommunity.5D_Repo) package
* Go for Chocolatey [trusted](https://chocolatey.org/faq#what-is-a-trusted-package) because it'd be a first-party package
* Go for Debian [sponsorship](https://www.debian.org/doc/manuals/distribute-deb/distribute-deb.html#getting-a-sponsor) (no requirements besides Debian Free Software Guidelines)
* Use (preferably read-only) [subrepos](https://github.com/ingydotnet/git-subrepo) if that helps management/linking, to keep all code in sync
* Maybe use [Open Build Service](https://openbuildservice.org/) to get support for many distros
