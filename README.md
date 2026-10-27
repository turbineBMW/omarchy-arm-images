# Omarchy Arm images

The installer images the [Omarchy Arm installer](https://github.com/turbineBMW/omarchy-arm-installer) downloads, and every model installer made from [omarchy-arm-installer-recipes](https://github.com/turbineBMW/omarchy-arm-installer-recipes), such as [the Surface Pro 11's](https://github.com/turbineBMW/omarchy-surface-pro-11-installer). There is one image for all of them: the apps differ in what they put on the stick, not in the image.

Each [release](https://github.com/turbineBMW/omarchy-arm-images/releases) `installer-<date>` holds:

| File | |
|---|---|
| `omarchy-arm-<date>-aarch64.iso.part-aa`, `-ab`, ... | The ISO in pieces under GitHub's 2 GiB asset limit |
| `omarchy-arm-<date>-aarch64.iso.sha256` | The SHA-256 of the whole ISO |
| `omarchy-arm-<date>-aarch64.offline.db` | The image's offline mirror database: what it installs without the network, so a recipe's other dependencies can go on the stick |

The apps take the newest `installer-*` release that is not a draft.

## The image

Omarchy's aarch64 ISO, at the omarchy-iso commit in [`iso/OMARCHY_ISO_COMMIT`](iso/OMARCHY_ISO_COMMIT), with four patches ([`iso/patches`](iso/patches)) that read a 1 GiB `OMARCHY_FW` partition on the stick:

```
OMARCHY_FW/
  DriverStore/FileRepository/<package>/...   firmware, staged with qcom-firmware-extract
  recipe/recipe.json                         the chosen recipe, if any
  recipe/key.asc                             its key
  recipe/info.json, info.json.sig            the repository's signed description
  recipe/repo/<name>.db(.sig), *.pkg.tar.*(.sig)
  recipe/official/*.pkg.tar.*(.sig)          Omarchy's and Arch's packages the recipe needs and the image lacks
```

Without a recipe on the stick, the recipe patches do nothing. How recipes work and how they are trusted is in [omarchy-arm-installer-recipes](https://github.com/turbineBMW/omarchy-arm-installer-recipes/blob/main/docs/RECIPES.md). The patches are meant for omarchy-iso itself; once they are there, the image is the stock Omarchy ISO.

## Building

```sh
bin/build-iso                       # the ISO, in omarchy-iso's privileged build container
PRERELEASE=1 bin/publish-iso ISO    # the release the apps find (needs unsquashfs and gh)
bin/iso-offline-db ISO DB           # just the offline mirror database
```

CI applies the patches to omarchy-iso and runs its `./test/all`.
