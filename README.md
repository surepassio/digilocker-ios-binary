# DigilockerSDK

Aadhaar-based identity verification through DigiLocker for iOS apps, by
[Surepass](https://surepass.io). Distributed as a prebuilt XCFramework via
Swift Package Manager; each release is checksummed and verified automatically
by SPM.

Building with React Native? Use
[`@surepass/digilocker-react-native-sdk`](https://www.npmjs.com/package/@surepass/digilocker-react-native-sdk)
instead — it wraps this same framework.

## Requirements

| Requirement | Minimum |
| ----------- | ------- |
| iOS         | 15.0    |
| Swift       | 5.7     |
| Xcode       | 14.0    |

No `Info.plist` entries and no runtime permissions are required.

## Installation

In Xcode: **File → Add Package Dependencies**, then enter:

```
https://github.com/surepassio/digilocker-ios-binary.git
```

Or in `Package.swift`:

```swift
dependencies: [
    .package(url: "https://github.com/surepassio/digilocker-ios-binary.git", from: "1.0.0")
]
```

## Quick start

`InitSDKView` is a SwiftUI view that runs the whole verification flow and
reports back through two callbacks. Present it, then dismiss it when either
callback fires:

```swift
import SwiftUI
import DigilockerSDK

struct VerificationScreen: View {
    @State private var startVerification = false
    let token: String   // from your backend — see below

    var body: some View {
        NavigationView {
            VStack {
                Button("Verify with DigiLocker") {
                    startVerification = true
                }

                NavigationLink(
                    destination: InitSDKView(
                        environment: Env.SANDBOX.rawValue,
                        token: token,
                        onCompletion: { clientId in
                            startVerification = false
                            // verification succeeded — fetch results
                            // server-side using this clientId
                        },
                        onFailure: { error in
                            startVerification = false
                            // verification failed or was cancelled
                        }
                    ),
                    isActive: $startVerification
                ) { EmptyView() }
            }
        }
    }
}
```

The `token` comes from your backend by calling Surepass's DigiLocker
Initialize API with your API credentials. Never embed API credentials in the
app — only the short-lived token.

## Environments

Use the `Env` enum rather than raw strings:

```swift
Env.SANDBOX.rawValue   // testing
Env.PROD.rawValue      // live
```

Only `"SANDBOX"` is matched explicitly — any other string routes to
production.

## API reference

### `InitSDKView`

```swift
public init(
    environment: String,
    token: String,
    onCompletion: @escaping (String) -> Void,
    onFailure: @escaping (String) -> Void
)
```

| Parameter      | Description                                            |
| -------------- | ------------------------------------------------------ |
| `environment`  | `Env.SANDBOX.rawValue` or `Env.PROD.rawValue`          |
| `token`        | Token from the DigiLocker Initialize API               |
| `onCompletion` | Called on success with the verification's `clientId`   |
| `onFailure`    | Called with an error message on failure or cancellation |

## Support

[techsupport@surepass.app](mailto:techsupport@surepass.app)
