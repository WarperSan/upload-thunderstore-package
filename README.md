# upload-thunderstore-package
A GitHub Action to validate, package and publish mods to Thunderstore automatically using ThunderPipe.

## Usage

> [!CAUTION]
> Do not put your token directly into your workflow. Doing so will allow anyone to act under your name

```yaml
name: 'Publish Package'

on: 
  push:
    tags:
      - '*'

jobs:
  publish:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-dotnet@v4
        with:
          dotnet-version: '9.x'

      - name: Restores Dependencies & Tools
        shell: bash
        run: dotnet restore && dotnet tool restore
      
      - name: Build
        shell: bash
        run: dotnet build --no-restore -c "Release"
      
      - uses: WarperSan/upload-thunderstore-package@v1.0.1
        with:
          community: 'lethal-company'
          team: 'LethalCompanyModding'
          categories: |
            mods
            tweaks-and-quality-of-life
            performance
            furniture
          name: 'ShipInventoryUpdated'
          description: 'Adds an inventory to the ship, allowing it to store items and retrieve them.'
          version: ${{ github.ref_name }}
          dependencies: |
            BepInEx-BepInExPack-5.4.2100
            Evaisa-LethalLib-1.1.1
            WhiteSpike-Interactive_Terminal_API-1.2.0
          website: 'https://github.com/LethalCompanyModding/ShipInventoryCommunity'
          files: |
            ./src/ShipInventoryUpdated/bin/Release/ShipInventoryUpdated.dll
          token: ${{ secrets.THUNDERSTORE_API_TOKEN }}
```

## Features

### File Mapping

The input `files` allows you to control the folder structure within the folder. By default, any file will be copied directly into `plugins/`. However, you can use the ` -> ` operator to force them into a specific folder.

Here is an example:
```yaml
files: |
  ./src/bin/Release/MyMod.dll
  ./src/Resources/*.bundle
  ./src/Resources/Localization/* -> Lang
```
```
plugins/
|- MyMod.dll
|- first.bundle
|- second.bundle
|- Lang/
    |- en.json
    |- fr.json
    |- es.txt
    |- rs.md
    ...
```

## Modify Listing Files

By default, the action assumes the listing files are present from the root folder (`./icon.png`, `./README.md`, etc.). However, you can point to where these files are located.

Here is an example:
```
./
|- src/
    |- MyMod.cs
    ...
|- Resources/
    |- icon.png
    ...
|- README.md
|- CHANGELOG.md
|- LICENSE.txt
```
```yaml
package-files: |
  ./README.md
  ./CHANGELOG.md
  ./LICENSE.txt
  ./Resources/icon.png
```

## Private Thunderstore instance

By default, the action will use `https://thunderstore.io` as the instance. However, you can redirect requests to a private instance to redirect requests to a private instance. This can be done for testing against a local instance or a staging environment.

This can be done by modifying the value of `host` to the URL of your private instance.