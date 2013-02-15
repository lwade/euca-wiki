The release process generally occurs in two steps:  the final compose, and general availability.

# Final Compose (SA)

* Build final packages in Jenkins
  * Make sure to use _no_ tarball suffix, as final releases don't need git info.
* Keep final Jenkins builds ( _all_ jobs in the sequence, not just binaries or tarballs) forever
* Name the final Jenkins builds according to the version number
  * Edit Build Information > DisplayName
* Sign final tarball
  * `gpg --armor --sign --detach-sign`
* Sign final packages
  * Make sure to use sigv3
* Upload to staging server
  * final tarball
  * tarball signature (*.tar.gz.asc)
  * all packages to staging server

# General Availability (GA)

* Upload everything to public download server
  * Sources include both a source package _and_ a tarball
* Tag each git repo
  * Pushing tags automatically causes sources to upload to GitHub
* Add n-v-r and commit data to [[List of Packages]] wiki page
* For a new series (e.g. x.y.0) work with IT to update where the mirrorlist points to by default

*****

[[category.releng]]