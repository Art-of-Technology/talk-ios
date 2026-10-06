<!--
  - SPDX-FileCopyrightText: 2026 Nextcloud GmbH and Nextcloud contributors
  - SPDX-License-Identifier: GPL-3.0-or-later
-->
# iOS upstream review context

- Fork: `Art-of-Technology/talk-ios`; source: `nextcloud/talk-ios`, reference branch `main`.
- Verify the fork integration branch from live branch/PR evidence before preparing any future PR; do not infer it from upstream `main` or local checkout names. Never target upstream.
- Read root `AGENTS.md`, `README.md`, `Podfile`, `.github/workflows/talk-ios-tests.yml` and `.github/workflows/swiftlint.yml` for current requirements.
- These are customization-sensitive surfaces, not assertions that fork customizations exist. Establish the actual fork delta and candidate overlap before assessing compatibility.

## Source and compatibility map

| Surface | Review focus and evidence |
| --- | --- |
| Authentication and accounts | `NextcloudTalk/Login/`, `NextcloudTalk/Database/TalkAccount.*`, network layer: browser authentication, callback handling, expiry, multi-account routing and secure credential cleanup. |
| Native notifications and push | `NextcloudTalk/Notifications/`, `NextcloudTalk/Network/NCPushProxySessionManager.swift`, `NotificationServiceExtension/`, `docs/notifications.md`: registration, decryption, account isolation, notification actions and extension/background behavior. |
| Chat, bots and buttons | `NextcloudTalk/Chat/`, `NextcloudTalk/Rooms/Bots/` and notification actions: trace changed bot/rich-message payloads through native rendering and handlers; verify capability gating, permissions, fallbacks and account/room routing. Do not assume web-only bot buttons have native support. |
| Calls and signaling | `NextcloudTalk/Calls/` (including CallKit), `NextcloudTalk/WebRTC/`, `BroadcastUploadExtension/`: incoming calls, audio routes, reconnection, screen sharing, background transitions and supported signaling modes. |
| Deep links and sharing | `NextcloudTalk/SceneDelegate.swift`, login callbacks, `ShareExtension/`, `TalkIntents/`: cold/warm launch, universal/custom links, account selection, authorization and safe external navigation. |
| Signing, privacy and persistence | `NextcloudTalk.xcodeproj/`, `Podfile`, target entitlements, `NextcloudTalk/Settings/NCAppBranding.*`, `NextcloudTalk/PrivacyInfo.xcprivacy`, database changes: bundle/app-group consistency, keychain/push entitlements, migrations, privacy declarations and sensitive logging. Keep deployment branding and signing material in ignored configuration. |

## Validation map for a future authorized implementation

- Native build/tests need macOS and Xcode; do not claim iOS execution from a Windows-only session. Use `pod install` and open `NextcloudTalk.xcworkspace` per README; inspect its submodule and dependency patch hooks before changing dependencies.
- Read the current workflow for Xcode/simulator versions; the README's example destination may lag. Discover an available matching simulator rather than copying an unavailable destination.
- Run SwiftLint using `.swiftlint.yml`, matching `.github/workflows/swiftlint.yml`.
- Build: `xcodebuild build-for-testing -workspace NextcloudTalk.xcworkspace -scheme NextcloudTalk -destination '<available simulator destination>'`.
- Tests: `xcodebuild test -workspace NextcloudTalk.xcworkspace -scheme NextcloudTalk -destination '<available simulator destination>'`; use `start-instance-for-tests.sh` with Docker for the documented local server setup and review the workflow's server/Talk compatibility matrix.
- `NextcloudTalkTests/Unit/` covers notifications, chat, peer connections and signaling; `Integration/` covers notification controller, chat, rooms, signaling and settings; `UI/` covers login, rooms and calls. Choose coverage from the candidate's actual affected paths.
- Review extension builds for `NotificationServiceExtension`, `ShareExtension`, `BroadcastUploadExtension` and `TalkIntents`, not just the main app. Confirm bundle IDs, app groups and entitlements agree across affected targets without committing deployment identity.
- Device smoke checks: APNs and incoming CallKit calls, notification open/reply under account switching and lock screen, expired login, bot/action fallback, deep links, share intents, audio routes and camera/microphone permissions. Simulator success alone does not prove delivery or production signing.
- Record tested server/Talk capabilities, OS versions and unavailable device/signing/push checks. A documentation-only review does not require a native build; recommendations must still name candidate-specific compatibility and validation gaps.
