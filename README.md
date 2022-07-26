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
  uses: 26F-Studio/love-actions-macos-portable@main
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

## All inputs

| Name                                | Required | Default              | Description                                                                                        |
| :---------------------------------- | -------- | -------------------- | -------------------------------------------------------------------------------------------------- |
| `app-name`                          | `false`  | `"LÖVE for macOS"`   | App display name. Used in `platform/xcode/macosx/love-macosx.plist`                                |
| `bundle-id`                         | `false`  | `"org.love2d.macOS"` | App bundle id. Used in `platform/xcode/love.xcodeproj/project.pbxproj`                             |
| `copyright`                         | `false`  | `""`                 | App copyright info. Used in `platform/xcode/macosx/love-macosx.plist`                              |
| `icon-path`                         | `false`  | `"./icon.icns"`      | `.icns` format icon's path. Used in `platform/xcode/Images.xcassets/OS X AppIcon.appiconset`       |
| `love-ref`                          | `false`  | `"c35356c841976eb6f370347b81eec845d5520338"` | LÖVE git ref. Could be commit hash, tags or branch name                    |
| `love-patch`                        | `false`  | `""`                 | Git patch file path for the LÖVE repo. The patch must start from `love-ref`. You can use `git diff -p <tag1> <tag2>` to get the patch file |
| `love-package`                      | `false`  | `"./game.love"`      | `.love` game package file path                                                                     |
| `libs-path`                         | `false`  | `""`                 | Path to the libraries folder. Would copy all contents to `platform/xcode/` excluding top folder    |
| `extra-assets`                      | `false`  | `""`                 | List of folder & file paths to be added to `platform/xcode/`. Separated by spaces                  |
| `product-name`                      | `false`  | `"love_app"`         | Base name of the package. Used to rename products                                                  |
| `version-string`                    | `false`  | `"11.4"`             | App version string no more than 3 numbers. Used in `platform/xcode/love.xcodeproj/project.pbxproj` |
| `output-folder`                     | `false`  | `"./build"`          | Built packages output folder                                                                       |
| `account-username`                  | `true`   | `""`                 | Apple ID username. Used to sign the app                                                            |
| `account-password`                  | `true`   | `""`                 | App specified password. Used to sign the app                                                       |
| `developer-id-application-base64`   | `true`   | `""`                 | Developer ID Application certificate base64 content. Used to sign the app                          |
| `developer-id-application-password` | `true`   | `""`                 | Developer ID Application certificate password. Used to sign the app                                |
| `team-id`                           | `true`   | `""`                 | Developer team id. Used to sign the app                                                            |
| `developer-id-installer-base64`     | `false`  | `""`                 | Developer ID Installer certificate base64 content. Used to sign the .pkg                           |
| `developer-id-installer-password`   | `false`  | `""`                 | Developer ID Installer certificate password. Used to sign the .pkg                                 |
| `dmg-background-path`               | `false`  | `""`                 | DMG background path. Used to create .dmg                                                           |
| `dmg-icon-position`                 | `false`  | `""`                 | DMG icon position. Used to create .dmg                                                             |
| `dmg-icon-size`                     | `false`  | `""`                 | DMG icon size. Used to create .dmg                                                                 |
| `dmg-link-position`                 | `false`  | `""`                 | DMG drop link position. Used to create .dmg                                                        |
| `dmg-text-size`                     | `false`  | `""`                 | DMG text size. Used to create .dmg                                                                 |
| `dmg-volume-icon-path`              | `false`  | `""`                 | DMG volume icon path. Used to create .dmg                                                          |
| `dmg-volume-name`                   | `false`  | `""`                 | DMG volume name. Used to create .dmg                                                               |
| `dmg-window-position`               | `false`  | `""`                 | DMG window position. Used to create .dmg                                                           |
| `dmg-window-size`                   | `false`  | `""`                 | DMG window size. Used to create .dmg                                                               |

## All outputs

| Name              | Example                                                         | Description                                                                                     |
| :---------------- | --------------------------------------------------------------- | ----------------------------------------------------------------------------------------------- |
| `package-paths`   | `./build/my_game.zip ./build/my_game.pkg ./build/my_game.dmg`   | Built packages' paths in a bash-style list relative to the repository root, separated by spaces |
