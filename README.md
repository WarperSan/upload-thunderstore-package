# upload-thunderstore-package
Github Action to upload a package to Thunderstore using ThunderPipe

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

      - uses: WarperSan/upload-thunderstore-package@v1
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
          token: ${{ secrets.THUNDERSTORE_API_TOKEN }}
```
