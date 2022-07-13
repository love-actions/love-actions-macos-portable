# love-actions-macos-portable

## About

Github Action for building & deploying macOS `.app(zipped)`, `.dmg` and `.pkg` packages of a [LÖVE](https://love2d.org/) framework based game.

### Note

This action would help you distribute your game ***OUTSIDE*** the App Store.
If you want to distribute through the App Store, please use [love-actions-macos-appstore](https://github.com/marketplace/actions/)

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
  uses: 26F-Studio/love-actions-macos@main
  with:
    app-name: "My Love Game"
    bundle-id: "org.love2d.my-game"
    copyright: "Copyright © 2020-2022 XXX Co. All Rights Reserved."
    icon-path: ./assets/macOS/icon.icns
    love-package: "./game.love"
    product-name: "my_game"
    version-string: "2.3.4"
    output-folder: "./dist"
    account-username: ${{ secrets.APPLE_ACCOUNT_USERNAME }}
    account-password: ${{ secrets.APPLE_ACCOUNT_PASSWORD }}
    certificate-base64: ${{ secrets.APPLE_CERT_DEVELOPER_ID_APPLICATION }}
    certificate-password: ${{ secrets.APPLE_CERT_DEVELOPER_ID_APPLICATION_PWD }}
    certificate-type: "Developer ID Application"
    team-id: "${{ secrets.APPLE_DEVELOPER_TEAM_ID }}"
```

## All inputs

| Name                     | Required  | Default                | Description                                                                                          |
| :----------------------- | --------- | ---------------------- | ---------------------------------------------------------------------------------------------------- |
| `app-name`             | `false` | `"LÖVE for macOS"`  | App display name. Used in `platform/xcode/macosx/love-macosx.plist`                              |
| `bundle-id`            | `false` | `"org.love2d.macOS"` | App bundle id. Used in `platform/xcode/love.xcodeproj/project.pbxproj`                          |
| `copyright`            | `false` | `""`                 | App copyright info. Used in `platform/xcode/macosx/love-macosx.plist`                           |
| `icon-path`            | `false` | `"./icon.icns"`      | `.icns` format icon's path. Used in `platform/xcode/Images.xcassets/OS X AppIcon.appiconset`  |
| `love-package`         | `false` | `"./game.love"`      | `.love` game package file path                                                                     |
| `product-name`         | `false` | `"love_app"`         | Base name of the package. Used to rename products                                                    |
| `version-string`       | `false` | `"11.4"`             | App version string no more than 3 numbers. Used in `platform/xcode/love.xcodeproj/project.pbxproj` |
| `output-folder`        | `false` | `"./build"`          | Built packages output folder                                                                         |
| `account-username`     | `true`  | `""`                 | Apple ID username. Used to sign the app                                                           |
| `account-password`     | `true`  | `""`                 | App specified password. Used to sign the app                                                      |
| `certificate-base64`   | `true`  | `""`                 | Certificate base64 content. Used to sign the app                                                  |
| `certificate-password` | `true`  | `""`                 | Certificate password. Used to sign the app                                                        |
| `certificate-type`     | `true`  | `""`                 | Certificate type. Usually be `Developer ID Application`. Used to sign the app                |
| `team-id`              | `true`  | `""`                 | Developer team id. Used to sign the app                                                           |

## All outputs

| Name              | Example                 | Description                                                                                     |
| :---------------- | ----------------------- | ----------------------------------------------------------------------------------------------- |
| `package-paths` | `./build/my_game.app` | Built packages' paths in a bash-style list relative to the repository root, separated by spaces |
