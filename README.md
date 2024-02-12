This is a new [**React Native**](https://reactnative.dev) project, bootstrapped using [`@react-native-community/cli`](https://github.com/react-native-community/cli).

Project created with: `npx react-native@latest init dev_settings_prod --skip-install`

This project is intended to demonstrate react-native-code-push issue [#2658](https://github.com/microsoft/react-native-code-push/issues/2658).

# Steps to reproduce

1. Install all dependencies: `yarn`
2. Generate the release build for Android: `yarn react-native build-android --mode=release`
3. Use [bundletool](https://developer.android.com/tools/bundletool) to extract the `AndroidManifest.xml` file from the release bundle: `bundletool dump manifest --bundle android/app/build/outputs/bundle/release/app-release.aab > AndroidManifest-prod.xml`
4. Open the extracted file: `open AndroidManifest-prod.xml`
5. Verify that it has an entry for React Native's `DevSettingsActivity`
6. Open the manifest merger report to find where that activity is coming from: `open android/app/build/outputs/logs/manifest-merger-release-report.txt`
7. Search for `DevSettingsActivity`:

```
activity#com.facebook.react.devsupport.DevSettingsActivity
ADDED from [:react-native-code-push] /Users/rfontes/Sites/dev_settings_prod/node_modules/react-native-code-push/android/app/build/intermediates/merged_manifest/release/AndroidManifest.xml:10:9-86
   android:name
      ADDED from [:react-native-code-push] /Users/rfontes/Sites/dev_settings_prod/node_modules/react-native-code-push/android/app/build/intermediates/merged_manifest/release/AndroidManifest.xml:10:19-83
```

# How to fix

1. Apply the suggested patch: `yarn patch-package`
2. Generate the production build and extract the `AndroidManifest.xml` file again:

```bash
yarn react-native build-android --mode=release

bundletool dump manifest \
   --bundle android/app/build/outputs/bundle/release/app-release.aab \
   > AndroidManifest-prod.xml

open AndroidManifest-prod.xml
```

We can see that the `DevSettingsActivity` wasn't included in the release `AndroidManifest.xml` file.
