# VPKOpenCV

Stripped-down build of OpenCV 3.4.13 whose only purpose is to run a fully functioning
`contrib/tracking` module for VPKitMaker. Everything else is left out.

## 3.4.13x (October 2026): `opencv2.xcframework`

Distributed as an XCFramework so that hosts build natively on every current target:

| Slice | Architectures | Platform |
|---|---|---|
| `ios-arm64` | arm64 | iOS device |
| `ios-arm64_x86_64-simulator` | arm64, x86_64 | iOS Simulator |

Minimum iOS 15.0, no bitcode, static linkage, `libc++`. iOS 26 and later simulator
runtimes are arm64-only (no Rosetta), which is why the old `opencv2.framework`
(device arm64 + x86_64 simulator only, version 3.4.13s / 3.4.13d) could no longer run the
editor on a simulator.

The CocoaPod `VPKOpenCV 3.4.13x` points at the release asset
`opencv2.xcframework.zip` attached to the GitHub release tagged `3.4.13x`, with its
SHA-256 in the podspec.

**Modules:** core, imgproc, video, tracking, plot, world (210 objects, identical to the
previous builds). Image codecs, protobuf, quirc and ITT are disabled; GOTURN needs `dnn`
and throws at runtime, as before.

## Build (3.4.13x)

Sources: `opencv` and `opencv_contrib` at tag `3.4.13`, with the contrib checkout reduced
to `modules/tracking` and `modules/plot`. OpenCV 3.4's `platforms/ios/build_framework.py`
cannot produce arm64 simulator objects or an XCFramework, so `build/build_framework_xc.py`
(a patched copy of that script) builds each platform separately and assembles the
XCFramework with `xcodebuild -create-xcframework`. One source patch is needed for the
iOS 26 SDK: remove the `#define fdopen(fd,mode) NULL` stub from `3rdparty/zlib/zutil.h`
(as in upstream zlib 1.2.12). Use CMake 3.x (3.31 was used; 3.4.13 declares
`cmake_minimum_required(3.0)`, which CMake 4 rejects).

```
python3 build/build_framework_xc.py \
  --opencv ../opencv --contrib ../contrib-min \
  --iphoneos_archs arm64 --iphonesimulator_archs arm64,x86_64 \
  --iphoneos_deployment_target 15.0 --disable-bitcode \
  --without calib3d --without dnn --without features2d --without flann --without highgui \
  --without imgcodecs --without java --without js --without ml --without objdetect \
  --without photo --without python2 --without python3 --without shape --without stitching \
  --without superres --without ts --without videoio --without videostab --without viz \
  --disable JPEG --disable PNG --disable TIFF --disable WEBP --disable OPENEXR \
  --disable JASPER --disable PROTOBUF --disable QUIRC --disable ITT \
  out
```

Verify with `lipo -info` on each slice and `vtool -show-build` on an object from the
simulator slice (`platform IOSSIMULATOR`).

## Release

1. Zip the XCFramework preserving symlinks: `zip -r -y opencv2.xcframework.zip opencv2.xcframework`.
2. `shasum -a 256 opencv2.xcframework.zip` and put the digest in `VPKOpenCV.podspec`.
3. Commit, tag (`3.4.13x`), push, create the GitHub release with the zip attached.
4. `pod repo push veepionyc VPKOpenCV.podspec --allow-warnings`.

## Earlier builds

`3.4.13s` (static) and `3.4.13d` (dynamic) were fat `opencv2.framework` builds with
arm64-iPhoneOS and x86_64-iPhoneSimulator slices, made with the stock
`platforms/ios/build_framework.py` from the same sources (see the git history for the
exact command). They remain in the Specs repo for older hosts.
