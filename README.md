# Android SDK Tools for aarch64 Linux

Fork of [lzhiyong/android-sdk-tools](https://github.com/lzhiyong/android-sdk-tools), updated for **Android 16 (API 36) / AGP 9.x** compatibility.

Builds `aapt2`, `aidl`, `zipalign`, `adb`, `fastboot` and other Android SDK tools as **static aarch64 Linux binaries** from AOSP source.

## Why this fork?

The upstream release (35.0.2) ships aapt2 v2.19, which is incompatible with AGP 9.1.0+ (requires v2.20+). Release builds with resource shrinking produce broken APKs (missing AndroidManifest.xml). This fork builds from AOSP `android-16.0.0_r4` to produce compatible tools.

## Changes from upstream

- Default AOSP tag: `android-16.0.0_r4` (was `master`)
- Added new aapt2 source files for Android 16: `FlaggedXmlVersioner.cpp`, `FlagNotEnabledResourceRemover.cpp`, `Unicode_data.cpp`
- GitHub Actions CI for automated aarch64 builds

## Pre-built binaries

See [Releases](https://github.com/1orz/android-sdk-tools/releases) for pre-built aarch64 static binaries.

## How to build

### 1. Install dependencies

```bash
# Ubuntu/Debian
sudo apt install cmake ninja-build python3 python3-requests bison flex golang protobuf-compiler
```

### 2. Clone AOSP source (~5GB)

```bash
python3 get_source.py
# or specify a tag
python3 get_source.py --tags android-16.0.0_r4
```

### 3. Build protobuf for host (generates protoc)

```bash
cd src/protobuf && mkdir build && cd build
cmake -GNinja -Dprotobuf_BUILD_TESTS=OFF ..
ninja -j$(nproc --all)
cd ../../..
```

### 4. Build with NDK

```bash
# Build all tools
python3 build.py \
    --ndk=/path/to/android-ndk-r28c \
    --abi=arm64-v8a \
    --protoc=src/protobuf/build/protoc

# Or build a single target
python3 build.py \
    --ndk=/path/to/android-ndk-r28c \
    --abi=arm64-v8a \
    --protoc=src/protobuf/build/protoc \
    --target=aapt2

# Output: build/android-sdk-tools-aarch64.zip
```

## Use with Gradle

```properties
# gradle.properties — bypass AGP's x86_64-only Maven aapt2
android.aapt2FromMavenOverride=/path/to/aapt2
```

## License

Apache 2.0 — see [LICENSE.txt](LICENSE.txt)
