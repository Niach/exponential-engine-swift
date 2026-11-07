# ExponentialEngine 0.1.1

The Engine for Swift: the runtime that runs Apps (instances, lifecycle,
records, the outbox, consent and the capability broker) over your app's
providers (Keychain vault, windows, native capabilities) on iOS 15+ and
macOS 12+. Painting stays in the app (genUI's `ExponentialUI`). Docs:
**https://exponential.at/sdk/apps/**; the API:
`apps/desktop/crates/eengine-ffi/README.md` in the monorepo.

```swift
// Package.swift
.package(url: "https://github.com/Niach/exponential-engine-swift", exact: "0.1.1")
// target
.product(name: "ExponentialEngine", package: "exponential-engine-swift")
```

```swift
import ExponentialEngine

let engine = try Engine(
    host: EngineHost(kind: "exported", platform: "ios", formFactor: "phone", locale: "en",
                     zone: nil, network: .off, dataDir: appSupport.path),
    identity: .local(deviceId: deviceId),
    providers: EngineProviders(vault: keychain, window: navigator)
)
let app = try engine.load(fileJson: packageFile)
_ = try app.event(eventJson: #"{"type":"launch"}"#)
```

The platform's capabilities, one product each (`ExponentialEnginePhotos`,
`-Files`, `-Share`, `-Print`, `-Notifications`: link only what your app
declares, ITMS-90683) or all five as `ExponentialEngineCapabilities` (UIKit,
PhotoKit and UserNotifications).
Their calls block: the Engine makes them from a worker thread, never the main one.

```swift
import ExponentialEngineCapabilities

let providers = EngineProviders(vault: keychain, window: navigator,
                                capabilities: EngineCapabilities.ios())
// UNUserNotificationCenterDelegate didReceive:
if let tap = NotificationTaps.tapJson(response: response) { _ = try app.event(eventJson: tap) }
```

Kotlin: `at.exponential:engine:0.1.1` on Maven Central. Licence: see
LICENSE and NOTICE. Generated from the Exponential monorepo
(`apps/desktop/crates/eengine-ffi`, tag `eengine-v0.1.1`); issues and
changes go there.
