# IP Vault Android SDK

The Android SDK for [IP Vault](https://ip.rev3rse.dev): your app opens the models, data and
content your CI sealed, in memory, only on installs that hold a licence. This repository publishes
the SDK's release binaries as a Maven repository, served at
`https://rev3rse-dev.github.io/ipvault-android/maven`.

## Use

```kotlin
// settings.gradle.kts
dependencyResolutionManagement {
    repositories {
        google()
        mavenCentral()
        maven("https://rev3rse-dev.github.io/ipvault-android/maven")
    }
}

// app/build.gradle.kts
dependencies {
    implementation("dev.rev3rse.ipvault:ipvault:0.3.3")
    // The adapter for your runtime, if an asset is a model:
    implementation("dev.rev3rse.ipvault:ipvault-onnxruntime:0.3.3")      // or ipvault-litert
    // Optional: bind a Play Integrity verdict to each activation and renewal.
    implementation("dev.rev3rse.ipvault:ipvault-playintegrity:0.3.3")
    // Optional: sell in Google Play, with every purchase checked by IP Vault.
    implementation("dev.rev3rse.ipvault:ipvault-playbilling:0.3.3")
}

android {
    defaultConfig {
        // The native core is built for these three ABIs; JNA, which it uses, brings more.
        ndk { abiFilters += listOf("arm64-v8a", "armeabi-v7a", "x86_64") }
    }
}
```

| Module | |
|---|---|
| `ipvault` | Activation, licences, and sealed assets opened in memory. Its native core is built for `arm64-v8a`, `armeabi-v7a` and `x86_64` |
| `ipvault-onnxruntime`, `ipvault-litert` | Run a sealed model through ONNX Runtime or LiteRT: requests in, results out; the app never holds the model |
| `ipvault-playintegrity` | Play Integrity tokens bound to each activation and renewal |
| `ipvault-playbilling` | The Google Play side of selling: offers, buying, restoring, acknowledging |

Android 9 (API 28) or later. The hosted service activates genuine phones only: hardware key
attestation and a locked bootloader. An emulator works against a development service.

Version 0.3.3 handles Play app-signing certificate rotation using Android's verified signing
history. Register the Play-delivered current certificate with IP Vault, then update the app in
place; sealed assets do not need resealing for the certificate change.

Your app's settings (tenant, service address, licence key) come from the developer console at
[app.rev3rse.dev](https://app.rev3rse.dev); the
[integration guide](https://ip.rev3rse.dev/docs/integration) covers the manifest, CI, and the
SDK.

## Verify

Every file has `.sha256` and `.sha512` checksums beside it. Gradle checks them for you with
dependency verification: `./gradlew --write-verification-metadata sha256 help` records them in
`gradle/verification-metadata.xml`.

From 0.2.2, each version is built by this repository's
[release workflow](.github/workflows/release.yml) on GitHub's runners, from the tagged IP Vault
source, and the run attests every AAR, POM and Gradle module file: a Sigstore-signed
[SLSA build provenance](https://slsa.dev/provenance) statement binding its SHA-256 to this
repository, the workflow, the tag and the run. Check a file Gradle downloaded (in
`~/.gradle/caches/modules-2/files-2.1/dev.rev3rse.ipvault/`) with
`gh attestation verify <file> --repo rev3rse-dev/ipvault-android`. Each version's release names
the source commit and carries its checksums and the attestation bundle. 0.2.0 was built without
provenance.

## Licence

Apache License 2.0; see [LICENSE](LICENSE). The native core includes open-source crates, listed
with their licences in [THIRD-PARTY-NOTICES.txt](THIRD-PARTY-NOTICES.txt).

Before 1.0, interfaces may change between minor versions.
