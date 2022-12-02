# love-actions-macos-portable

## About

Github Action for building & deploying macOS `.app(zipped)`, `.dmg` and `.pkg` packages of a [LÖVE](https://love2d.org/) framework based game.

### Note

This action would help you distribute your game ***OUTSIDE*** the App Store.
If you want to distribute through the App Store, please use [love-actions-macos-appstore](https://github.com/marketplace/actions/love-actions-for-macos-appstore)

### Related actions

See related actions below:

[Love actions bare package](https://github.com/marketplace/actions/love-actions-bare-package)

[Love actions for testing](https://github.com/marketplace/actions/love-actions-for-testing)

[Love actions for android](https://github.com/marketplace/actions/love-actions-for-android)

[Love actions for iOS](https://github.com/marketplace/actions/love-actions-for-ios)

[Love actions for Linux](https://github.com/marketplace/actions/love-actions-for-linux)

[Love actions for macOS AppStore](https://github.com/marketplace/actions/love-actions-for-macos-appstore)

[Love actions for Windows](https://github.com/marketplace/actions/love-actions-for-windows)

## Quick example

```yaml
- name: Build macOS packages
  id: build-packages
  uses: love-action/love-actions-macos-portable@v1
  with:
    app-name: "My Love Game"
    bundle-id: "org.love2d.my-game"
    copyright: "Copyright © 2020-2022 XXX Co. All Rights Reserved."
    icon-path: ./assets/macOS/icon.icns
    love-ref: "11.4"
    love-patch: "./love.patch"
    love-package: "./game.love"
    libs-path: ./libs
    extra-assets: ./README.md ./license.txt
    product-name: "my_game"
    version-string: "2.3.4"
    output-folder: "./dist"
    account-username: ${{ secrets.APPLE_ACCOUNT_USERNAME }}
    account-password: ${{ secrets.APPLE_ACCOUNT_PASSWORD }}
    developer-id-application-base64: ${{ secrets.APPLE_CERT_DEVELOPER_ID_APPLICATION }}
    developer-id-application-password: ${{ secrets.APPLE_CERT_DEVELOPER_ID_APPLICATION_PWD }}
    team-id: "${{ secrets.APPLE_DEVELOPER_TEAM_ID }}"
    developer-id-installer-base64: ${{ secrets.APPLE_CERT_DEVELOPER_ID_INSTALLER }}
    developer-id-installer-password: ${{ secrets.APPLE_CERT_DEVELOPER_ID_INSTALLER_PWD }}
    dmg-background-path: ./assets/macOS/dmg.png
    dmg-icon-position: "287 313"
    dmg-icon-size: "128"
    dmg-link-position: "734 313"
    dmg-text-size: "12"
    dmg-volume-icon-path: ./assets/macOS/dmg.icns
    dmg-volume-name: "my_game"
    dmg-window-position: "200 120"
    dmg-window-size: "1024 604"
```

## With [Love actions bare package](https://github.com/marketplace/actions/love-actions-bare-package) and [Love actions for testing](https://github.com/marketplace/actions/love-actions-for-testing)

```yml
env:
  BUILD_TYPE: ${{ fromJSON('["dev", "release"]')[startsWith(github.ref, 'refs/tags/v')] }}
  CORE_LOVE_PACKAGE_PATH: ./core.love
  CORE_LOVE_ARTIFACT_NAME: core_love_package
  PRODUCT_NAME: my_love_app
  BUNDLE_ID: com.example.myloveapp

jobs:
  build-core:
    runs-on: ubuntu-latest
    env:
      OUTPUT_FOLDER: ./build
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: recursive
      - name: Build core love package
        uses: love-actions/love-actions-core@v1
        with:
          build-list: ./media/ ./parts/ ./Zframework/ ./conf.lua ./main.lua ./version.lua
          package-path: ${{ env.CORE_LOVE_PACKAGE_PATH }}
      - name: Upload core love package
        uses: actions/upload-artifact@v3
        with:
          name: ${{ env.CORE_LOVE_ARTIFACT_NAME }}
          path: ${{ env.CORE_LOVE_PACKAGE_PATH }}
  auto-test:
    runs-on: ubuntu-latest
    needs: build-core
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: recursive
      - name: Love actions for testing
        uses: love-actions/love-actions-test@v1
        with:
          font-path: ./parts/fonts/proportional.otf
          language-folder: ./parts/language
  build-macos-portable:
    runs-on: macos-latest
    needs: [build-core, auto-test]
    env:
      OUTPUT_FOLDER: ./build
    steps:
      - uses: actions/checkout@v3
        with:
          submodules: recursive
      # Download your core love package here
      - name: Download core love package
        uses: actions/download-artifact@v3
        with:
          name: ${{ env.CORE_LOVE_ARTIFACT_NAME }}
      # This is an example dynamic library
      - name: Download ColdClear
        uses: ./.github/actions/get-cc
        with:
          platform: macOS
          dir: ./ColdClear
      - name: Process ColdClear
        shell: bash
        run: |
          rm ./ColdClear/universal/libcold_clear.a
      - name: Build macOS packages
        id: build-packages
        uses: love-actions/love-actions-macos-portable@v1
        with:
          app-name: ${{ env.PRODUCT_NAME }}
          bundle-id: ${{ env.BUNDLE_ID }}
          copyright: "Copyright © 2019-2022 26F-Studio. Some Rights Reserved."
          icon-path: ./.github/build/macOS/${{ env.BUILD_TYPE }}/icon.icns
          love-package: ${{ env.CORE_LOVE_PACKAGE_PATH }}
          libs-path: ./ColdClear/universal/
          product-name: ${{ env.PRODUCT_NAME }}
          version-string: "1.0.0"
          output-folder: ${{ env.OUTPUT_FOLDER }}
          account-username: ${{ secrets.APPLE_ACCOUNT_USERNAME }}
          account-password: ${{ secrets.APPLE_ACCOUNT_PASSWORD }}
          team-id: "${{ secrets.APPLE_DEVELOPER_TEAM_ID }}"
          developer-id-application-base64: ${{ secrets.APPLE_CERT_DEVELOPER_ID_APPLICATION }}
          developer-id-application-password: ${{ secrets.APPLE_CERT_DEVELOPER_ID_APPLICATION_PWD }}
          developer-id-installer-base64: ${{ secrets.APPLE_CERT_DEVELOPER_ID_INSTALLER }}
          developer-id-installer-password: ${{ secrets.APPLE_CERT_DEVELOPER_ID_INSTALLER_PWD }}
          dmg-background-path: ./.github/build/macOS/${{ env.BUILD_TYPE }}/dmg.png
          dmg-icon-position: "239 203"
          dmg-icon-size: "100"
          dmg-link-position: "565 203"
          dmg-text-size: "12"
          dmg-volume-icon-path: ./.github/build/macOS/${{ env.BUILD_TYPE }}/dmg.icns
          dmg-volume-name: ${{ env.PRODUCT_NAME }}
          dmg-window-position: "200 120"
          dmg-window-size: "800 500"
      - name: Upload pkg artifact
        uses: actions/upload-artifact@v3
        with:
          name: ${{ needs.get-info.outputs.base-name }}_macOS_portable_pkg
          path: ${{ env.OUTPUT_FOLDER }}/${{ env.PRODUCT_NAME }}.pkg
      - name: Upload dmg artifact
        uses: actions/upload-artifact@v3
        with:
          name: ${{ needs.get-info.outputs.base-name }}_macOS_portable_dmg
          path: ${{ env.OUTPUT_FOLDER }}/${{ env.PRODUCT_NAME }}.dmg
      - name: Upload bare artifact
        uses: actions/upload-artifact@v3
        with:
          name: ${{ needs.get-info.outputs.base-name }}_macOS_portable_bare
          path: ${{ env.OUTPUT_FOLDER }}/${{ env.PRODUCT_NAME }}.zip
```

## All inputs

| Name                                  | Required  | Default                                        | Description                                                                                                                                     |
| :------------------------------------ | --------- | ---------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------- |
| `app-name`                          | `false` | `"LÖVE for macOS"`                          | App display name. Used in `platform/xcode/macosx/love-macosx.plist`                                                                           |
| `bundle-id`                         | `false` | `"org.love2d.macOS"`                         | App bundle id. Used in `platform/xcode/love.xcodeproj/project.pbxproj`                                                                        |
| `copyright`                         | `false` | `""`                                         | App copyright info. Used in `platform/xcode/macosx/love-macosx.plist`                                                                         |
| `icon-path`                         | `false` | `"./icon.icns"`                              | `.icns` format icon's path. Used in `platform/xcode/Images.xcassets/OS X AppIcon.appiconset`                                                |
| `love-ref`                          | `false` | `"c35356c841976eb6f370347b81eec845d5520338"` | LÖVE git ref. Could be commit hash, tags or branch name                                                                                        |
| `love-patch`                        | `false` | `""`                                         | Git patch file path for the LÖVE repo. The patch must start from `love-ref`. You can use `git diff -p <tag1> <tag2>` to get the patch file |
| `love-package`                      | `false` | `"./game.love"`                              | `.love` game package file path                                                                                                                |
| `libs-path`                         | `false` | `""`                                         | Path to the libraries folder. Would copy all contents to `platform/xcode/` excluding top folder                                               |
| `extra-assets`                      | `false` | `""`                                         | List of folder & file paths to be added to `platform/xcode/`. Separated by spaces                                                             |
| `product-name`                      | `false` | `"love_app"`                                 | Base name of the package. Used to rename products                                                                                               |
| `version-string`                    | `false` | `"11.4"`                                     | App version string no more than 3 numbers. Used in `platform/xcode/love.xcodeproj/project.pbxproj`                                            |
| `output-folder`                     | `false` | `"./build"`                                  | Built packages output folder                                                                                                                    |
| `account-username`                  | `true`  | `""`                                         | Apple ID username. Used to sign the app                                                                                                         |
| `account-password`                  | `true`  | `""`                                         | App specified password. Used to sign the app                                                                                                    |
| `developer-id-application-base64`   | `true`  | `""`                                         | Developer ID Application certificate base64 content. Used to sign the app                                                                       |
| `developer-id-application-password` | `true`  | `""`                                         | Developer ID Application certificate password. Used to sign the app                                                                             |
| `team-id`                           | `true`  | `""`                                         | Developer team id. Used to sign the app                                                                                                         |
| `developer-id-installer-base64`     | `false` | `""`                                         | Developer ID Installer certificate base64 content. Used to sign the .pkg                                                                        |
| `developer-id-installer-password`   | `false` | `""`                                         | Developer ID Installer certificate password. Used to sign the .pkg                                                                              |
| `dmg-background-path`               | `false` | `""`                                         | DMG background path. Used to create .dmg                                                                                                        |
| `dmg-icon-position`                 | `false` | `""`                                         | DMG icon position. Used to create .dmg                                                                                                          |
| `dmg-icon-size`                     | `false` | `""`                                         | DMG icon size. Used to create .dmg                                                                                                              |
| `dmg-link-position`                 | `false` | `""`                                         | DMG drop link position. Used to create .dmg                                                                                                     |
| `dmg-text-size`                     | `false` | `""`                                         | DMG text size. Used to create .dmg                                                                                                              |
| `dmg-volume-icon-path`              | `false` | `""`                                         | DMG volume icon path. Used to create .dmg                                                                                                       |
| `dmg-volume-name`                   | `false` | `""`                                         | DMG volume name. Used to create .dmg                                                                                                            |
| `dmg-window-position`               | `false` | `""`                                         | DMG window position. Used to create .dmg                                                                                                        |
| `dmg-window-size`                   | `false` | `""`                                         | DMG window size. Used to create .dmg                                                                                                            |

## All outputs

| Name              | Example                                                         | Description                                                                                     |
| :---------------- | --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `package-paths` | `./build/my_game.zip ./build/my_game.pkg ./build/my_game.dmg` | Built packages' paths in a bash-style list relative to the repository root, separated by spaces |
