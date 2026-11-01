---
layout: post
title: "WinTips Privacy Policy"
date: 2026-11-01
last_modified_at: 2026-11-01
---

## In one sentence

**WinTips does not collect, transmit, or share any personal information.** The application runs entirely on your local machine and contains no networking code — no account, no telemetry, no analytics, no crash reporting, no advertising, and no third-party SDKs.

## 1. What we do not collect

- Identity data (name, email, phone number)
- Account or login credentials
- Keystrokes or typing history
- Clipboard contents (text, images, files)
- Usage behavior and session statistics
- Crash reports or uploaded diagnostic logs
- Device identifiers, advertising ID, IP address
- Location data

None of the above is collected, and none of it ever leaves your device. WinTips has no server and no place to send data to — even if you wanted to contribute data, the app has no ability to transmit it.

## 2. System features the app uses locally

To draw its notification popups, WinTips touches the following system features. **All of it happens in local memory only — nothing is recorded, persisted, or uploaded.**

### 1. Global keyboard hook (`WH_KEYBOARD_LL`)

The hook callback only compares the virtual-key code, and only recognizes Caps Lock, Num Lock, Scroll Lock, Insert, and the <kbd>C</kbd> / <kbd>V</kbd> / <kbd>X</kbd> keys while Ctrl is held. Every other key is passed through immediately (`CallNextHookEx`) with no logging or handling. **WinTips does not store the keys you press, and no "keylogger" or input-history data structure exists in the code.**

### 2. Clipboard state detection

The app asks the system whether any clipboard format is currently available (text, files, images, and similar). It only checks **present / absent** — it never reads what the clipboard contains, does not parse text or images, and never writes to the clipboard. The "×N" consecutive-action counter on the popup lives in memory only and is cleared when the app exits.

### 3. System appearance settings (read-only)

The app reads your dark / light mode and system accent color (`HKCU\Software\Microsoft\Windows\CurrentVersion\Themes\Personalize`) plus transparency-related DWM switches (`HKCU\Software\Microsoft\Windows\DWM`), solely to pick the popup's colors and material. The results are never written to disk.

### 4. Launch at startup (optional, can be turned off)

When enabled, the app writes a single value named `WinTips` under `HKCU\Software\Microsoft\Windows\CurrentVersion\Run`, containing the current program path; turning the option off deletes it. **This is the only registry location the app ever writes.**

### 5. Opening external links

Links in the settings window are handed to your default browser when you click them, after which the page is governed by that website's own privacy policy. The app itself makes no network requests.

## 3. Data stored locally

1. `%APPDATA%\WinTips\settings.json`: which notifications to show, display duration, animation speed, popup position, UI language, launch-at-startup flag. Preferences only; no personal information.
2. `%APPDATA%\WinTips\error.log`: timestamp and exception stack trace written when the app itself fails. Written only on a crash; may contain local file paths and .NET method names for troubleshooting, but **never your input** (the app has no text input fields).
3. `%TEMP%\.net\WinTips\`: .NET single-file runtime extraction cache, created by the runtime and safely rebuilt if deleted.
4. The `WinTips` value under `HKCU\Software\Microsoft\Windows\CurrentVersion\Run` (exists only if you enabled launch at startup).

All of the above stays in your local user profile and is never uploaded.

## 4. Sharing, cross-border transfer, and sale

**None.** There is no data to share. The app provides, sells, or transfers no information to any third party, and no cross-border data transfer of any kind takes place.

## 5. Retention and deletion

Because nothing is uploaded, deletion simply means cleaning up local files:

1. Exit WinTips (tray icon → right-click → Exit);
2. Turn off "Launch at startup" in settings, or delete the `WinTips` value under the registry `Run` key;
3. Delete the folder `%APPDATA%\WinTips`.

After these steps, no trace of the app remains on your device. For versions installed from the Microsoft Store, uninstalling does not automatically remove that folder — follow the steps above for a complete cleanup.

## 6. Children's privacy

WinTips collects no information from anyone, and therefore none from children. The app contains no advertising, in-app purchases, social features, or user-generated content.

## 7. Your rights

Since WinTips stores nothing on any server, your rights to access, correct, export, and delete personal information can all be exercised directly on your own machine — open the folders listed in Section 3 to see everything, and delete them to make it take effect.

## 8. A note about the Microsoft Store

If you obtained WinTips from the Microsoft Store, Microsoft may collect Store-related information (such as install, update, crash, and usage statistics) under its own privacy statement. That data is controlled by Microsoft, is not accessible to WinTips, and is outside the app's collection scope. See the [Microsoft Privacy Statement](https://privacy.microsoft.com/privacystatement).

## 9. Changes to this policy

If a future version introduces any feature that processes data (for example an optional update check or crash reporting), this policy will be updated **before** that feature ships, describing what is collected, why, and how to turn it off, with the change recorded below. **We will not change how data is handled without telling you.**

| Version | Date | Change |
| --- | --- | --- |
| 1.0 | 2026-11-01 | First release, for WinTips 0.31 on the Microsoft Store |

## 10. Contact

- GitHub Issues: <https://github.com/DoYouNatureQ/WinTips/issues>
- Project home: <https://github.com/DoYouNatureQ/WinTips>