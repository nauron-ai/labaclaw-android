# labaclaw-android

Native Android access layer for `labaclaw`.

This repository owns the Android app, its Kotlin/Compose UI, background services, and the Rust JNI/UniFFI bridge. The runtime itself stays in [`labaclaw`](https://github.com/nauron-ai/labaclaw).

## Requirements

- JDK 17
- Android SDK + NDK r25.2.9519653 or newer
- Rust toolchain
- Android Rust targets:
  - `aarch64-linux-android`
  - `armv7-linux-androideabi`
  - `x86_64-linux-android`
- `cargo-ndk`

Install the Rust targets and `cargo-ndk`:

```bash
rustup target add aarch64-linux-android armv7-linux-androideabi x86_64-linux-android
cargo install cargo-ndk
```

## Repository Layout

- `app/`: Android application module
- `android-bridge/`: Rust bridge crate packaged into `jniLibs`
- `.github/workflows/`: Android CI and release pipelines

## Local Build

Build debug APK:

```bash
./gradlew assembleDebug
```

Build unsigned release APK:

```bash
./gradlew assembleRelease
```

Build unsigned release AAB:

```bash
./gradlew bundleRelease
```

Build the native bridge directly:

```bash
cargo check --manifest-path android-bridge/Cargo.toml
cargo ndk \
  -t arm64-v8a \
  -t armeabi-v7a \
  -t x86_64 \
  -o app/src/main/jniLibs \
  build --release --manifest-path android-bridge/Cargo.toml
```

The Gradle build can also generate native libs via the `buildRustLibrary` task.

## Release Artifacts

- CI on pull requests and pushes builds:
  - native `jniLibs`
  - debug APK
  - release APK
  - release AAB
  - lint report
- Tag pushes `v*` publish GitHub release assets from this repo.
- If signing secrets are configured, release APK/AAB artifacts are signed.
- If signing secrets are not configured, the release workflow still publishes unsigned artifacts.

## Signing Secrets

Signed release builds use these GitHub secrets:

- `ANDROID_KEYSTORE_BASE64`
- `ANDROID_KEYSTORE_PASSWORD`
- `ANDROID_KEY_ALIAS`
- `ANDROID_KEY_PASSWORD`

`ANDROID_KEYSTORE_BASE64` should contain the base64-encoded keystore file.

## Relationship to `labaclaw`

- `labaclaw`: runtime, CLI, daemon, Android headless binary target
- `labaclaw-android`: native Android app and JNI bridge

For running the headless runtime on Android via Termux or ADB, use the Android runtime docs in [`labaclaw`](https://github.com/nauron-ai/labaclaw/blob/main/docs/android-setup.md).

## License

Dual-licensed under MIT or Apache-2.0.
