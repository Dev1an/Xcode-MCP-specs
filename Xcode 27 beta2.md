# Tools Schema Reference

Xcode 27 Beta 2

Generated from JSON containing 43 tools.

## Table of Contents

- [1. Build Project](#1-build-project)
- [2. End Device Interaction Session](#2-end-device-interaction-session)
- [3. Prepare for device interaction: install and start application on a device](#3-prepare-for-device-interaction-install-and-start-application-on-a-device)
- [4. Start Device Interaction Session](#4-start-device-interaction-session)
- [5. Perform single device interaction and fetch device state](#5-perform-single-device-interaction-and-fetch-device-state)
- [6. DocumentationSearch](#6-documentationsearch)
- [7. Get Build Log](#7-get-build-log)
- [8. GetConsoleOutput](#8-getconsoleoutput)
- [9. Get Crash Issue Logs](#9-get-crash-issue-logs)
- [10. Get Field Performance Issue Logs](#10-get-field-performance-issue-logs)
- [11. GetFileCompilerFlags](#11-getfilecompilerflags)
- [12. Get Test List](#12-get-test-list)
- [13. Get Top Crash Issues](#13-get-top-crash-issues)
- [14. Get Top Field Performance Issues](#14-get-top-field-performance-issues)
- [15. Invoke a Debugger Command](#15-invoke-a-debugger-command)
- [16. Prepare For Localization](#16-prepare-for-localization)
- [17. RenderPreview](#17-renderpreview)
- [18. Run All Tests](#18-run-all-tests)
- [19. RunCodeSnippet](#19-runcodesnippet)
- [20. Run Project](#20-run-project)
- [21. Run Some Tests](#21-run-some-tests)
- [22. Stop Project](#22-stop-project)
- [23. Get String Context](#23-get-string-context)
- [24. Insert Translation](#24-insert-translation)
- [25. Read String Catalog](#25-read-string-catalog)
- [26. UpdateFileCompilerFlags](#26-updatefilecompilerflags)
- [27. Get Current File](#27-get-current-file)
- [28. Search Files](#28-search-files)
- [29. Search Content](#29-search-content)
- [30. List Workspace Issues](#30-list-workspace-issues)
- [31. List Run Destinations](#31-list-run-destinations)
- [32. List Schemes](#32-list-schemes)
- [33. List Windows](#33-list-windows)
- [34. List Directory Contents](#34-list-directory-contents)
- [35. Create Directory](#35-create-directory)
- [36. Move File](#36-move-file)
- [37. Read](#37-read)
- [38. XcodeRefreshCodeIssuesInFile](#38-xcoderefreshcodeissuesinfile)
- [39. Remove File](#39-remove-file)
- [40. Switch Run Destination](#40-switch-run-destination)
- [41. Switch Scheme](#41-switch-scheme)
- [42. Edit File](#42-edit-file)
- [43. Write File](#43-write-file)

---

## 1. Build Project

**Name:** `BuildProject`


### Description

Builds an Xcode project and waits until the build completes.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`

| Field             | Type    | Required | Description                                                  |
| ----------------- | ------- | -------: | ------------------------------------------------------------ |
| `buildForTesting` | boolean |       No | Whether to also build test targets that would not usually be included in a regular build |
| `tabIdentifier`   | string  |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `buildResult`
- `errors`
- `fullLogPath`

| Field                     | Type          | Required | Description                                                  |
| ------------------------- | ------------- | -------: | ------------------------------------------------------------ |
| `buildResult`             | string        |      Yes | The message indicating the result of the build operation     |
| `elapsedTime`             | number        |       No | The time it took to finish the build, in seconds             |
| `errors`                  | array<object> |      Yes | List of build errors                                         |
| `errors[].classification` | string        |      Yes | The type of error (error, warning, etc.)                     |
| `errors[].filePath`       | string        |       No | The file path where the error occurred                       |
| `errors[].lineNumber`     | integer       |       No | The line number where the error occurred                     |
| `errors[].message`        | string        |      Yes | The error message                                            |
| `fullLogPath`             | string        |      Yes | Path of the full log in textual format, containing the complete command lines and any output from the build tasks |


## 2. End Device Interaction Session

**Name:** `DeviceInteractionEndSession`


### Description

Closes previous device session created in DeviceInteractionStartSession.
        
Call this action once you have finished interacting with a device.
Keeping a device interaction session alive is expensive and affects the user-facing UI.
It is crucial to close the session when not in use.

### Input Schema

**Type:** `object`

**Required input fields:**

- `interactionSessionKey`

| Field                   | Type   | Required | Description                                                  |
| ----------------------- | ------ | -------: | ------------------------------------------------------------ |
| `interactionSessionKey` | string |      Yes | Device Interaction session to close. Taken from Start Device Interaction Session |

### Output Schema

**Type:** `object`

**Required output fields:**

- `userMessage`

| Field         | Type   | Required | Description             |
| ------------- | ------ | -------: | ----------------------- |
| `userMessage` | string |      Yes | A summary of the action |


## 3. Prepare for device interaction: install and start application on a device

**Name:** `DeviceInteractionInstallAndRun`


### Description

Builds, installs, and starts the current application on the currently targeted device.

Call this action to install the application on the currently targeted device and start it
so subsequent interaction calls will have the most up to date version.
Invoke this every time you modify a project, change the target device, or the previous
debug session has been disconnected and application logs are missing.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `interactionSessionKey`

| Field                   | Type          | Required | Description                                                  |
| ----------------------- | ------------- | -------: | ------------------------------------------------------------ |
| `commandLineArguments`  | array<string> |       No | Arguments that the application should be run with. Can include '$(inherited)' placeholder string for scheme-provided arguments |
| `environmentVariables`  | object        |       No | Environment variables that the application should be run with. Can contain '$(inherited)' as a key to include scheme-provided environment variables |
| `interactionSessionKey` | string        |      Yes | Device Interaction session identifier that initiates this call. Taken from Start Device Interaction Session |
| `tabIdentifier`         | string        |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `userMessage`

| Field         | Type   | Required | Description             |
| ------------- | ------ | -------: | ----------------------- |
| `userMessage` | string |      Yes | A summary of the action |


## 4. Start Device Interaction Session

**Name:** `DeviceInteractionStartSession`


### Description

Prepares a runtime for iOS interaction. This should be called as early as possible if device interaction will be needed.

Invoke this tool if you are going to perform runtime verification on a device/simulator.
This tool finds and (if necessary) boots a desired device. The action may take time,
so this should be invoked early in the process to prepare the device in parallel with ongoing feature development.
Keeping the session open is expensive, so call DeviceInteractionEndSession to close the session once
interaction is finished.

Do NOT call this if the application you are working on cannot be built and installed on an iOS device.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `sessionIdentifier`

| Field               | Type   | Required | Description                                                  |
| ------------------- | ------ | -------: | ------------------------------------------------------------ |
| `deviceIdentifier`  | string |       No | The UUID/ECID/name/OS version/type of the device to perform interaction. Accepts any values and tries to match the best candidate. |
| `sessionIdentifier` | string |      Yes | Unique human-friendly identifier for this session (e.g. "Verify Login Flow"). Used in logs and UI - use Title Case. |
| `tabIdentifier`     | string |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `summary`
- `skillToTrigger`
- `interactionSessionKey`
- `deviceUUID`
- `deviceIsSimulator`

| Field                   | Type    | Required | Description                                                  |
| ----------------------- | ------- | -------: | ------------------------------------------------------------ |
| `deviceIsSimulator`     | boolean |      Yes | true if the device for this session is a simulator, otherwise false |
| `deviceUUID`            | string  |      Yes | UUID of the device to be used for the interaction session    |
| `interactionSessionKey` | string  |      Yes | A secret key to identify the interaction session             |
| `skillToTrigger`        | string  |      Yes | The name of the skill that will handle the next step(s)      |
| `summary`               | string  |      Yes | Notes about the session and guidance on next steps for a caller |


## 5. Perform single device interaction and fetch device state

**Name:** `DeviceInteractionSynthesize`


### Description

Synthesizes device events (tap, swipe, type, etc.) on a physical device or simulator
and captures the resulting state.

This action provides a unified interface for device interaction. After executing the
specified interaction command, it captures a screenshot and UI hierarchy, returning
paths to both files.
Use this to:
- Tap UI elements at specific coordinates
- Swipe/scroll through content
- Type text into fields
- Press hardware buttons
- Capture current device state (screenshot + hierarchy)
- Change device orientation

Always use positions based on the most recent hierarchy dump. Never try to guess positions from a screenshot only.

### Input Schema

**Type:** `object`

**Required input fields:**

- `interactSessionKey`

| Field                | Type   | Required | Description                                                  |
| -------------------- | ------ | -------: | ------------------------------------------------------------ |
| `interactSessionKey` | string |      Yes | Device Interaction session key to work with. Taken from Start Device Interaction Session |
| `interactionCommand` | string |       No | The interaction command to execute (e.g., 't 100 200' for tap) |

### Output Schema

**Type:** `object`

**Required output fields:**

- `thumbnailScreenshotPath`
- `screenshotPath`
- `hierarchyPath`
- `logsPath`
- `applicationState`

| Field                     | Type   | Required | Description                                                  |
| ------------------------- | ------ | -------: | ------------------------------------------------------------ |
| `applicationState`        | string |      Yes | State of the run application: NotRun, Running, Stopped, Crashed, Disconnected. |
| `hierarchyPath`           | string |      Yes | Path to the captured UI hierarchy file (incl. device orientation). If missing, retry as that might be AX transient issue |
| `logsPath`                | string |      Yes | Path to the file where collected console logs of the application are stored |
| `screenshotPath`          | string |      Yes | Path to the captured full-size screenshot                    |
| `thumbnailScreenshotPath` | string |      Yes | Path to a screenshot file with small resolution              |


## 6. DocumentationSearch

**Name:** `DocumentationSearch`


### Description

Searches Apple Developer Documentation using semantic matching.

### Input Schema

**Type:** `object`

**Required input fields:**

- `query`

| Field        | Type          | Required | Description                                                  |
| ------------ | ------------- | -------: | ------------------------------------------------------------ |
| `frameworks` | array<string> |       No | Framework(s) to search in. Searches all frameworks if not specified. |
| `query`      | string        |      Yes | The search query                                             |

### Output Schema

**Type:** `object`

**Required output fields:**

- `documents`

| Field                  | Type          | Required | Description                                     |
| ---------------------- | ------------- | -------: | ----------------------------------------------- |
| `documents`            | array<object> |      Yes | The documents most relevant to the search query |
| `documents[].contents` | string        |      Yes | The document contents                           |
| `documents[].kind`     | string        |      Yes | The kind of document                            |
| `documents[].score`    | number        |      Yes | The matching score of the document              |
| `documents[].title`    | string        |      Yes | The document title                              |
| `documents[].uri`      | string        |      Yes | The document uri                                |


## 7. Get Build Log

**Name:** `GetBuildLog`


### Description

Gets the log of the current or most recently finished build. You can choose which build log entries to include by specifying the severity of any issues emitted by the build task represented by the entry. You can also filter by message regex pattern or file glob pattern. The response also indicates whether the build is currently in progress.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`

| Field           | Type                             | Required | Description                                                  |
| --------------- | -------------------------------- | -------: | ------------------------------------------------------------ |
| `glob`          | string                           |       No | Glob to filter the returned build log entries. Will match against the 'path' field of any emitted issues, as well as against the location of any build task. |
| `pattern`       | string                           |       No | Regex to filter the returned build log entries. Will match against the 'message' field of any emitted issues, as well as the task description, command line, and console output of any build tasks. |
| `severity`      | `remark` \| `warning` \| `error` |       No | Limit the output of build log entries to those that emitted issues of the specified severity or higher. Valid values in order of decreasing severity are 'error', 'warning', 'remark'. Defaults to 'error'. |
| `tabIdentifier` | string                           |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `buildResult`
- `buildLogEntries`
- `buildIsRunning`
- `truncated`
- `totalFound`
- `fullLogPath`

| Field                                        | Type          | Required | Description                                                  |
| -------------------------------------------- | ------------- | -------: | ------------------------------------------------------------ |
| `buildIsRunning`                             | boolean       |      Yes | Indicates whether the build is still running.                |
| `buildLogEntries`                            | array<object> |      Yes | The build log entries describing build commands that emitted issues |
| `buildLogEntries[].buildTask`                | string        |       No | The build task represented by the log entry                  |
| `buildLogEntries[].emittedIssues`            | array<object> |      Yes | Any issues emitted by the build task represented by the log entry |
| `buildLogEntries[].emittedIssues[].line`     | integer       |      Yes | The line number where the issue was detected, if known       |
| `buildLogEntries[].emittedIssues[].message`  | string        |      Yes | The message describing the issue                             |
| `buildLogEntries[].emittedIssues[].path`     | string        |       No | The file path where the issue was detected, if any           |
| `buildLogEntries[].emittedIssues[].severity` | string        |      Yes | The severity of issue (error, warning, remark)               |
| `buildResult`                                | string        |      Yes | The message indicating the result of the build operation     |
| `fullLogPath`                                | string        |      Yes | Path of the full log in textual format, containing the complete command lines and any output from the build tasks |
| `message`                                    | string        |       No | Optional message with additional information about the search results |
| `totalFound`                                 | integer       |      Yes | Total number of build log entries before truncation          |
| `truncated`                                  | boolean       |      Yes | Whether results were truncated due to exceeding 100 issues   |


## 8. GetConsoleOutput

**Name:** `GetConsoleOutput`


### Description

Retrieves console output (stdout, stderr, OSLog) from a running or completed app launch session. Supports regex filtering, severity filtering, and context extraction. Use this to analyze app logs, debug issues, find errors, or understand app behavior.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`

| Field                    | Type                        | Required | Description                                                  |
| ------------------------ | --------------------------- | -------: | ------------------------------------------------------------ |
| `contextLines`           | integer                     |       No | Number of context lines to include around pattern matches (like grep -C). Use this with 'pattern' to see surrounding context. Default: 0 |
| `includeMetadata`        | boolean                     |       No | Include detailed OSLog metadata (subsystem, category, pid, tid, sender info). Only applies to OSLog items. Default: false |
| `launchSessionReference` | string                      |       No | Optional launch session reference. Omit to use the current/most recent launch session. |
| `oslogSeverity`          | array<string>               |       No | Filter OSLog by severity levels. Array of: 'error', 'fault', 'info', 'debug', 'default'. Only filters OSLog items, not stdout/stderr. Omit to include all severities. |
| `outputType`             | `stdio` \| `oslog` \| `all` |       No | Type of output to retrieve: 'stdio' (stdout/stderr only), 'oslog' (OSLog only), or 'all' (both). Default: 'all' |
| `pattern`                | string                      |       No | Optional regex pattern to filter console output. Uses full regex syntax (e.g., 'error.*failed', 'Exception.*\\n.*at line'). Filters both stdout/stderr text and OSLog message content. |
| `tabIdentifier`          | string                      |      Yes | The workspace tab identifier                                 |
| `tailLimit`              | integer                     |       No | Maximum number of lines to return from the END of output (tail behavior). Returns the last N lines. Default: 500. |

### Output Schema

**Type:** `object`

**Required output fields:**

- `units`
- `totalCount`
- `truncated`
- `launchSessionInfo`

| Field                                 | Type          | Required | Description                                                  |
| ------------------------------------- | ------------- | -------: | ------------------------------------------------------------ |
| `launchSessionInfo`                   | string        |      Yes | Information about the launch session: app name, reference, PID, and state (running/expired/etc). Useful for verifying which session was queried. |
| `totalCount`                          | integer       |      Yes | Total line count before limits applied. Use this to understand how much output was available. |
| `truncated`                           | boolean       |      Yes | True if results were truncated due to tailLimit.             |
| `units`                               | array<object> |      Yes | Array of console output units. Each unit is a message/log entry with content, timestamp, kind (stdio/oslog), and optional severity. |
| `units[].content`                     | string        |      Yes | The console output text. For stdio: raw stdout/stderr text. For OSLog: the log message (without metadata unless includeMetadata=true, then this is the parsed message). |
| `units[].kind`                        | string        |      Yes | The type of console output: 'stdio' for stdout/stderr, 'oslog' for OSLog system logs |
| `units[].metadata`                    | object        |       No | Detailed OSLog metadata when includeMetadata=true. Includes: subsystem, category, pid, tid, process name, sender function/file/line, and more. Only present for kind='oslog' and when requested. |
| `units[].metadata.category`           | string        |       No | OSLog category within the subsystem (e.g., 'networking', 'database'). Used for finer-grained filtering. |
| `units[].metadata.format`             | string        |       No | OSLog format string with placeholders (e.g., 'Request failed: %{public}@') |
| `units[].metadata.library`            | string        |       No | Library/framework that logged this message                   |
| `units[].metadata.messageType`        | string        |       No | OSLog severity: 'default', 'info', 'debug', 'error', or 'fault' |
| `units[].metadata.pid`                | integer       |      Yes | Process ID that generated this log                           |
| `units[].metadata.processName`        | string        |       No | Name of the process that generated this log                  |
| `units[].metadata.senderFunctionName` | string        |       No | Function/method name that called the logging API (e.g., '-[UIViewController viewDidLoad]'). Useful for code location. |
| `units[].metadata.senderImagePath`    | string        |       No | Path to the executable/library that sent this log (e.g., '/System/Library/Frameworks/UIKit.framework/UIKit') |
| `units[].metadata.senderImageUUID`    | string        |       No | UUID of the sender's binary image. Used for symbolication and crash analysis. |
| `units[].metadata.senderSourceLine`   | integer       |       No | Line number in source file where log was called (if available) |
| `units[].metadata.senderSourcePath`   | string        |       No | Source file path where log was called (if available)         |
| `units[].metadata.sessionUUID`        | string        |       No | Unique identifier for the logging session                    |
| `units[].metadata.subsystem`          | string        |       No | OSLog subsystem identifier (e.g., 'com.apple.UIKit'). Used for grouping related logs. |
| `units[].metadata.tid`                | integer       |      Yes | Thread ID that generated this log. Useful for identifying multi-threading issues. |
| `units[].metadata.timestamp`          | string        |       No | Formatted timestamp string from OSLog                        |
| `units[].severity`                    | string        |       No | OSLog severity level: 'error', 'fault', 'info', 'debug', or 'default'. Only present for kind='oslog'. Errors and faults indicate problems. |
| `units[].timestamp`                   | number        |      Yes | Unix timestamp (seconds since epoch) of when this message appeared. Use this for chronological ordering and time-based analysis. |


## 9. Get Crash Issue Logs

**Name:** `GetCrashIssueLogs`


### Description

    Get detailed crash logs, expert triage knowledge, and actionable recommendations for a specific crash signature.
    Use this after GetTopCrashIssues to drill down into specific crash issues.
    Returns raw crash log text along with domain-specific analysis guidance from expert triage documents.
    If no `bundle_id` is provided, automatically resolves from the active scheme's target. `bundle_id` is case sensitive and capitalization matters.
    If no `platform` is provided, automatically resolves from the app's supported platforms and the active run destination. Supported platform values: 'iOS', 'macOS', 'watchOS', 'tvOS', 'visionOS'.
    Use `is_beta` to specify TestFlight (true) or App Store (false) data. If not provided, fetches data from both release channels.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `signature_name`

| Field            | Type    | Required | Description                                                  |
| ---------------- | ------- | -------: | ------------------------------------------------------------ |
| `app_version`    | string  |       No | The app version to filter crash logs by (e.g., 4.6). If not provided, returns logs across all versions. |
| `bundle_id`      | string  |       No | The bundle identifier of the app (e.g., com.apple.Playgrounds). If not provided, automatically resolved from the active scheme's target. |
| `is_beta`        | boolean |       No | Whether to fetch TestFlight (true) or App Store (false) data. If not provided, fetches data from both release channels. |
| `platform`       | string  |       No | The platform to query (e.g., 'iOS', 'macOS', 'watchOS', 'tvOS', 'visionOS'). If not provided, automatically resolved from the active run destination. |
| `signature_name` | string  |      Yes | The human-readable crash signature name from GetTopCrashIssues |
| `tabIdentifier`  | string  |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `success`
- `data`
- `message`
- `bundleId`
- `signatureName`

| Field           | Type    | Required | Description                                            |
| --------------- | ------- | -------: | ------------------------------------------------------ |
| `appVersion`    | string  |       No | The app version that was queried (nil if all versions) |
| `bundleId`      | string  |      Yes | The bundle ID that was queried                         |
| `data`          | string  |      Yes | The crash logs and expert triage knowledge             |
| `message`       | string  |      Yes | Status message about the operation                     |
| `signatureName` | string  |      Yes | The crash signature name that was queried              |
| `success`       | boolean |      Yes | Whether the API request succeeded                      |


## 10. Get Field Performance Issue Logs

**Name:** `GetFieldPerformanceIssueLogs`


### Description

Get detailed logs, performance data, expert triage knowledge, and actionable recommendations for specific field performance issue.
Use this after GetTopFieldPerformanceIssues to drill down into specific performance issues.
This tool provides detailed performance diagnostics including stack traces, timeline data, and domain-specific analysis guidance from expert triage documents.
Supported diagnostic types: `launches` (app launch performance), `hangs` (app hang issues), `diskwrites` (disk write performance), `energy` (CPU/energy usage).
If no `bundle_id` is provided, automatically resolves from the active scheme's target. `bundle_id` is case sensitive and capitalization matters.
If no `platform` is provided, automatically resolves from the app's supported platforms and the active run destination. Supported platform values depend on the diagnostic type (e.g., 'iOS', 'macOS', 'watchOS', 'tvOS', 'visionOS').
Some diagnostic types (e.g., energy) support both App Store and TestFlight data. Use `is_beta` to specify which release channel to query. If not provided, auto-detected from the app version.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `app_version`
- `signature_name`
- `diagnostic_type`

| Field             | Type    | Required | Description                                                  |
| ----------------- | ------- | -------: | ------------------------------------------------------------ |
| `app_version`     | string  |      Yes | The app version (e.g., 13.14.0)                              |
| `bundle_id`       | string  |       No | The bundle identifier of the app (e.g., com.toyopagroup.picaboo). If not provided, automatically resolved from the active scheme's target. |
| `diagnostic_type` | string  |      Yes | The type of performance diagnostic to retrieve. Supported values: 'launches', 'hangs', 'diskwrites', 'energy' |
| `is_beta`         | boolean |       No | Whether to fetch TestFlight (true) or App Store (false) data. If not provided, auto-detected from the app version. |
| `platform`        | string  |       No | The platform to query (e.g., 'iOS', 'macOS', 'watchOS', 'tvOS', 'visionOS'). Supported values depend on the diagnostic type. If not provided, automatically resolved from the active run destination. |
| `signature_name`  | string  |      Yes | The human-readable signature name from GetTopFieldPerformanceIssues |
| `tabIdentifier`   | string  |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `success`
- `data`
- `message`
- `bundleId`
- `appVersion`
- `signatureName`
- `diagnosticType`

| Field            | Type    | Required | Description                                           |
| ---------------- | ------- | -------: | ----------------------------------------------------- |
| `appVersion`     | string  |      Yes | The app version that was queried                      |
| `bundleId`       | string  |      Yes | The bundle ID that was queried                        |
| `data`           | string  |      Yes | The formatted JSON response from the field report API |
| `diagnosticType` | string  |      Yes | The diagnostic type that was queried                  |
| `message`        | string  |      Yes | Status message about the operation                    |
| `signatureName`  | string  |      Yes | The signature name that was queried                   |
| `success`        | boolean |      Yes | Whether the API request succeeded                     |


## 11. GetFileCompilerFlags

**Name:** `GetFileCompilerFlags`


### Description

Gets the additional per-file compiler flags for a single source file in a specific Xcode target — the same value shown in the Compiler Flags column of Target > Build Phases > Compile Sources. Returns an empty string when the file is a member of the target with no flags set. Use UpdateFileCompilerFlags to modify flags.

Note: Per-file compiler flags are uncommon — most targets configure compilation through target build settings instead. Use this tool to inspect existing per-file flags (e.g. when checking why one file in a target compiles differently from its peers, such as adopting `-fbounds-safety` for a single file).

Note about Swift: Values shown for `.swift` files may not actually affect builds because the unit of compilation for Swift is a module. Prefer OTHER_SWIFT_FLAGS at the target level.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `targetName`
- `filePath`

| Field           | Type   | Required | Description                                                  |
| --------------- | ------ | -------: | ------------------------------------------------------------ |
| `filePath`      | string |      Yes | The path to the source file within the Xcode project organization (e.g., 'ProjectName/Sources/MyFile.swift'). An absolute or workspace-relative filesystem path is also accepted as a fallback. |
| `projectPath`   | string |       No | The project-organization path of the .xcodeproj that owns the target (e.g., 'MyApp/MyApp.xcodeproj'). Only needed when the same target name exists in multiple projects in the workspace; omit otherwise. |
| `tabIdentifier` | string |      Yes | The workspace tab identifier.                                |
| `targetName`    | string |      Yes | The name of the Xcode target whose build phase contains the source file. |

### Output Schema

**Type:** `object`

**Required output fields:**

- `targetName`
- `filePath`
- `compilerFlags`

| Field           | Type   | Required | Description                                                  |
| --------------- | ------ | -------: | ------------------------------------------------------------ |
| `compilerFlags` | string |      Yes | The additional compiler flags on the file, as a single space-separated string. Empty when the file is a member of the target with no per-file flags set. |
| `filePath`      | string |      Yes | The project-organization path of the file.                   |
| `guidance`      | string |       No | Populated when a filesystem path was supplied and resolved to a project-organization path. Contains instructions for the agent to prefer the resolved path in subsequent calls. |
| `targetName`    | string |      Yes | The target whose per-file compiler flags were returned.      |
| `warning`       | string |       No | Populated when the build system is unlikely to honor per-file flags on this file (e.g. Swift sources, where the unit of compilation is a module). The flags returned are still the values stored in the project, but the agent should not assume they influence the build; consider a target-level setting instead. |


## 12. Get Test List

**Name:** `GetTestList`


### Description

Gets all available tests from the active scheme's active test plan. Results are limited to 100 tests. The complete list is written to fullTestListPath in grep-friendly format. Use grep with keys like TEST_TARGET, TEST_IDENTIFIER, or TEST_FILE_PATH to find specific tests.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`

| Field           | Type   | Required | Description                  |
| --------------- | ------ | -------: | ---------------------------- |
| `tabIdentifier` | string |      Yes | The workspace tab identifier |

### Output Schema

**Type:** `object`

**Required output fields:**

- `tests`
- `schemeName`
- `summary`
- `counts`
- `truncated`
- `totalTests`
- `fullTestListPath`

| Field                 | Type          | Required | Description                                  |
| --------------------- | ------------- | -------: | -------------------------------------------- |
| `activeTestPlanName`  | string        |       No | The active test plan name                    |
| `counts`              | object        |      Yes | Aggregated test counts                       |
| `counts.disabled`     | integer       |      Yes | Number of disabled tests                     |
| `counts.enabled`      | integer       |      Yes | Number of enabled tests                      |
| `counts.total`        | integer       |      Yes | Total test count                             |
| `fullTestListPath`    | string        |      Yes | Path to file containing complete test list   |
| `schemeName`          | string        |      Yes | The active scheme name                       |
| `summary`             | string        |      Yes | Human-readable test list summary             |
| `tests`               | array<object> |      Yes | List of tests limited to 100                 |
| `tests[].displayName` | string        |      Yes | Human readable test name                     |
| `tests[].filePath`    | string        |       No | Source file path                             |
| `tests[].identifier`  | string        |      Yes | Test identifier in XCTestIdentifier format   |
| `tests[].isEnabled`   | boolean       |      Yes | Whether the test is enabled in the test plan |
| `tests[].lineNumber`  | integer       |       No | Line number in source file                   |
| `tests[].tags`        | array<string> |       No | Swift Testing tags                           |
| `tests[].targetName`  | string        |      Yes | The test target name                         |
| `totalTests`          | integer       |      Yes | Total number of tests before truncation      |
| `truncated`           | boolean       |      Yes | Whether test list was truncated              |


## 13. Get Top Crash Issues

**Name:** `GetTopCrashIssues`


### Description

    Retrieve the top crash signatures for an app from Apple's crash reporting service.
    Returns crash signatures sorted by device count (number of unique devices affected) for the last 14 days.
    Use this tool to identify and prioritize the most impactful crashes in your app.
    If no `bundle_id` is provided, automatically resolves from the active scheme's target. `bundle_id` is case sensitive and capitalization matters.
    If no `platform` is provided, automatically resolves from the app's supported platforms and the active run destination. Supported platform values: 'iOS', 'macOS', 'watchOS', 'tvOS', 'visionOS'.
    If no `app_version` is provided, returns crashes across all versions. If provided, filters to that specific version.
    Use `is_beta` to specify TestFlight (true) or App Store (false) data. If not provided, returns data from both release channels.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`

| Field           | Type    | Required | Description                                                  |
| --------------- | ------- | -------: | ------------------------------------------------------------ |
| `app_version`   | string  |       No | The app version to filter by (e.g., 4.6). If not provided, returns crashes across all versions for the last 14 days. |
| `bundle_id`     | string  |       No | The bundle identifier of the app (e.g., com.apple.Playgrounds). If not provided, automatically resolved from the active scheme's target. |
| `count`         | integer |       No | Number of top crash signatures to return. Use 1 when the user asks for their 'top crash' (singular). Defaults to 5 when not specified or when the user asks for 'top crashes' (plural). |
| `is_beta`       | boolean |       No | Whether to fetch TestFlight (true) or App Store (false) data. If not provided, returns data from both release channels. |
| `platform`      | string  |       No | The platform to query (e.g., 'iOS', 'macOS', 'watchOS', 'tvOS', 'visionOS'). If not provided, automatically resolved from the active run destination. |
| `tabIdentifier` | string  |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `success`
- `data`
- `message`
- `bundleId`

| Field        | Type    | Required | Description                                            |
| ------------ | ------- | -------: | ------------------------------------------------------ |
| `appVersion` | string  |       No | The app version that was queried (nil if all versions) |
| `bundleId`   | string  |      Yes | The bundle ID that was queried                         |
| `data`       | string  |      Yes | The formatted JSON response with top crash signatures  |
| `message`    | string  |      Yes | Status message about the operation                     |
| `success`    | boolean |      Yes | Whether the API request succeeded                      |


## 14. Get Top Field Performance Issues

**Name:** `GetTopFieldPerformanceIssues`


### Description

    Analyze app performance and identify performance regressions across different diagnostic types.
    Use this tool to investigate or analyze performance issues by querying Apple's field report APIs.
    Supported diagnostic types: `launches` (app launch performance), `hangs` (app hang issues), `diskwrites` (disk write performance), `energy` (CPU/energy usage).
    If no `bundle_id` is provided, automatically resolves from the active scheme's target. `bundle_id` is case sensitive and capitalization matters.
    If no `platform` is provided, automatically resolves from the app's supported platforms and the active run destination. Supported platform values depend on the diagnostic type (e.g., 'iOS', 'macOS', 'watchOS', 'tvOS', 'visionOS').
    Some diagnostic types (e.g., energy) support both App Store and TestFlight data. Use `is_beta` to specify which release channel to query. If not provided, auto-detected from the app version when possible.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `diagnostic_type`

| Field             | Type    | Required | Description                                                  |
| ----------------- | ------- | -------: | ------------------------------------------------------------ |
| `app_version`     | string  |       No | The app version (e.g., 4.6). If not provided, returns a list of available versions to choose from. |
| `bundle_id`       | string  |       No | The bundle identifier of the app (e.g., com.apple.Playgrounds). If not provided, automatically resolved from the active scheme's target. |
| `diagnostic_type` | string  |      Yes | The type of performance diagnostic to retrieve. Supported values: 'launches', 'hangs', 'diskwrites', 'energy' |
| `is_beta`         | boolean |       No | Whether to fetch TestFlight (true) or App Store (false) data. If not provided, auto-detected from the app version when possible. For diagnostic types that support both (e.g., energy), you will be asked to choose if it cannot be determined. |
| `platform`        | string  |       No | The platform to query (e.g., 'iOS', 'macOS', 'watchOS', 'tvOS', 'visionOS'). Supported values depend on the diagnostic type. If not provided, automatically resolved from the active run destination. |
| `tabIdentifier`   | string  |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `success`
- `data`
- `message`
- `bundleId`
- `diagnosticType`

| Field               | Type          | Required | Description                                                  |
| ------------------- | ------------- | -------: | ------------------------------------------------------------ |
| `appVersion`        | string        |       No | The app version that was queried (nil if version selection is needed) |
| `availableVersions` | array<object> |       No | List of available versions when app_version was not provided. Each entry has 'version' and 'build' keys. |
| `bundleId`          | string        |      Yes | The bundle ID that was queried                               |
| `data`              | string        |      Yes | The formatted JSON response from the field report API        |
| `diagnosticType`    | string        |      Yes | The diagnostic type that was queried                         |
| `message`           | string        |      Yes | Status message about the operation                           |
| `success`           | boolean       |      Yes | Whether the API request succeeded                            |


## 15. Invoke a Debugger Command

**Name:** `InvokeDebuggerCommand`


### Description

Sends an lldb command to Xcode's active debugging session and returns the output.
The process must already be running with the debugger attached. You can use the
RunProject tool with attachDebugger=true, if it isn't running and debugged already.

Send any lldb command: 'bt', 'po variable', 'breakpoint set -n main', 'continue',
'thread step-over', 'frame variable', etc. The command runs in the same lldb session
as Xcode's debug console, so the agent and Xcode UI share the same debugging state.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `command`

| Field           | Type    | Required | Description                                                  |
| --------------- | ------- | -------: | ------------------------------------------------------------ |
| `command`       | string  |      Yes | The lldb command to execute in Xcode's active debug session (e.g., 'bt', 'po self', 'breakpoint set -n viewDidLoad', 'continue', 'thread step-over', 'frame variable'). |
| `tabIdentifier` | string  |      Yes | The workspace tab identifier                                 |
| `timeout`       | integer |       No | Maximum seconds to wait for the command to complete. Default: 30. Increase for commands that resume execution (e.g., 'continue'). |

### Output Schema

**Type:** `object`

**Required output fields:**

- `output`
- `debugSessionActive`
- `isWaitingForMore`

| Field                | Type    | Required | Description                                                  |
| -------------------- | ------- | -------: | ------------------------------------------------------------ |
| `debugSessionActive` | boolean |      Yes | Whether an active debug session exists. False if no app is being debugged. |
| `isWaitingForMore`   | boolean |      Yes | True if the command timed out before completing. The command may still be executing (e.g., after 'continue'). Call again with a simple command like 'thread list' to check the state. |
| `output`             | string  |      Yes | The lldb output from the command. This is the same output that appears in Xcode's debug console. |
| `processIdentifier`  | integer |       No | The process identifier (PID) of the debugged process, if available. |


## 16. Prepare For Localization

**Name:** `LocalizationPlanner`


### Description

Ensures the project is in a state where translations can be added. **Call this tool each time you are tasked with adding a language** to the project, or to translate an entire project. Before calling this tool, you MUST activate the `xcode-integration:translation-coordinator` skill. Do not call this tool without first loading that skill's instructions.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `targetLocaleIdentifier`

| Field                    | Type   | Required | Description                                           |
| ------------------------ | ------ | -------: | ----------------------------------------------------- |
| `tabIdentifier`          | string |      Yes | The workspace tab identifier                          |
| `targetLocaleIdentifier` | string |      Yes | The locale identifier for which you want to translate |

### Output Schema

**Type:** `object`

**Required output fields:**

- `nextStep`

| Field         | Type   | Required | Description                                                  |
| ------------- | ------ | -------: | ------------------------------------------------------------ |
| `changesMade` | string |       No | List of changes successfully made to the project. Includes list of all String Catalogs in the project. |
| `nextStep`    | string |      Yes | Advisory telling you what action to take next. Follow these steps to successfully localize the entire project. |
| `stepsFailed` | string |       No | Description of failed steps that require manual intervention. The user has to execute them manually. If nil, all steps succeeded. |
| `suggestions` | string |       No | Non-blocking suggestions for improving the project's localization setup. The user should consider these recommendations. Don't try to follow them yourself, it needs a user interaction. |


## 17. RenderPreview

**Name:** `RenderPreview`


### Description

Builds and renders a Preview and waits until a snapshot of the resulting UI is available.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `sourceFilePath`

| Field                                         | Type    | Required | Description                                                  |
| --------------------------------------------- | ------- | -------: | ------------------------------------------------------------ |
| `previewCanvasControlOverrides`               | object  |       No | Optional overrides for the canvas controls, only applicable to preview types that support each kind of canvas control. Use the `supportedCanvasControlOverrides` field of the output of a previous invocation of this tool to get the available canvas control overrides and their supported values. |
| `previewCanvasControlOverrides.timelineIndex` | integer |       No | A zero-based timestop index to render for previews that vary across a timeline, such as a Widget or a Live Activity. This index represents a sequence number rather than a specific clock time. This parameter is only meaningful for previews that return a list of indexes in the `timelineIndexes` field of `supportedCanvasControlOverrides` of the same invocation of this tool for the same active scheme and run destination. If no index is provided, the first preview in the timeline is rendered. This parameter is ignored for previews that don't have a timeline. |
| `previewCanvasControlOverrides.toggleState`   | boolean |       No | The boolean state of a toggle control for previews that have one, such as a Live Activity. This parameter is only meaningful for previews that return a toggle state in the `toggleState` field of `supportedCanvasControlOverrides` of the same invocation of this tool for the same active scheme and run destination. If no state is provided, the first state in the toggle state list is rendered. This parameter is ignored for previews that don't have a toggle control. |
| `previewDefinitionIndexInFile`                | integer |       No | The zero based index of the #Preview macro or PreviewProvider struct definition in the source file counting from the top. Defaults to 0, i.e. the first one. |
| `previewLocalizationOverride`                 | string  |       No | A locale identifier to preview in (e.g. "fr", "ja"). Use a value that was returned in the `supportedLocalizations` field by a previous invocation of this tool. |
| `previewVariantOverrides`                     | object  |       No | A dictionary mapping variant group names to variant names for any preview variants that should be overridden. Use keys and values that were returned in the `supportedPreviewVariantOverrides` field by a previous invocation of this tool for the same active scheme and run destination. |
| `sourceFilePath`                              | string  |      Yes | The path to the file within the Xcode project organization (e.g., 'ProjectName/Sources/MyFile.swift') |
| `tabIdentifier`                               | string  |      Yes | The workspace tab identifier                                 |
| `timeout`                                     | integer |       No | The time in seconds to wait for the rendering of the preview to complete. Defaults to 120 seconds. |

### Output Schema

**Type:** `object`

**Required output fields:**

_None._

| Field                                             | Type           | Required | Description                                                  |
| ------------------------------------------------- | -------------- | -------: | ------------------------------------------------------------ |
| `errors`                                          | array<object>  |       No | Any errors that occurred during the preview attempt (such as input validation failures). |
| `errors[].message`                                | string         |      Yes | The error message with potential underlying errors included. |
| `previewSnapshotPath`                             | string         |       No | The path to the image snapshot of the requested preview.     |
| `renderedDestination`                             | object         |       No | Describes the run destination that the preview was actually rendered against (e.g. which simulator). May differ from the workspace's currently-selected run destination if the pipeline picked a different one for this preview. Omitted if the destination could not be determined. |
| `renderedDestination.deviceModelName`             | string         |       No | Display name of the device model that the preview was rendered for (e.g. “iPhone 15 Pro”, “iPad Pro (11-inch) (2nd generation)”, “My Mac”). Omitted if not known. |
| `renderedDestination.platformName`                | string         |       No | Display name of the platform that the preview was rendered for (e.g. “iOS Simulator”, “macOS”). Omitted if not known. |
| `renderedDestination.systemVersion`               | string         |       No | Operating system version that the preview was rendered for, in major.minor[.patch] form (e.g. “17.2”). Omitted if not known. |
| `supportedCanvasControlOverrides`                 | object         |       No | The supported canvas control overrides for this preview, which can be passed to the tool in the `previewCanvasControlOverrides` parameter in subsequent tool invocations. Contains `timelineIndexes` for previews that vary across a timeline (such as Widgets or Live Activities) and `toggleStates` for previews that have a toggle control. Omitted if the preview doesn't support any canvas control overrides. |
| `supportedCanvasControlOverrides.timelineIndexes` | array<integer> |       No | List of supported timeline indexes for previews that vary across a timeline, such as Widgets or Live Activities. The indexes represents sequence numbers rather than specific times. Omitted if the preview doesn't have a timeline control. |
| `supportedCanvasControlOverrides.toggleStates`    | array<boolean> |       No | List of supported toggle states, for previews that have toggle control. Omitted if the preview doesn't have a toggle control. |
| `supportedLocalizations`                          | array<string>  |       No | The locale identifiers supported by the workspace (e.g. ["en", "fr", "ja"]), which can be passed to the tool in the `previewLocalizationOverride` parameter in subsequent tool invocations to preview in a specific language. Do not call this tool in parallel with other invocations when this parameter is provided. |
| `supportedPreviewVariantOverrides`                | object         |       No | The supported preview variant overrides, which can be passed to the tool in the `previewVariantOverrides` parameter in subsequent tool invocations. The keys are the names of variant groups that can be overridden, and the values are the names of the variants that are supported for that group. |


## 18. Run All Tests

**Name:** `RunAllTests`


### Description

Runs all tests from the active scheme's active test plan

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`

| Field           | Type   | Required | Description                  |
| --------------- | ------ | -------: | ---------------------------- |
| `tabIdentifier` | string |      Yes | The workspace tab identifier |

### Output Schema

**Type:** `object`

**Required output fields:**

- `summary`
- `counts`
- `results`
- `schemeName`
- `truncated`
- `totalResults`
- `fullSummaryPath`

| Field                     | Type          | Required | Description                                                  |
| ------------------------- | ------------- | -------: | ------------------------------------------------------------ |
| `activeTestPlanName`      | string        |       No | Active test plan name                                        |
| `counts`                  | object        |      Yes | Test execution counts                                        |
| `counts.expectedFailures` | integer       |      Yes | Number of expected failures                                  |
| `counts.failed`           | integer       |      Yes | Number of failed tests                                       |
| `counts.notRun`           | integer       |      Yes | Number of tests not run                                      |
| `counts.passed`           | integer       |      Yes | Number of passed tests                                       |
| `counts.skipped`          | integer       |      Yes | Number of skipped tests                                      |
| `counts.total`            | integer       |      Yes | Total test count                                             |
| `fullConsoleLogsPath`     | string        |       No | A text file path containing all logs that would be printed in the console from `print`, `NSLog`, etc. during test build and execution. |
| `fullSummaryPath`         | string        |      Yes | A text file path containing all test results and complete issue details in textual form |
| `message`                 | string        |       No | Additional information about truncation                      |
| `results`                 | array<object> |      Yes | Results for each test (truncated at 100, failures shown first) |
| `results[].displayName`   | string        |      Yes | Human-readable test name                                     |
| `results[].errorMessages` | array<string> |      Yes | Error messages                                               |
| `results[].identifier`    | string        |      Yes | Test identifier in XCTestIdentifier format                   |
| `results[].state`         | string        |      Yes | Execution state                                              |
| `results[].targetName`    | string        |      Yes | The test target name                                         |
| `schemeName`              | string        |      Yes | Active scheme name                                           |
| `summary`                 | string        |      Yes | Test execution summary                                       |
| `totalResults`            | integer       |      Yes | Total number of test results before truncation               |
| `truncated`               | boolean       |      Yes | Whether results were truncated due to exceeding 100 results  |
| `xcresultBundlePath`      | string        |       No | Path to the .xcresult bundle produced by the test run. Can be parsed with xcresulttool. If code coverage is enabled, xccov can extract coverage data from this bundle. |


## 19. RunCodeSnippet

**Name:** `RunCodeSnippet`


### Description

Builds and runs a snippet of code in the context of a specific file and waits until results are available. This tool is available for source files in targets that build applications, frameworks, libraries, or command line executables. The output consists of the console output generated by the `print` statements contained in the provided snippet.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `codeSnippet`
- `sourceFilePath`
- `purpose`

| Field            | Type    | Required | Description                                                  |
| ---------------- | ------- | -------: | ------------------------------------------------------------ |
| `codeSnippet`    | string  |      Yes | The code snippet that should be run within the context of the specified Swift file. |
| `purpose`        | string  |      Yes | A short human-readable description of the purpose of running this code snippet. Do NOT use the word 'test' in this string, because that is misleading and could make the user think that this functionality is related to testing. |
| `sourceFilePath` | string  |      Yes | The path to a Swift source file within the Xcode project organization (e.g., 'ProjectName/Sources/MyFile.swift') whose context the code snippet will have access to (including `fileprivate` declarations). |
| `tabIdentifier`  | string  |      Yes | The workspace tab identifier.                                |
| `timeout`        | integer |       No | The time in seconds to wait for the running of the snippet to complete. Defaults to 600 seconds. |

### Output Schema

**Type:** `object`

**Required output fields:**

_None._

| Field                  | Type   | Required | Description                                                  |
| ---------------------- | ------ | -------: | ------------------------------------------------------------ |
| `error`                | object |       No | Any error that occurred while trying to run the snippet, if applicable. This could be a problem compiling the snippet or it could be a runtime error. |
| `error.detailsPath`    | string |       No | The path of an optional file that contains all the details of the error. |
| `error.message`        | string |      Yes | A very short message describing the error.                   |
| `error.recoveryAdvice` | string |       No | An optional message providing advice on how to resolve the error. |
| `error.summary`        | string |      Yes | A longer summary of the error and (if known) its cause.      |
| `executionResults`     | string |       No | The console output generated by the `print` statements contained in the provided snippet. |


## 20. Run Project

**Name:** `RunProject`


### Description

Builds and runs the current scheme in Xcode, equivalent to pressing the Run button (Cmd+R).
Returns when the app has launched and is running.
Use the InvokeDebuggerCommand tool to debug the app (make sure to run with attachDebugger=true).
Use the GetConsoleOutput tool to read the app's console output.
Use the StopProject tool to stop the app, when finished.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`

| Field            | Type    | Required | Description                                                  |
| ---------------- | ------- | -------: | ------------------------------------------------------------ |
| `attachDebugger` | boolean |       No | Whether to attach the debugger to the launched process. Defaults to false. |
| `tabIdentifier`  | string  |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `runResult`
- `buildErrors`
- `fullLogPath`

| Field                          | Type          | Required | Description                                                  |
| ------------------------------ | ------------- | -------: | ------------------------------------------------------------ |
| `buildErrors`                  | array<object> |      Yes | List of build errors, if the build failed                    |
| `buildErrors[].classification` | string        |      Yes | The type of error (error, warning, etc.)                     |
| `buildErrors[].filePath`       | string        |       No | The file path where the error occurred                       |
| `buildErrors[].lineNumber`     | integer       |       No | The line number where the error occurred                     |
| `buildErrors[].message`        | string        |      Yes | The error message                                            |
| `elapsedTime`                  | number        |       No | The time it took to build and launch, in seconds             |
| `fullLogPath`                  | string        |      Yes | Path of the full build log in textual format                 |
| `launchSessionReference`       | string        |       No | A reference to the launch session that can be passed to GetConsoleOutput to read the app's output |
| `processIdentifier`            | integer       |       No | The process identifier (PID) of the launched app             |
| `runResult`                    | string        |      Yes | The message indicating the result of the run operation       |


## 21. Run Some Tests

**Name:** `RunSomeTests`


### Description

Runs specific tests using the active scheme's active test plan

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `tests`

| Field                    | Type          | Required | Description                                                  |
| ------------------------ | ------------- | -------: | ------------------------------------------------------------ |
| `tabIdentifier`          | string        |      Yes | The workspace tab identifier                                 |
| `tests`                  | array<object> |      Yes | Array of test specifiers to run. Each specifier contains 'targetName' and 'testIdentifier' fields. Use GetTestList action to discover available tests and their identifiers, then extract the 'targetName' and 'identifier' fields from the TestActionInfo results to construct TestActionSpecifier objects. |
| `tests[].targetName`     | string        |      Yes | The test target name                                         |
| `tests[].testIdentifier` | string        |      Yes | Test identifier in XCTestIdentifier format                   |

### Output Schema

**Type:** `object`

**Required output fields:**

- `summary`
- `counts`
- `results`
- `schemeName`
- `truncated`
- `totalResults`
- `fullSummaryPath`

| Field                     | Type          | Required | Description                                                  |
| ------------------------- | ------------- | -------: | ------------------------------------------------------------ |
| `activeTestPlanName`      | string        |       No | Active test plan name                                        |
| `counts`                  | object        |      Yes | Test execution counts                                        |
| `counts.expectedFailures` | integer       |      Yes | Number of expected failures                                  |
| `counts.failed`           | integer       |      Yes | Number of failed tests                                       |
| `counts.notRun`           | integer       |      Yes | Number of tests not run                                      |
| `counts.passed`           | integer       |      Yes | Number of passed tests                                       |
| `counts.skipped`          | integer       |      Yes | Number of skipped tests                                      |
| `counts.total`            | integer       |      Yes | Total test count                                             |
| `fullConsoleLogsPath`     | string        |       No | A text file path containing all logs that would be printed in the console from `print`, `NSLog`, etc. during test build and execution. |
| `fullSummaryPath`         | string        |      Yes | A text file path containing all test results and complete issue details in textual form |
| `message`                 | string        |       No | Additional information about truncation                      |
| `results`                 | array<object> |      Yes | Results for each test (truncated at 100, failures shown first) |
| `results[].displayName`   | string        |      Yes | Human-readable test name                                     |
| `results[].errorMessages` | array<string> |      Yes | Error messages                                               |
| `results[].identifier`    | string        |      Yes | Test identifier in XCTestIdentifier format                   |
| `results[].state`         | string        |      Yes | Execution state                                              |
| `results[].targetName`    | string        |      Yes | The test target name                                         |
| `schemeName`              | string        |      Yes | Active scheme name                                           |
| `summary`                 | string        |      Yes | Test execution summary                                       |
| `totalResults`            | integer       |      Yes | Total number of test results before truncation               |
| `truncated`               | boolean       |      Yes | Whether results were truncated due to exceeding 100 results  |
| `xcresultBundlePath`      | string        |       No | Path to the .xcresult bundle produced by the test run. Can be parsed with xcresulttool. If code coverage is enabled, xccov can extract coverage data from this bundle. |


## 22. Stop Project

**Name:** `StopProject`


### Description

Stops the currently running app in Xcode, equivalent to pressing the Stop button (Cmd+.).
If no app is currently running, reports that there is nothing to stop.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`

| Field           | Type   | Required | Description                  |
| --------------- | ------ | -------: | ---------------------------- |
| `tabIdentifier` | string |      Yes | The workspace tab identifier |

### Output Schema

**Type:** `object`

**Required output fields:**

- `stopResult`

| Field               | Type    | Required | Description                                              |
| ------------------- | ------- | -------: | -------------------------------------------------------- |
| `processIdentifier` | integer |       No | The process identifier (PID) of the app that was stopped |
| `stopResult`        | string  |      Yes | The result of the stop operation                         |


## 23. Get String Context

**Name:** `StringCatalogContext`


### Description

Returns context and the source language value for a given string in the String Catalog. The sourceValues field contains the text that must be translated. Before calling this tool, you MUST activate the `xcode-integration:translation` skill. Do not call this tool without first loading that skill's instructions.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `filePath`
- `stringKey`
- `targetLocaleIdentifier`

| Field                    | Type   | Required | Description                                           |
| ------------------------ | ------ | -------: | ----------------------------------------------------- |
| `filePath`               | string |      Yes | The path to the String Catalog                        |
| `stringKey`              | string |      Yes | String key for which to get context for               |
| `tabIdentifier`          | string |      Yes | The workspace tab identifier                          |
| `targetLocaleIdentifier` | string |      Yes | The locale identifier for which you want to translate |

### Output Schema

**Type:** `object`

**Required output fields:**

- `sourceValues`
- `shouldTranslate`
- `translations`
- `usageLocations`
- `similarStrings`
- `nextSteps`

| Field                                 | Type          | Required | Description                                                  |
| ------------------------------------- | ------------- | -------: | ------------------------------------------------------------ |
| `appearances`                         | array<object> |       No | Hints about how this string is displayed in the UI. Includes element type (button, label) and framework (SwiftUI, UIKit, AppKit). Null if appearance data unavailable. |
| `comment`                             | string        |       No | Comment from the String Catalog, if present                  |
| `isStringSet`                         | boolean       |       No | Whether the string is a string set (array of values). Only set for string sets. |
| `nextSteps`                           | string        |      Yes | Suggestions of what tool to use next                         |
| `relevantPluralCases`                 | array<string> |       No | If the string contains numerical format specifiers, this lists the relevant plural cases for the target locale (e.g., ['zero', 'one', 'other']). Null when this string doesn't require any pluralization. |
| `shouldTranslate`                     | boolean       |      Yes | Whether this string should be translated (false means 'DO NOT TRANSLATE') |
| `similarStrings`                      | array<object> |      Yes | Similar strings found in other String Catalogs in the workspace, useful for understanding translation patterns. Keep your translation similar to these strings and learn terminology and formality used in this project. |
| `sourcePluralCasesToAdd`              | array<string> |       No | Plural cases for the SOURCE locale that you should add BEFORE translating the target. Null when the source is already plural-varied or when no pluralization is needed. |
| `sourceValues`                        | object        |      Yes | The source language values that you MUST translate into the target locale. Contains the text to translate — do NOT translate the key. |
| `sourceValues.setValues`              | array<string> |       No | For string sets: the source language values. Each one must be translated. Only set for string sets |
| `sourceValues.sourceLocaleIdentifier` | string        |      Yes | The source locale identifier.                                |
| `sourceValues.value`                  | string        |       No | The source language value that MUST be translated. This is the text to translate, NOT the key |
| `sourceValues.variationDescription`   | string        |       No | For varied strings: human-readable description of the source variation tree showing all variants that need translation |
| `supportedDevices`                    | array<string> |       No | The devices this String Catalog builds for (e.g., device.iphone, device.mac). Varying a string by device is optional and rarely needed. Only consider adding device variations when the string contains a reference to tapping or clicking, or when it mentions a specific device name like "on your iPhone". Most strings do not need device variations. |
| `translations`                        | array<object> |      Yes | Existing translations for this string in locales related to the target locale |
| `usageDataUnavailable`                | string        |       No | Informational message when usage data cannot be retrieved. Contains guidance on how to proceed (e.g., build project). Null when usage data is successfully retrieved. |
| `usageLocations`                      | array<object> |      Yes | Locations in source code where this string is used, including file paths, line numbers, and column numbers. Use this information to __always__ check the code at these locations in the project to learn more about HOW the string is used in the codebase. This is a vital clue for a better understanding of the string's meaning. |


## 24. Insert Translation

**Name:** `StringCatalogEdit`


### Description

Inserts a translation for a given locale into a String Catalog. Before calling this tool, you MUST activate the `xcode-integration:translation` skill. Do not call this tool without first loading that skill's instructions.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `filePath`
- `stringKey`
- `targetLocaleIdentifier`

| Field                                                  | Type          | Required | Description                                                  |
| ------------------------------------------------------ | ------------- | -------: | ------------------------------------------------------------ |
| `filePath`                                             | string        |      Yes | The path to the String Catalog                               |
| `stringKey`                                            | string        |      Yes | String key to translate                                      |
| `stringSetTranslation`                                 | array<string> |       No | Array of translated values for string sets. Prefer typographically correct quotes for the target language (e.g., „...“ for German, «...» for French). All curly quotes must be escaped (e.g., \\u201E...\\u201C for German „...“) |
| `tabIdentifier`                                        | string        |      Yes | The workspace tab identifier                                 |
| `targetLocaleIdentifier`                               | string        |      Yes | Identifier of the locale for which to insert the given translation |
| `templateTranslation`                                  | object        |       No | Translation with template + substitutions for varying a string by plural or for translating a string that is already varied by plural. Prefer typographically correct quotes for the target language (e.g., „...“ for German, «...» for French). All curly quotes must be escaped (e.g., \\u201E...\\u201C for German „...“) |
| `templateTranslation.substitutions`                    | array<object> |      Yes | Array of substitution definitions                            |
| `templateTranslation.substitutions[].argNum`           | integer       |      Yes | Argument number corresponding to the format specifier position in the original string |
| `templateTranslation.substitutions[].formatSpecifier`  | string        |      Yes | Format specifier type without %.                             |
| `templateTranslation.substitutions[].name`             | string        |      Yes | Name of the substitution placeholder                         |
| `templateTranslation.substitutions[].variants`         | object        |      Yes | Dictionary mapping variation paths to translated values. Prefer typographically correct quotes for the target language (e.g., „...“ for German, «...» for French). All curly quotes must be escaped (e.g., \\u201E...\\u201C for German „...“) |
| `templateTranslation.template`                         | string        |      Yes | Template string containing substitution references. Prefer typographically correct quotes for the target language (e.g., „...“ for German, «...» for French). All curly quotes must be escaped (e.g., \\u201E...\\u201C for German „...“) |
| `translation`                                          | string        |       No | Simple string translation for non-varied strings. Prefer typographically correct quotes for the target language (e.g., „...“ for German, «...» for French). All curly quotes must be escaped (e.g., \\u201E...\\u201C for German „...“). Refer to the `xcode-integration:translation` skill for details |
| `variationTranslation`                                 | object        |       No | Variation structure for strings with top-level plural, device, or width variations. Prefer typographically correct quotes for the target language (e.g., „...“ for German, «...» for French). All curly quotes must be escaped (e.g., \\u201E...\\u201C for German „...“) |
| `variationTranslation.substitutions`                   | array<object> |       No | Optional array of substitution definitions for templates that reference substitutions. Prefer typographically correct quotes for the target language (e.g., „...“ for German, «...» for French). All curly quotes must be escaped (e.g., \\u201E...\\u201C for German „...“) |
| `variationTranslation.substitutions[].argNum`          | integer       |      Yes | Argument number corresponding to the format specifier position in the original string |
| `variationTranslation.substitutions[].formatSpecifier` | string        |      Yes | Format specifier type without %.                             |
| `variationTranslation.substitutions[].name`            | string        |      Yes | Name of the substitution placeholder                         |
| `variationTranslation.substitutions[].variants`        | object        |      Yes | Dictionary mapping variation paths to translated values. Prefer typographically correct quotes for the target language (e.g., „...“ for German, «...» for French). All curly quotes must be escaped (e.g., \\u201E...\\u201C for German „...“) |
| `variationTranslation.topLevelVariation`               | object        |      Yes | Dictionary mapping variation paths to template strings or translations. Prefer typographically correct quotes for the target language (e.g., „...“ for German, «...» for French). All curly quotes must be escaped (e.g., \\u201E...\\u201C for German „...“) |

### Output Schema

**Type:** `object`

**Required output fields:**

- `success`
- `message`

| Field     | Type    | Required | Description                                       |
| --------- | ------- | -------: | ------------------------------------------------- |
| `message` | string  |      Yes | Success or error message                          |
| `success` | boolean |      Yes | Whether the translation was successfully inserted |


## 25. Read String Catalog

**Name:** `StringCatalogRead`


### Description

Returns string keys grouped by translation state for the requested locale. Before calling this tool, you MUST activate the `xcode-integration:translation-coordinator` skill. Do not call this tool without first loading that skill's instructions.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `filePath`
- `targetLocaleIdentifier`

| Field                    | Type    | Required | Description                                     |
| ------------------------ | ------- | -------: | ----------------------------------------------- |
| `filePath`               | string  |      Yes | The path to the String Catalog                  |
| `keyLimit`               | integer |       No | Maximum number of keys to return                |
| `offset`                 | integer |       No | Number of keys to skip before returning results |
| `requestedState`         | string  |       No | The translation state to retrieve keys for      |
| `tabIdentifier`          | string  |      Yes | The workspace tab identifier                    |
| `targetLocaleIdentifier` | string  |      Yes | Locale identifier to check translations for     |

### Output Schema

**Type:** `object`

**Required output fields:**

- `newCount`
- `needsReviewCount`
- `translatedCount`
- `machineTranslatedCount`
- `nextStep`

| Field                    | Type          | Required | Description                                                  |
| ------------------------ | ------------- | -------: | ------------------------------------------------------------ |
| `keys`                   | array<string> |       No | Array of string keys in the requested state. Curly apostrophes and quotes are escaped (e.g., \\u2019 for curly apostrophe, \\u201C for curly quote). |
| `machineTranslatedCount` | integer       |      Yes | Number of machine-translated strings for this locale         |
| `needsReviewCount`       | integer       |      Yes | Number of strings marked as needing review for this locale   |
| `newCount`               | integer       |      Yes | Number of strings that are untranslated for this locale      |
| `nextStep`               | string        |      Yes | Suggestion of what tool to use next                          |
| `requestedState`         | string        |       No | The requested state bucket, or null if not requested         |
| `returnedCount`          | integer       |       No | Number of keys returned after applying pagination            |
| `totalForRequestedState` | integer       |       No | Total number of keys in the requested state bucket before pagination, or null if not requested |
| `translatedCount`        | integer       |      Yes | Number of human-translated strings for this locale           |


## 26. UpdateFileCompilerFlags

**Name:** `UpdateFileCompilerFlags`


### Description

Updates, appends or deletes the additional per-file compiler flags for a single source file in a specific Xcode target — the same value shown in the Compiler Flags column of Target > Build Phases > Compile Sources.

Note: Per-file compiler flags should never be used when target build settings would suffice. Only use per-file compiler flags sparingly, when adding a flag to a specific file is absolutely required and is the only way to solve a problem (e.g. incrementally adopting `-fbounds-safety` by applying it to a single file only). Prefer UpdateTargetBuildSetting in any other case.

Always use this tool to update per-file compiler flags — do not edit project.pbxproj directly.

Note about Swift: Per-file compiler flags on Swift sources are typically ignored by the build system because the unit of compilation for Swift is a module. The value still round-trips through the project, but it may not affect builds. Prefer OTHER_SWIFT_FLAGS at the target level (use UpdateTargetBuildSetting).

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `targetName`
- `filePath`

| Field           | Type    | Required | Description                                                  |
| --------------- | ------- | -------: | ------------------------------------------------------------ |
| `appendValue`   | boolean |       No | Append the value to any existing compiler flags (separated by a single space) instead of replacing them. Has no effect when 'compilerFlags' is omitted. |
| `compilerFlags` | string  |       No | The additional compiler flags to set on the file, as a single space-separated string (e.g. "-DFOO=1 -Wno-unused-variable"). To delete all per-file compiler flags for the file, omit this property. |
| `filePath`      | string  |      Yes | The path to the source file within the Xcode project organization (e.g., 'ProjectName/Sources/MyFile.swift'). An absolute or workspace-relative filesystem path is also accepted as a fallback. |
| `projectPath`   | string  |       No | The project-organization path of the .xcodeproj that owns the target (e.g., 'MyApp/MyApp.xcodeproj'). Only needed when the same target name exists in multiple projects in the workspace; omit otherwise. |
| `tabIdentifier` | string  |      Yes | The workspace tab identifier.                                |
| `targetName`    | string  |      Yes | The name of the Xcode target whose build phase contains the source file. |

### Output Schema

**Type:** `object`

**Required output fields:**

- `targetName`
- `filePath`
- `compilerFlags`
- `previousFlags`

| Field           | Type   | Required | Description                                                  |
| --------------- | ------ | -------: | ------------------------------------------------------------ |
| `compilerFlags` | string |      Yes | The compiler flags now set on the file, as a single space-separated string. Empty when the file has no per-file flags set after the update. |
| `filePath`      | string |      Yes | The project-organization path of the file.                   |
| `guidance`      | string |       No | Populated when a filesystem path was supplied for filePath or projectPath and resolved to a project-organization path. Contains instructions for the agent to prefer the resolved path in subsequent calls. |
| `previousFlags` | string |      Yes | The compiler flags that were set on the file before this update, as a single space-separated string. Empty when no flags were set previously. Useful for confirming what changed without an extra GetFileCompilerFlags call. |
| `targetName`    | string |      Yes | The target whose per-file compiler flags were updated.       |
| `warning`       | string |       No | Populated when the change was applied but the build system is unlikely to honor it (e.g. per-file flags on Swift sources, where the unit of compilation is a module). The flags are still written to the project file so the value reflects the agent's intent, but the agent should consider a target-level alternative. |


## 27. Get Current File

**Name:** `XcodeGetCurrentFile`


### Description

Gets information about the currently active file in the Xcode editor including file path, content, and selection. Returns content in cat -n format with line numbers. Supports reading up to 600 lines by default with optional offset and limit parameters for large files.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`

| Field              | Type    | Required | Description                                                  |
| ------------------ | ------- | -------: | ------------------------------------------------------------ |
| `includeContent`   | boolean |       No | Whether to include file content in the response              |
| `includeSelection` | boolean |       No | Whether to include current selection information             |
| `limit`            | integer |       No | The number of lines to read (only provide if the file is too large to read at once) |
| `offset`           | integer |       No | The line number to start reading from (only provide if the file is too large to read at once) |
| `tabIdentifier`    | string  |      Yes | Identifier for the workspace tab                             |

### Output Schema

**Type:** `object`

**Required output fields:**

- `isEditable`

| Field                      | Type    | Required | Description                                                  |
| -------------------------- | ------- | -------: | ------------------------------------------------------------ |
| `content`                  | string  |       No | Content of the current file formatted with line numbers (cat -n style) |
| `filePath`                 | string  |       No | Full path to the current file                                |
| `isEditable`               | boolean |      Yes | Whether the file is editable                                 |
| `linesRead`                | integer |       No | Number of lines actually read                                |
| `selection`                | object  |       No | Current selection information                                |
| `selection.characterRange` | object  |      Yes | Character range of the selection                             |
| `selection.lineRange`      | object  |      Yes | Line range of the selection                                  |
| `selection.text`           | string  |      Yes | Selected text content                                        |
| `startLine`                | integer |       No | The line number where reading started                        |
| `totalLines`               | integer |       No | Total number of lines in the file                            |


## 28. Search Files

**Name:** `XcodeGlob`


### Description

Finds files in the Xcode project structure matching wildcard patterns. Works on Xcode project organization, not filesystem structure. Example patterns: '*.swift', '**/*.json', 'src/**/*.{swift,m}'. If no pattern is provided, defaults to '**/*' (all files).

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`

| Field           | Type   | Required | Description                                                  |
| --------------- | ------ | -------: | ------------------------------------------------------------ |
| `path`          | string |       No | Which project directory to search in (optional, defaults to root) |
| `pattern`       | string |       No | File matching pattern using wildcards (* ** ? [abc] {swift,m}). Examples: '*.swift', '**/*.json', 'src/**/*.{swift,m}'. Defaults to '**/*' if not provided. |
| `tabIdentifier` | string |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `matches`
- `pattern`
- `searchPath`
- `truncated`
- `totalFound`

| Field                 | Type          | Required | Description                                                  |
| --------------------- | ------------- | -------: | ------------------------------------------------------------ |
| `matches`             | array<string> |      Yes | List of matching file paths in the project structure (truncated at 100), sorted by most recently modified first |
| `message`             | string        |       No | Optional message with additional information about the search results |
| `packageDependencies` | array<string> |       No | Names of package dependencies whose files are included in results |
| `pattern`             | string        |      Yes | The pattern that was searched for                            |
| `searchPath`          | string        |      Yes | The project path that was searched                           |
| `totalFound`          | integer       |      Yes | Total number of files found before truncation                |
| `truncated`           | boolean       |      Yes | Whether results were truncated due to exceeding 100 files    |


## 29. Search Content

**Name:** `XcodeGrep`


### Description

Searches for text patterns in files within the Xcode project structure using regex. Works on Xcode project organization, not filesystem structure. CRITICAL: Must include a 'pattern' argument - this tool will fail without it. Input pattern uses standard regex syntax, not JSON escaping. To find \d in source code, use pattern \\d. Output results are JSON-encoded e.g. backslashes, quotes, and newlines appear escaped (\\, \", \n).

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `pattern`

| Field             | Type                                       | Required | Description                                                  |
| ----------------- | ------------------------------------------ | -------: | ------------------------------------------------------------ |
| `glob`            | string                                     |       No | Only search files matching this pattern                      |
| `headLimit`       | integer                                    |       No | Stop after N results                                         |
| `ignoreCase`      | boolean                                    |       No | Ignore case when matching                                    |
| `linesAfter`      | integer                                    |       No | Show N lines after each match for context                    |
| `linesBefore`     | integer                                    |       No | Show N lines before each match for context                   |
| `linesContext`    | integer                                    |       No | Show N lines both before and after each match                |
| `multiline`       | boolean                                    |       No | Allow patterns to span multiple lines                        |
| `outputMode`      | `content` \| `filesWithMatches` \| `count` |       No | What to return: content, files_with_matches, or count (default: files_with_matches) |
| `path`            | string                                     |       No | Where to search - file or directory in project (defaults to root) |
| `pattern`         | string                                     |      Yes | Text to search for using regex. REQUIRED: Must include a 'pattern' argument - this tool will fail without it. |
| `showLineNumbers` | boolean                                    |       No | Show line numbers with results (content mode only)           |
| `tabIdentifier`   | string                                     |      Yes | The workspace tab identifier                                 |
| `type`            | string                                     |       No | Shortcut for common file types (swift, js, py, etc.)         |

### Output Schema

**Type:** `object`

**Required output fields:**

- `results`
- `pattern`
- `searchPath`
- `matchCount`
- `truncated`

| Field                 | Type          | Required | Description                                                  |
| --------------------- | ------------- | -------: | ------------------------------------------------------------ |
| `matchCount`          | integer       |      Yes | Total number of matches found                                |
| `message`             | string        |       No | Additional information about the search results              |
| `packageDependencies` | array<string> |       No | Names of package dependencies whose files are included in results |
| `pattern`             | string        |      Yes | The pattern that was searched for                            |
| `results`             | array<string> |      Yes | Search results based on output mode                          |
| `searchPath`          | string        |      Yes | The project path that was searched                           |
| `truncated`           | boolean       |      Yes | Whether results were truncated due to limits                 |


## 30. List Workspace Issues

**Name:** `XcodeListNavigatorIssues`


### Description

Lists the currently known issues shown Xcode's Issue Navigator UI in the workspace. These issues include those that have been discovered since the last build, and also issues like package resolution problems and workspace configuration issues. You can filter the issues to include by file name, glob, or severity. Use this tool when you want to list all the users the user can see in the workspace UI.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`

| Field           | Type   | Required | Description                                                  |
| --------------- | ------ | -------: | ------------------------------------------------------------ |
| `glob`          | string |       No | Glob to filter the returned issues. Will match against the 'path' field. |
| `pattern`       | string |       No | Regex to filter the returned issues. Will match against the 'message' field. |
| `severity`      | string |       No | Limit the returned issues to those that have the specified severity or higher. Valid values in order of decreasing severity are 'error', 'warning', 'remark'. Defaults to 'error'. |
| `tabIdentifier` | string |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `issues`
- `truncated`
- `totalFound`

| Field               | Type               | Required | Description                                                  |
| ------------------- | ------------------ | -------: | ------------------------------------------------------------ |
| `issues`            | array<object>      |      Yes | The list of current issues matching the input filters        |
| `issues[].category` | string             |       No | The category of the issue, if known                          |
| `issues[].line`     | integer            |       No | The line number where the issue was detected, if known       |
| `issues[].message`  | string             |      Yes | The message describing the issue                             |
| `issues[].path`     | string             |       No | The file path where the issue was detected, if any           |
| `issues[].severity` | string             |      Yes | The severity of issue (error, warning, remark)               |
| `issues[].vitality` | `fresh` \| `stale` |       No | Whether an issue from a previous build is known to still be relevant or whether something might have changed since it was emitted (for example if the source file has been edited and it isn't yet known whether that edit fixes the issue). Possible values: (fresh, stale) |
| `message`           | string             |       No | Optional message with additional information about the search results |
| `totalFound`        | integer            |      Yes | Total number of issues before truncation                     |
| `truncated`         | boolean            |      Yes | Whether results were truncated due to exceeding 100 issues   |


## 31. List Run Destinations

**Name:** `XcodeListRunDestinations`


### Description

Lists run destinations available for the currently active scheme, grouped the same way the Xcode picker groups them (Devices, Simulators, Build, Incompatible, etc.), and identifies which one is active. Each entry includes the destination's `displayTitle` (matching the Xcode toolbar label and the unambiguous handle for XcodeSwitchRunDestination), its group, eligibility status, platform, OS version, architecture, and SDK display name. By default, destinations in the 'Incompatible' group are omitted from the inline list to save context (the `groups` summary still reports their count and the full file at fullRunDestinationListPath still contains them). Pass `includeIncompatible: true` to include them inline. The inline list is capped at 40 entries in both modes, with the active destination listed first; the complete list is always available via fullRunDestinationListPath.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`

| Field                 | Type    | Required | Description                                                  |
| --------------------- | ------- | -------: | ------------------------------------------------------------ |
| `includeIncompatible` | boolean |       No | When true, destinations in the 'Incompatible' group are included in the inline `destinations` array. When false or omitted (the default), they are skipped to save context but are still counted in the `groups` summary and listed in fullRunDestinationListPath. |
| `tabIdentifier`       | string  |      Yes | The workspace tab identifier.                                |

### Output Schema

**Type:** `object`

**Required output fields:**

- `destinations`
- `groups`
- `truncated`
- `totalDestinations`
- `fullRunDestinationListPath`

| Field                                 | Type          | Required | Description                                                  |
| ------------------------------------- | ------------- | -------: | ------------------------------------------------------------ |
| `activeDestinationDisplayTitle`       | string        |       No | The disambiguated displayTitle of the currently active run destination, or null if no destination is active. This value round-trips: pass it back to XcodeSwitchRunDestination to re-select the same destination. |
| `activeSchemeName`                    | string        |       No | The name of the currently active scheme, or null if no scheme is active. Run destinations are scoped to the active scheme. |
| `destinations`                        | array<object> |      Yes | Inline run destination entries. By default the 'Incompatible' group is omitted; pass `includeIncompatible: true` to include it. Always capped at 40 entries, with the active destination listed first. |
| `destinations[].architecture`         | string        |       No | The destination's target architecture (for example, 'arm64' or 'x86_64'). |
| `destinations[].displayTitle`         | string        |      Yes | The destination's name as shown in the Xcode picker, including any disambiguation suffix when needed. This is the handle to pass back to XcodeSwitchRunDestination — it always round-trips. |
| `destinations[].group`                | string        |      Yes | The destination's group as shown in the picker (for example: 'Devices', 'Simulators', 'Virtual Machines', 'Build', 'Incompatible'). |
| `destinations[].ineligibilityMessage` | string        |       No | Human-readable explanation of why this destination is ineligible, if any. |
| `destinations[].isActive`             | boolean       |      Yes | True if this is the currently active run destination.        |
| `destinations[].isEligible`           | boolean       |      Yes | True if this destination can be used to build, run, or test the active scheme. Destinations in the 'Incompatible' group return false. |
| `destinations[].isGenericDevice`      | boolean       |      Yes | True if this destination represents a generic build target such as 'Any iOS Device'. |
| `destinations[].isSimulator`          | boolean       |      Yes | True if this destination represents a simulator device.      |
| `destinations[].osVersion`            | string        |       No | The operating system version of the destination's device (for example, '18.4'), if known. |
| `destinations[].platformIdentifier`   | string        |       No | The platform identifier of the destination's device (for example, 'com.apple.platform.iphonesimulator'). |
| `destinations[].sdkDisplayName`       | string        |       No | The display name of the destination's target SDK (for example, 'iOS 18.4 Simulator'). |
| `fullRunDestinationListPath`          | string        |      Yes | Path to a file containing every destination — including incompatible ones — in grep-friendly format. |
| `groups`                              | array<object> |      Yes | Summary of every destination group in picker order, each with a name and total count (independent of any inline filtering). |
| `groups[].count`                      | integer       |      Yes | The number of run destinations in this group (independent of inline filtering). |
| `groups[].name`                       | string        |      Yes | The group's display name as shown in the picker.             |
| `message`                             | string        |       No | Optional message explaining why the inline list differs from totalDestinations or noting other context (such as no active scheme). |
| `totalDestinations`                   | integer       |      Yes | Total number of destinations available for the active scheme, before any filtering or truncation. |
| `truncated`                           | boolean       |      Yes | True if the inline `destinations` array contains fewer entries than `totalDestinations` (because of incompatible filtering, the 40-entry cap, or both). |


## 32. List Schemes

**Name:** `XcodeListSchemes`


### Description

Lists all schemes available in the current Xcode workspace and identifies which one is currently
active. Each entry includes the scheme's name, its sharing status (shared vs. user-only), and the
project or workspace container that holds it. Inline results are limited to 100
schemes with the active scheme listed first; the complete list is always written to
fullSchemeListPath in grep-friendly format with keys like SCHEME_NAME, DISAMBIGUATED_NAME,
IS_ACTIVE, IS_SHARED, and CONTAINER_NAME. Use the XcodeSwitchScheme tool to change the active scheme.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`

| Field           | Type   | Required | Description                   |
| --------------- | ------ | -------: | ----------------------------- |
| `tabIdentifier` | string |      Yes | The workspace tab identifier. |

### Output Schema

**Type:** `object`

**Required output fields:**

- `schemes`
- `truncated`
- `totalSchemes`
- `fullSchemeListPath`

| Field                         | Type          | Required | Description                                                  |
| ----------------------------- | ------------- | -------: | ------------------------------------------------------------ |
| `activeSchemeName`            | string        |       No | The name of the currently active scheme, or null if no scheme is active. |
| `fullSchemeListPath`          | string        |      Yes | Path to a file containing the complete scheme list in grep-friendly format. |
| `message`                     | string        |       No | Optional message with additional information about truncation. |
| `schemes`                     | array<object> |      Yes | Visible, non-transient schemes in the workspace, limited to 100 entries with the active scheme listed first. |
| `schemes[].containerName`     | string        |       No | The display name of the project or workspace container that hosts this scheme. |
| `schemes[].disambiguatedName` | string        |      Yes | The scheme's name with the container name appended in parentheses when multiple schemes share the same name. Pass this value to XcodeSwitchScheme to resolve ambiguity. |
| `schemes[].isActive`          | boolean       |      Yes | True if this is the currently active scheme.                 |
| `schemes[].isShared`          | boolean       |      Yes | True if this scheme is shared (committed to source control alongside the project); false if it is a user-only scheme. |
| `schemes[].name`              | string        |      Yes | The scheme's name as it appears in the Xcode scheme picker.  |
| `totalSchemes`                | integer       |      Yes | Total number of schemes before truncation.                   |
| `truncated`                   | boolean       |      Yes | True if the inline schemes array was truncated because more than 100 schemes exist. |


## 33. List Windows

**Name:** `XcodeListWindows`


### Description

Lists the current Xcode windows and their workspace information

### Input Schema

**Type:** `object`

**Required input fields:**

_None._

_No fields defined._

### Output Schema

**Type:** `object`

**Required output fields:**

- `message`

| Field     | Type   | Required | Description                           |
| --------- | ------ | -------: | ------------------------------------- |
| `message` | string |      Yes | Description of all open Xcode windows |


## 34. List Directory Contents

**Name:** `XcodeLS`


### Description

Lists files and directories in the Xcode project structure at the specified path. Works on Xcode project organization, not filesystem structure.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `path`

| Field           | Type          | Required | Description                                                  |
| --------------- | ------------- | -------: | ------------------------------------------------------------ |
| `ignore`        | array<string> |       No | Skip files/folders matching these patterns                   |
| `path`          | string        |      Yes | The project path to browse (e.g., 'ProjectName/Sources/')    |
| `recursive`     | boolean       |       No | Recursively list all files (truncated to 100 lines). Default: true |
| `tabIdentifier` | string        |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `items`
- `path`

| Field                 | Type          | Required | Description                                                  |
| --------------------- | ------------- | -------: | ------------------------------------------------------------ |
| `items`               | array<string> |      Yes | List of files and directories at the specified path          |
| `message`             | string        |       No | Optional message about the operation                         |
| `packageDependencies` | array<string> |       No | Names of items that are package dependencies rather than regular project directories |
| `path`                | string        |      Yes | The resolved project path that was browsed                   |


## 35. Create Directory

**Name:** `XcodeMakeDir`


### Description

Creates directories and groups in the Xcode project structure.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `directoryPath`

| Field           | Type   | Required | Description                                                 |
| --------------- | ------ | -------: | ----------------------------------------------------------- |
| `directoryPath` | string |      Yes | Project navigator relative path for the directory to create |
| `tabIdentifier` | string |      Yes | The workspace tab identifier                                |

### Output Schema

**Type:** `object`

**Required output fields:**

- `success`
- `message`

| Field         | Type    | Required | Description                              |
| ------------- | ------- | -------: | ---------------------------------------- |
| `createdPath` | string  |       No | The path of the created directory        |
| `message`     | string  |      Yes | Result message describing the operation  |
| `success`     | boolean |      Yes | Whether the directory creation succeeded |


## 36. Move File

**Name:** `XcodeMV`


### Description

Moves or renames files and directories in the project navigator with support for filesystem operations.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `sourcePath`
- `destinationPath`

| Field                | Type             | Required | Description                                                  |
| -------------------- | ---------------- | -------: | ------------------------------------------------------------ |
| `destinationPath`    | string           |      Yes | Project navigator relative path for the destination (for move) or new name (for rename) |
| `operation`          | `move` \| `copy` |       No | The type of move operation to perform                        |
| `operation.rawValue` | string           |      Yes | Move operation type                                          |
| `overwriteExisting`  | boolean          |       No | Whether to overwrite existing files at the destination       |
| `sourcePath`         | string           |      Yes | Project navigator relative path of the source item to move/rename |
| `tabIdentifier`      | string           |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `success`
- `operation`
- `message`

| Field                  | Type    | Required | Description                                 |
| ---------------------- | ------- | -------: | ------------------------------------------- |
| `destinationFinalPath` | string  |       No | The final path of the moved/renamed item    |
| `message`              | string  |      Yes | Result message describing the operation     |
| `operation`            | string  |      Yes | The operation that was performed            |
| `sourceOriginalPath`   | string  |       No | The original path of the source item        |
| `success`              | boolean |      Yes | Whether the move/rename operation succeeded |


## 37. Read

**Name:** `XcodeRead`


### Description

Reads the contents of a file within the Xcode project organization. Returns content in cat -n format with line numbers. Supports reading up to 600 lines by default with optional offset and limit parameters for large files. Output is JSON-encoded e.g. backslashes, quotes, and newlines appear escaped (\\, \", \n). Account for this when interpreting file content.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `filePath`

| Field           | Type    | Required | Description                                                  |
| --------------- | ------- | -------: | ------------------------------------------------------------ |
| `filePath`      | string  |      Yes | The path to the file within the Xcode project organization (e.g., 'ProjectName/Sources/MyFile.swift') |
| `limit`         | integer |       No | The number of lines to read (only provide if the file is too large to read at once) |
| `offset`        | integer |       No | The line number to start reading from (only provide if the file is too large to read at once) |
| `tabIdentifier` | string  |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `content`
- `filePath`
- `totalLines`
- `linesRead`
- `startLine`
- `fileSize`

| Field        | Type    | Required | Description                                                  |
| ------------ | ------- | -------: | ------------------------------------------------------------ |
| `content`    | string  |      Yes | The file contents formatted with line numbers (cat -n style) |
| `filePath`   | string  |      Yes | The project path that was read                               |
| `fileSize`   | integer |      Yes | Size of the file in bytes                                    |
| `linesRead`  | integer |      Yes | Number of lines actually read                                |
| `message`    | string  |       No | Optional message about the operation                         |
| `startLine`  | integer |      Yes | The line number where reading started                        |
| `totalLines` | integer |      Yes | Total number of lines in the file                            |


## 38. XcodeRefreshCodeIssuesInFile

**Name:** `XcodeRefreshCodeIssuesInFile`


### Description

Retrieves current compiler diagnostics (errors, warnings, notes) for a file in the Xcode project. Returns formatted diagnostic information including severity levels and messages.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `filePath`

| Field           | Type   | Required | Description                                                  |
| --------------- | ------ | -------: | ------------------------------------------------------------ |
| `filePath`      | string |      Yes | The path to the file within the Xcode project organization (e.g., 'ProjectName/Sources/MyFile.swift') |
| `tabIdentifier` | string |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `filePath`
- `diagnosticsCount`
- `content`
- `success`

| Field              | Type    | Required | Description                                                  |
| ------------------ | ------- | -------: | ------------------------------------------------------------ |
| `content`          | string  |      Yes | Formatted diagnostic output with severity levels and messages |
| `diagnosticsCount` | integer |      Yes | Number of diagnostics found (errors, warnings, notes)        |
| `filePath`         | string  |      Yes | The project path that was checked for diagnostics            |
| `success`          | boolean |      Yes | Whether the diagnostic retrieval completed successfully      |


## 39. Remove File

**Name:** `XcodeRM`


### Description

Removes files and directories from the Xcode project structure and optionally deletes the underlying files from the filesystem.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `path`

| Field           | Type    | Required | Description                                                  |
| --------------- | ------- | -------: | ------------------------------------------------------------ |
| `deleteFiles`   | boolean |       No | Also move the underlying files to Trash (defaults to true)   |
| `path`          | string  |      Yes | The project path to remove (e.g., 'ProjectName/Sources/MyFile.swift') |
| `recursive`     | boolean |       No | Remove directories and their contents recursively            |
| `tabIdentifier` | string  |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `removedPath`
- `success`
- `message`

| Field         | Type    | Required | Description                                  |
| ------------- | ------- | -------: | -------------------------------------------- |
| `message`     | string  |      Yes | Status message about the removal operation   |
| `removedPath` | string  |      Yes | The project path that was removed            |
| `success`     | boolean |      Yes | Whether the removal operation was successful |


## 40. Switch Run Destination

**Name:** `XcodeSwitchRunDestination`


### Description

Changes the active run destination for the currently active scheme. The active scheme is left unchanged. Pass the destination's `displayTitle` (the disambiguated label shown in the Xcode picker). Valid sources: any `displayTitle` field returned by XcodeListRunDestinations, the list tool's `activeDestinationDisplayTitle` field, this tool's own `activeDestinationDisplayTitle` from a prior call, or XcodeSwitchScheme's `activeDestinationDisplayTitle` field — outputs from any of these chain back into this parameter without re-listing. Refuses to switch to a destination that is not eligible for the active scheme and reports the framework's reason. The output reflects Xcode's post-switch state.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `displayTitle`

| Field           | Type   | Required | Description                                                  |
| --------------- | ------ | -------: | ------------------------------------------------------------ |
| `displayTitle`  | string |      Yes | The destination's `displayTitle` — the disambiguated label shown in the Xcode picker. Round-trips through any of these prior tool outputs without needing a fresh XcodeListRunDestinations call: this tool's own `activeDestinationDisplayTitle`, XcodeSwitchScheme's `activeDestinationDisplayTitle`, or any `displayTitle` / `activeDestinationDisplayTitle` from XcodeListRunDestinations. |
| `tabIdentifier` | string |      Yes | The workspace tab identifier.                                |

### Output Schema

**Type:** `object`

**Required output fields:**

- `activeDestinationDisplayTitle`
- `activeSchemeName`
- `message`

| Field                           | Type   | Required | Description                                                  |
| ------------------------------- | ------ | -------: | ------------------------------------------------------------ |
| `activeDestinationDisplayTitle` | string |      Yes | The disambiguated displayTitle of the run destination that is now active, read from Xcode after the switch (so it reflects any auto-resolution Xcode applied). This value round-trips through XcodeSwitchRunDestination. |
| `activeSchemeName`              | string |      Yes | The name of the scheme the run destination is associated with (unchanged by this action). |
| `message`                       | string |      Yes | A human-readable description of the result.                  |


## 41. Switch Scheme

**Name:** `XcodeSwitchScheme`


### Description

Changes the active scheme in the current Xcode workspace to the specified scheme.
Accepts either a scheme's name or its disambiguated name (needed when multiple schemes share the same name).
Use the XcodeListSchemes tool to discover available scheme names.
The active run destination may be adjusted automatically when the previous one isn't compatible with the newly selected scheme — and Xcode may pick a different *variant* (for example, switching from "My Mac" to "My Mac (Mac Catalyst)") even when the destination's display name appears similar. The response's `activeDestinationDisplayTitle` matches what XcodeListRunDestinations returns, so callers can detect such auto-resolutions without a follow-up list call.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `schemeName`

| Field           | Type   | Required | Description                                                  |
| --------------- | ------ | -------: | ------------------------------------------------------------ |
| `schemeName`    | string |      Yes | The name of the scheme to make active. To resolve ambiguity when multiple schemes share the same name, use the disambiguated name returned by XcodeListSchemes (for example, "MyScheme (MyProject)"). |
| `tabIdentifier` | string |      Yes | The workspace tab identifier.                                |

### Output Schema

**Type:** `object`

**Required output fields:**

- `activeSchemeName`
- `message`

| Field                           | Type   | Required | Description                                                  |
| ------------------------------- | ------ | -------: | ------------------------------------------------------------ |
| `activeDestinationDisplayTitle` | string |       No | The disambiguated displayTitle of the active run destination — the same string XcodeListRunDestinations returns. Use this to detect when Xcode auto-resolved the destination to a different variant (e.g. from 'My Mac' to 'My Mac (Mac Catalyst)') because the previous destination wasn't compatible with the new scheme. |
| `activeSchemeName`              | string |      Yes | The name of the scheme that is now active.                   |
| `message`                       | string |      Yes | A human-readable description of the result.                  |


## 42. Edit File

**Name:** `XcodeUpdate`


### Description

Edits files in the Xcode project by replacing text content. Works on Xcode project structure paths, not filesystem paths. IMPORTANT: The tool will fail if filePath, oldString, or newString parameters are missing. Input oldString and newString use literal characters e.g. if XcodeRead shows \\d, use \d in parameters to match it.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `filePath`
- `oldString`
- `newString`

| Field           | Type    | Required | Description                                                  |
| --------------- | ------- | -------: | ------------------------------------------------------------ |
| `filePath`      | string  |      Yes | REQUIRED: The path to the file to modify within the Xcode project organization (e.g., 'ProjectName/Sources/MyFile.swift') |
| `newString`     | string  |      Yes | REQUIRED: The text to replace it with, must be different from oldString |
| `oldString`     | string  |      Yes | REQUIRED: The text to replace                                |
| `replaceAll`    | boolean |       No | Replace all occurrences of oldString (default false)         |
| `tabIdentifier` | string  |      Yes | The workspace tab identifier                                 |

### Output Schema

**Type:** `object`

**Required output fields:**

- `filePath`
- `editsApplied`
- `success`
- `originalContentLength`
- `modifiedContentLength`

| Field                   | Type    | Required | Description                                                  |
| ----------------------- | ------- | -------: | ------------------------------------------------------------ |
| `editsApplied`          | integer |      Yes | Number of successful text replacements made                  |
| `filePath`              | string  |      Yes | The path of the file that was edited                         |
| `message`               | string  |       No | Optional message with additional information about the edit results |
| `modifiedContentLength` | integer |      Yes | Length of the file content after editing                     |
| `originalContentLength` | integer |      Yes | Length of the file content before editing                    |
| `success`               | boolean |      Yes | Whether the edit operation completed successfully            |


## 43. Write File

**Name:** `XcodeWrite`


### Description

Creates or overwrites files with content in the Xcode project. Works on Xcode project structure paths, not filesystem paths. Automatically adds new files to the project structure. Both filePath and content parameters are required. Input content uses literal characters e.g. if XcodeRead shows \\d, use \d in the content parameter to write it.

### Input Schema

**Type:** `object`

**Required input fields:**

- `tabIdentifier`
- `filePath`
- `content`

| Field           | Type   | Required | Description                                                  |
| --------------- | ------ | -------: | ------------------------------------------------------------ |
| `content`       | string |      Yes | REQUIRED: The content to write to the file                   |
| `filePath`      | string |      Yes | REQUIRED: The path to the file within the Xcode project organization (e.g., 'ProjectName/Sources/MyFile.swift') |
| `tabIdentifier` | string |      Yes | The workspace tab identifier (required)                      |

### Output Schema

**Type:** `object`

**Required output fields:**

- `success`
- `filePath`
- `bytesWritten`
- `linesWritten`
- `message`
- `wasExistingFile`

| Field             | Type    | Required | Description                                       |
| ----------------- | ------- | -------: | ------------------------------------------------- |
| `absolutePath`    | string  |       No | The absolute file system path of the written file |
| `bytesWritten`    | integer |      Yes | Number of bytes written to the file               |
| `filePath`        | string  |      Yes | The project path that was written                 |
| `linesWritten`    | integer |      Yes | Number of lines written to the file               |
| `message`         | string  |      Yes | Result message describing the write operation     |
| `success`         | boolean |      Yes | Whether the file write operation succeeded        |
| `wasExistingFile` | boolean |      Yes | Whether the file existed before writing           |

|      |      |      |      |
| ---- | ---- | ---: | ---- |
|      |      |      |      |
|      |      |      |      |
|      |      |      |      |
|      |      |      |      |