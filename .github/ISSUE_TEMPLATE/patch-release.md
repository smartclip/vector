---
name: Vector patch release
about: Use this template for a new patch release.
title: "Vector [version] release"
labels: "domain: releasing"
---

Before the release:

- [ ] Create a new release preparation branch from the current release branch
  - `git fetch && git checkout v0.<current minor version> && git checkout -b prepare-v0.<new version number>`
- [ ] Cherry-pick in all commits to be released from the associated release milestone
- [ ] Run `cargo vdev generate release-cue` to generate a new cue file for the release
- [ ] Add `changelog` key to generated cue file
  - [ ] `git log --no-merges --cherry-pick --right-only <last release tag>...`
  - [ ] Should be hand-written list of changes
        ([example](https://github.com/vectordotdev/vector/blob/9fecdc8b5c45c613de2d01d4d2aee22be3a2e570/website/cue/reference/releases/0.19.0.cue#L44))
  - [ ] Add description key to the generated cue file with a description of the release (see
        previous releases for examples).
- [ ] Update version number in `distribution/install.sh`
- [ ] Add new version to `website/cue/reference/versions.cue`
- [ ] Create new release md file by copying an existing one in `./website/content/en/releases/` and
      updating version number
- [ ] Bump the release number in the `Cargo.toml` to the current version number
- [ ] Run `cargo check` to regenerate `Cargo.lock` file
- [ ] Commit these changes
- [ ] Open PR against the release branch (`v0.<new version number>`) for review
- [ ] PR approval

On the day of release:

- [ ] Rebase the release preparation branch on the release branch
    - [ ] Ensure release date in cue matches current date.
    - [ ] Squash the release preparation commits (but not the cherry-picked commits!) to a single
        commit. This makes it easier to cherry-pick to master after the release. 
- [ ] Merge release preparation branch into the release branch
    - `git co v0.<current minor version> && git merge --ff-only prepare-v0.<current minor version>.<patch>`
- [ ] Tag new release
  - [ ] `git tag v0.<minor>.<patch> -a -m v0.<minor>.<patch>`
  - [ ] `git push origin v0.<minor>.<patch>`
- [ ] Wait for release workflow to complete
  - Discoverable via [https://github.com/timberio/vector/actions/workflows/release.yml](https://github.com/timberio/vector/actions/workflows/release.yml)
- [ ] Release updated Helm chart. See [releasing Helm chart](https://github.com/vectordotdev/helm-charts#releasing).
- [ ] Once Helm chart is released, updated Vector manifests
    - Run `cargo vdev generate manifests` and open a PR with changes
- [ ] Add docker images to [https://github.com/DataDog/images](https://github.com/DataDog/images/tree/master/vector) to have them available internally.
- [ ] Cherry-pick any release commits from the release branch that are not on `master`, to `master`
- [ ] Kick-off post-mortems for any regressions resolved by the release
