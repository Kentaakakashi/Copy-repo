# THEO AI — Complete Project Handoff Summary

Project handoff • Updated 15 October 2026 • Covers the project background, requirements, architecture, repository work, API integration, screen automation, design, voice, current testing status, known limitations, and next steps.

This is the detailed handoff for continuing development of **Theo, your Android AI assistant**. It is written directly in this response, not as a text file.

The goal is to preserve the decisions, requirements, implementation details, known problems, and outstanding work so another development session can continue without starting from scratch or repeating earlier mistakes.

---

# 1. Project overview

## What Theo is supposed to become

Theo is a native Android AI assistant that should combine conversational AI, voice interaction, screen understanding, and the ability to operate other Android applications.

It should feel like a personal, JARVIS-inspired assistant rather than a basic chatbot with an API field.

The intended experience includes:

- Natural-language conversations powered by APIs that you configure.
- A cinematic, futuristic interface inspired by a solar core.
- Voice input and spoken responses.
- Screen understanding through screenshots and accessibility information.
- App launching and navigation based on natural-language instructions.
- Intelligent waiting when an application is loading.
- Safe execution of actions with confirmation for sensitive operations.
- Honest reporting of whether an action actually succeeded.
- Future integration of more capabilities inspired by the desktop J.A.R.V.I.S. project.

The central requirement is that Theo must **actually perform the requested action and verify the result**. It must not merely describe what it would do, pretend that an action succeeded, or claim a screen changed when it did not.

For example, if you tell Theo to open Discord and navigate to a chat, it should launch Discord, wait for it to load, identify the correct chat, open it, and confirm the outcome based on observable evidence.

If the chat cannot be identified, Theo should ask you instead of guessing.

## The guiding principle

The project is not complete simply because the AI can produce a convincing response. The Android app must connect the AI's plan to real device capabilities, execute the steps, observe the outcome, and report the truth.

---

# 2. Repository and development environment

- **Repository:** https://github.com/Kentaakakashi/Theo-AI-mobile
- **Development branch:** `feature/screen-aware-automation`
- **Pull request:** https://github.com/Kentaakakashi/Theo-AI-mobile/pull/1
- **PR state recorded in handoff:** Draft; no merge was requested.

### Technology stack

The project is a native Android application using:

- Kotlin.
- Jetpack Compose for the interface.
- Android accessibility services for interacting with other apps.
- Android APIs for screen capture on supported versions.
- Configured cloud AI APIs for language and vision tasks.
- OkHttp for network requests in the new vision pipeline.
- Android SharedPreferences for settings persistence, including API-key settings.
- GitHub Actions for automated compilation and build checks.

The development workflow has involved editing repository files directly through the connected GitHub tools, committing changes to the development branch, and inspecting GitHub Actions results.

### Important working constraints

These requirements must continue to be followed:

1. **Do not merge the pull request unless explicitly asked.**
2. Make changes on the existing development branch.
3. Inspect the current source before modifying it; do not assume previous edits or names are unchanged.
4. Check build results after changes.
5. Investigate actual CI errors and fix them instead of claiming the build passed.
6. Do not report a device test as completed unless it was actually performed.
7. Do not ask you to paste API keys into the conversation. Keys should be entered in Theo's settings.
8. Do not claim a feature is fully functional merely because its code has been added.

---

# 3. Original project vision and requirements

The project has evolved from a general assistant toward a screen-aware mobile agent.

## A. Natural-language control

Theo should understand instructions such as:

- “Open Discord.”
- “Find the conversation with my friend.”
- “Wait until the page finishes loading.”
- “Tell me what is currently on my screen.”
- “If you are unsure which item I mean, ask me.”
- “Don't send the message yet.”

These should be treated as real tasks rather than requests for instructions.

The AI is responsible for understanding the request and planning. Android is responsible for carrying out permitted actions through the appropriate system interfaces.

## B. Real execution and truthful reporting

You explicitly required that Theo not fake execution reports.

The accepted behavior is:

- Safe actions can execute automatically.
- Sensitive actions require confirmation.
- Ambiguous requests should trigger a clarifying question.
- Theo must verify the result before claiming success.
- Failure should be reported clearly, along with whatever useful information is available.

A successful AI response is not proof that an Android action succeeded.

For example, “I opened the chat” is only justified after Theo has evidence that the target conversation is actually open.

## C. Screen-aware intelligence

Theo needs to reason about both:

1. The Android accessibility tree, which can expose controls, labels, text, and interaction targets.
2. The visual screen, which can show layouts, loading indicators, icons, images, dialogs, and other visual information that may be missing from the accessibility tree.

The recent work adds a cloud vision component to complement the existing accessibility-based system.

## D. Loading awareness

Theo must not treat a temporary splash screen, logo, blank page, spinner, or skeleton layout as the final destination.

It should wait for the app to load, observe the new screen, and continue only when appropriate.

## E. Cinematic personality and interface

The intended design is futuristic and polished, with a solar-core visual identity and a JARVIS-inspired voice. The interface should feel intentional rather than like a generic AI application.

---

# 4. Visual design: the solar-core interface

The interface work introduced a cinematic visual direction.

### Established design elements

- Black or very dark backgrounds.
- Amber and orange solar-flare accents.
- A central orb representing Theo's core.
- Radiating flare spokes and orbiting particles.
- A futuristic presentation with restrained, high-contrast elements.
- A clear voice interaction affordance, including the label `TAP CORE TO SPEAK`.
- A small `// SOLAR CORE` identity label.

One earlier implementation commit associated with this visual work was:

`6e37406e9d02297864d2ae6f670a55ad739e7d3a`

That implementation included 32 flare spokes and 96 orbit particles.

The project should preserve this visual identity while prioritizing usability. The animation should not obscure controls, interfere with screen reading, or make the assistant feel slow.

The interface and the agent runtime are separate concerns: a convincing animation does not establish that voice recognition, vision, or automation is working.

---

# 5. The JARVIS inspiration and feature-porting plan

You wanted Theo to draw inspiration from the existing desktop project:

https://github.com/allurimohansrisaivarma-coder/J.A.R.V.I.S

That repository is a Windows-oriented Python assistant, not a drop-in Android implementation. Its capabilities need to be adapted to Android rather than copied literally.

## Capabilities identified from the desktop project

The desktop project's described feature set includes:

- Groq-hosted Qwen language and vision capabilities.
- Gemini vision fallback.
- Groq Whisper speech-to-text.
- Edge TTS speech output.
- Optional LanceDB and SQLite long-term memory.
- Screen understanding.
- File access.
- Google Calendar and Gmail integrations.
- MCP tools.
- Search, weather, browser, and world-monitor tools.
- Windows local application launching.
- Barge-in support.
- A floating HUD/widget.
- System monitoring.

These are reference capabilities, not a claim that all of them have already been implemented in the Android app.

### How to adapt them

| Desktop capability | Android direction |
|---|---|
| Local Windows application launcher | Android package discovery and app launching |
| Screen understanding | Accessibility tree plus Android screenshot capture and cloud vision |
| Desktop speech input | Android-compatible speech recognition or a configured cloud STT provider |
| Edge TTS | Android TTS initially; optional cloud TTS later |
| Persistent memory | A suitable Android database and retrieval system |
| File access | Android storage APIs and permissions |
| Calendar and email | Explicitly configured integrations with suitable authentication |
| Floating HUD | Android-compatible overlay or foreground-service design where appropriate |
| System monitoring | Android-supported diagnostics and system APIs |
| MCP tools | Android-compatible network/tool integration rather than desktop-only assumptions |

Each capability should be introduced in a way that respects Android's permissions, lifecycle, security model, and background-execution restrictions.

---

# 6. Voice design and current direction

The desired voice is a cinematic, British male assistant voice inspired by JARVIS.

## Reference voice settings

The desktop J.A.R.V.I.S. project was described as using Microsoft Edge TTS with:

- Voice: `en-GB-RyanNeural`
- Pitch: `-5Hz`
- Rate: `+20%`

Those are reference settings from the desktop project, not settings that automatically transfer to Android's built-in speech engine.

Other voice-related repositories were also considered:

- https://github.com/TheJoshCode/Jarvis/ — an alternative with on-device speech/vision-related work and heavier requirements.
- https://github.com/Mortalbeats/J.A.R.V.I.S/tree/main/jarvis-assistant — a reference for the Edge TTS Ryan voice.
- https://github.com/strangedeev/jarvis — a reference using ElevenLabs, Whisper, and memory.

These are research references, not integrated dependencies unless explicitly added later.

## Android voice changes already made

A change was committed to prefer a British English voice when the device has one installed, with a US English fallback.

The associated commit is:

`11e748aca68f499c056b3197bf35c276de90f3e6`

The native TTS settings used were:

- Pitch: `0.86`
- Speech rate: `0.94`

These values are specific to Android TTS and are not directly equivalent to the desktop Edge TTS values.

### Limitations

- The exact voice depends on which voice data is installed on the user's Android device.
- A British English preference does not guarantee a particular voice will be available.
- Native Android TTS is not an exact reproduction of a particular actor or film voice.
- A cloud voice provider such as ElevenLabs could be explored later if higher fidelity becomes a priority.

### Build follow-up

A prior GitHub Actions run associated with the voice change was `38755717723`. Its final result was not established in the handoff notes, so it should not be described as a confirmed passing build without checking the actual run.

---

# 7. Screen automation: the original implementation

The automation foundation existed before the latest vision integration.

## Relevant source files

| File | Responsibility |
|---|---|
| `app/src/main/java/com/example/service/ScreenAutomation.kt` | Automation action definitions and execution logic |
| `app/src/main/java/com/example/service/ActionIntentParser.kt` | Parsing or interpreting requested actions |
| `app/src/main/java/com/example/service/TheoAccessibilityService.kt` | Android accessibility interaction and screen inspection |
| `app/src/main/java/com/example/ui/screens/ScreenAutomationSettingsScreen.kt` | Automation settings UI |
| `app/src/main/java/com/example/viewmodel/TheoViewModel.kt` | Application state and orchestration |
| `app/src/main/java/com/example/service/TheoAiEngine.kt` | AI-driven planning |
| `app/src/test/java/com/example/ScreenAutomationTest.kt` | Automation-related unit tests |
| `SCREEN_AUTOMATION.md` | Documentation for automation behavior and limitations |

A `THIRD_PARTY_NOTICES` entry references MobileAgent-Android under the MIT license.

## Existing safeguards

The original implementation established the following principles:

- Screen automation is opt-in.
- Safe actions may execute automatically.
- Sensitive actions require confirmation.
- Ambiguous app matches should trigger clarification.
- The plan has a 12-step bound.
- Target package validation is used to constrain app actions.
- Accessibility information supplies the identifiers used to interact with UI controls.

These protections should remain intact as vision support is expanded.

### Why node-based interaction matters

An accessibility tree may expose a button with a text label and a corresponding node identifier. Theo can use that identifier to select the intended control.

A screenshot, by contrast, may show a button visually without providing a reliable accessibility identifier.

The latest implementation uses visual information to improve understanding, but it still requires a matching accessibility control for the existing action path. It does **not** yet implement general-purpose coordinate-based tapping based solely on the screenshot.

This distinction is crucial when evaluating the current feature.

---

# 8. App discovery and launching history

Earlier development added app-launching and action-dispatch behavior.

The relevant historical commits were:

- `6fe6fefd` — app launching.
- `308e1276` — action dispatch.
- `e846a089` — package discovery.

The app-name matching was described as exact or limited at that point. The current code should be inspected to determine how much matching has since improved.

The intended flow is:

1. Interpret the user's requested app or action.
2. Resolve the app to a valid installed package.
3. Check that the requested action is allowed.
4. Execute it through Android's available mechanisms.
5. Observe the screen or application state.
6. Verify the result.
7. Report success, failure, or uncertainty accurately.

A package launch only establishes that the launch operation was requested or initiated. It does not prove the target app finished loading or that the requested destination was reached.

---

# 9. Screen loading and retry behavior

Loading awareness was added before the cloud vision integration.

## Existing timing behavior

The implementation was documented as using:

- Up to 12 attempts to obtain a screen snapshot.
- A 650 ms delay between snapshot attempts.
- Approximately 7.8 seconds of retry time under the maximum retry pattern.
- A 700 ms wait for a stable screen fingerprint.
- Model-requested `wait` actions that retry at 1.2-second intervals, up to five times.

The exact runtime behavior should be confirmed against the current source before changing these values.

## Prompting rules

The AI planning instructions were designed to recognize that these may be temporary states:

- App splash screens.
- Logos.
- Spinners.
- Skeleton/loading layouts.
- Blank or partially rendered screens.

The model is expected to return a wait action rather than assume that a temporary screen is the final result.

It should also ask for clarification if the requested chat recipient is not specified.

### Important future consideration

Waiting should not mean blindly sleeping for a fixed period and then assuming success. The stronger approach is to repeatedly inspect the screen and continue when the expected state is observable, with sensible timeouts and clear failure reporting.

---

# 10. Latest major feature: cloud-based screen vision

This was the central technical change in the most recent development session.

You said that you had obtained both API keys: **Groq and Gemini**.

The implementation was then extended so Theo could send a captured screenshot to a configured vision model and use the returned description as additional context when planning screen actions.

## The intended pipeline

1. **User's natural-language instruction.** Example: open Discord and find a chat.
2. **Accessibility screen snapshot.** Collect accessible controls and their identifiers.
3. **Screenshot capture.** On supported Android versions, capture the active display.
4. **Cloud vision analysis.** Groq Qwen vision first; Gemini as fallback.
5. **AI action planning.** Use the visual description alongside accessible UI information.
6. **Guarded action execution and verification.** Use allowed UI controls; confirm sensitive actions.

This is the intended architecture, not a claim that the complete flow has already passed a real-device test.

## A. Screenshot capture

A method was added to:

`app/src/main/java/com/example/service/TheoAccessibilityService.kt`

The method is:

`captureActiveScreenJpeg()`

It uses Android's `AccessibilityService.takeScreenshot` API on Android 11 and newer, which corresponds to API level 30+.

The capture path was designed to:

- Capture the active display.
- Convert the image to JPEG.
- Limit the width to 1280 pixels.
- Compress at JPEG quality 78.
- Keep screenshot data in memory rather than save it to disk.
- Avoid logging screenshot contents.
- Return no screenshot on unsupported older Android versions, allowing the rest of the flow to fall back to accessibility-tree-only operation.

The implementation uses the Android screenshot callback and bitmap conversion APIs. These details still require verification through a successful build and a device test.

## B. Vision analyzer

A new file was added:

`app/src/main/java/com/example/service/ScreenVisionAnalyzer.kt`

Its purpose is to send the screenshot to a configured vision API and return a textual description of the screen.

The intended provider order is:

1. Groq Qwen vision as the primary provider.
2. Gemini as the fallback.
3. Accessibility-tree-only operation if screenshot analysis cannot be completed.

The analyzer was designed to send image data directly to the cloud API without creating a persistent screenshot file.

Its prompts instruct the vision model to treat the contents of the screen as untrusted data and describe visible controls, dialogs, and loading states.

The visual model's output is context for the planner. It is not permission to bypass the existing safeguards.

### Groq configuration

The current implementation uses the model identifier:

`qwen/qwen3.6-27b`

The endpoint used is:

`https://api.groq.com/openai/v1/chat/completions`

The image is passed as a base64 data URL.

This integration was based on the Groq vision documentation reviewed during development. Model availability and request compatibility should still be checked against the current provider documentation and a successful API call.

### Gemini fallback

Gemini is called through its image-capable API using inline image data.

The implementation used this model identifier:

`gemini-3.5-flash-lite`

**Important:** the handoff does not establish that this exact Gemini model identifier has been verified as available for your API key. This must be tested. If the API rejects the model, inspect the error and replace it with a currently supported vision-capable Gemini model.

## C. Passing vision context into the planner

The AI engine was modified in:

`app/src/main/java/com/example/service/TheoAiEngine.kt`

The planning method was extended to accept an optional visual description:

`planScreenAction(..., visualContext: String? = null)`

The planner can now use the screen description alongside accessibility information when choosing the next step.

However, the planning instructions preserve an important boundary: the model should only select actions that match available accessibility node identifiers. The screenshot description does not currently authorize arbitrary coordinate taps.

## D. Orchestration in the ViewModel

The relevant orchestration changes are in:

`app/src/main/java/com/example/viewmodel/TheoViewModel.kt`

The intended behavior is:

1. Obtain a screen snapshot.
2. Wait for a stable screen fingerprint using the existing retry logic.
3. Attempt screenshot capture when supported.
4. Call the vision analyzer using the configured Groq key and available Gemini key.
5. Clear the local screenshot byte array using `.fill(0)` after analysis.
6. Log provider information and description length rather than the screenshot itself.
7. Pass the visual description to the AI planner.
8. Continue using the existing guarded automation path.

If screenshot capture or vision analysis fails, the intended fallback is to continue using the accessibility tree.

Clearing the byte array is a sensible attempt to reduce the lifetime of the screenshot data in memory, but it is not a guarantee that every internal copy has been erased. The image is also transmitted to the selected cloud provider, so the privacy implications of cloud processing still apply.

---

# 11. API-key settings and connection testing

You wanted the application to use APIs that you supply rather than silently depending on an undisclosed built-in AI service.

The latest work added a dedicated Groq vision credential field and a way to test the Groq key.

## Settings UI

The settings screen was modified in:

`app/src/main/java/com/example/ui/screens/SettingsScreen.kt`

A new section was added with the heading:

**Groq Qwen Vision Credentials**

The intended controls include:

- A field for the Groq API key.
- A button to test the Groq API connection.
- A status message indicating the test result.
- A privacy warning.

The screen's settings callback was expanded to pass five values instead of four, with the Groq key added to the existing settings flow.

The latest recorded settings commit is:

`edd0b051519d7acec99f923a17b5bdfa6e7a0c39`

## MainActivity integration

`app/src/main/java/com/example/MainActivity.kt` was updated to wire the five-argument settings save callback and the Groq test action.

This matters because a field can appear in the UI without actually being saved or used by the application. The callback wiring was added to address that concern, but persistence still needs to be tested on a device.

## Groq connection test

A method was added to `TheoAiEngine.kt`:

`testGroqConnection(apiKey)`

It uses:

`GET https://api.groq.com/openai/v1/models`

The purpose is to check whether the API key can successfully access the Groq models endpoint.

### What this test proves

If successful, it provides evidence that the key and endpoint work for that request.

### What it does not prove

It does not prove that:

- The selected Qwen vision model is available for your account.
- Screenshot requests are accepted.
- The image format is correct.
- The vision response can be parsed.
- Theo can navigate an application.
- The Android screenshot capture works on your phone.

Those require separate tests.

## Gemini configuration

The existing settings and AI integration already had a Gemini key configuration path, referred to as **Gemini Cloud Credentials**.

The new vision path was designed to reuse Gemini keys from the existing custom API-key field, using the existing key-parsing logic.

This must be checked in the current code: the fact that the project has Gemini credentials does not automatically prove the vision analyzer reads the correct saved value.

## API-key storage and privacy

The Groq key is stored using Android SharedPreferences, under the key:

`groq_api_key`

The project already used SharedPreferences for related settings, including Gemini credentials.

This is local settings persistence, **not a claim that the keys are encrypted at rest**.

The previous discussion also established that SharedPreferences data generally persists through ordinary application updates but is cleared when the app is uninstalled. A future secure export/restore or backup design could be considered, but no such feature should be claimed as implemented.

Do not paste either API key into this chat, source code, commit messages, screenshots, or logs.

---

# 12. Build and compilation history

This is one of the most important sections for whoever continues the work.

The latest code had encountered actual CI failures. Those failures were investigated and two specific source issues were fixed, but the most recent build was still in progress in the last recorded status.

## Initial compilation issues

The workflow logs for run `38765104787` showed failures around:

- `ScreenVisionAnalyzer.kt`, involving the closing parentheses for the Gemini JSON request.
- `SettingsScreen.kt`, where some settings callbacks still used four arguments even though the callback signature had been expanded to five.

These were concrete compile errors, not hypothetical concerns.

## Fixes made

### Gemini JSON structure

The closing parentheses around this section in `ScreenVisionAnalyzer.kt` were corrected:

`.put("data", base64))))`

The issue involved closing the nested JSON array/object structure properly.

The analyzer fix was committed as:

`a92ea19298fa5aff915f53ca32f7e33cff95b2ee`

### Settings callback mismatch

The remaining calls to:

`onSaveSettings(selectedModel, selectedIntensity, apiKeyInput, termuxHostInput)`

were updated to include the fifth argument:

`groqApiKeyInput`

The latest recorded settings commit after this fix was:

`edd0b051519d7acec99f923a17b5bdfa6e7a0c39`

## Last recorded GitHub Actions status

The most recent recorded workflow run was:

- **GitHub Actions run:** `38765370421`
- **Workflow:** Compile debug APK
- **URL:** https://github.com/Kentaakakashi/Theo-AI-mobile/actions/runs/38765370421
- **Recorded head SHA:** `edd0b051519d7acec99f923a17b5bdfa6e7a0c39`
- **Job ID:** `116527056648`
- **Last recorded status:** In progress.

The SDK setup steps had passed; Android SDK installation was still running. Gradle compilation and tests had not yet been confirmed.

Other runs that were recorded as in progress at that time included:

- `38765307224`, associated with SHA `3fc8fb...`
- `38765289611`, associated with SHA `0440c66...`

Older runs `38765122057` and `38765104787` had failed before the latest fixes.

### Current conclusion

The source fixes were committed, but the handoff does not establish that the latest build passed.

**Do not treat the build as green until the latest workflow for the current branch head has finished successfully.**

The current branch may have advanced beyond the recorded SHA, so the next developer should first check the latest branch commit and workflow run rather than assume `edd0b...` is still the head.

---

# 13. Important code paths to inspect next

These are the highest-priority files for continuing development.

1. **`ScreenVisionAnalyzer.kt`** — Provider requests, image encoding, response parsing, fallback behavior, and error handling.
2. **`TheoAccessibilityService.kt`** — Screenshot API compatibility, callback lifecycle, bitmap conversion, and accessibility interaction.
3. **`TheoViewModel.kt`** — API-key persistence, vision invocation, screenshot-buffer cleanup, loading retries, and automation orchestration.
4. **`TheoAiEngine.kt`** — Planner prompts, visual context, connection test, and the rules limiting permitted actions.
5. **`SettingsScreen.kt` and `MainActivity.kt`** — Settings callbacks, credential entry, saved state, connection-test UI, and navigation wiring.
6. **`SCREEN_AUTOMATION.md` and `ScreenAutomationTest.kt`** — Documentation and regression coverage for the automation behavior.

### Specific implementation risks to verify

The handoff identified several areas that need actual source and build inspection:

- The Android `takeScreenshot` callback implementation, including the callback types and imports.
- The lifecycle of `ScreenshotResult`, `hardwareBuffer`, and the generated bitmap.
- Whether the hardware buffer is closed at a safe point.
- Whether the bitmap is copied to software memory before JPEG compression, if required.
- Whether the relevant diagnostic logger supports the methods and arguments used by the new code.
- Whether the existing Gemini key parser is visible and usable from the new analyzer path.
- Whether the planner's optional `visualContext` parameter is passed through every intended call site.
- Whether all settings save callbacks were updated.
- Whether the new Compose imports and icons are valid.
- Whether the Groq and Gemini HTTP response parsers handle errors, missing content, and malformed responses safely.

These are **inspection points**, not assertions that each one is currently broken.

---

# 14. Security, privacy, and trust boundaries

Because Theo can read screens and operate apps, the safety rules must be enforced in the implementation rather than relying only on the AI prompt.

## Screenshot privacy

The new design aims to:

- Capture screenshots in memory.
- Avoid writing them to persistent storage.
- Avoid logging screenshot content.
- Send the image only when the vision-analysis flow is invoked.
- Clear the local byte array after analysis.

However, when cloud vision is used, the screenshot is sent to the selected provider. Screens may contain private messages, personal details, notifications, or other sensitive information.

The user should be able to understand when screen data is being processed by a cloud service.

## Screen content is untrusted input

A screenshot can contain text that looks like an instruction to the AI. For example, an app might display a malicious message telling the assistant to ignore its rules or reveal information.

Theo must treat visible screen content as data to analyze, not as an authority that can override the user's instruction or the application's safety rules.

The new vision prompts were designed with this distinction in mind.

## Action safety

Keep the following safeguards:

- Validate the target application/package.
- Restrict the number of steps.
- Ask for confirmation before sensitive actions.
- Ask when a recipient or target is ambiguous.
- Never send messages or perform other consequential actions merely because a screen suggests doing so.
- Verify the result before reporting success.

## API credentials

Never:

- Commit API keys.
- Include keys in diagnostic logs.
- Send keys to an unrelated provider.
- Print full keys in the UI.
- Ask the user to disclose keys in chat.

A connection test should report a useful status without exposing the credential.

---

# 15. Current capabilities versus unfinished work

This distinction should guide all future updates.

| Capability | Handoff status |
|---|---|
| Native Android app using Kotlin and Jetpack Compose | Established |
| Solar-core visual direction | Implemented in earlier commits |
| App launching and action dispatch | Implemented in earlier commits |
| Accessibility-based screen automation | Existing implementation |
| Risk-based confirmation and bounded action plans | Existing implementation |
| Loading-state retry behavior | Existing implementation |
| Groq key settings field | Added in the latest work |
| Groq API-key connection test | Added; needs successful build and runtime verification |
| Screenshot capture on Android 11+ | Implemented in source; device behavior not verified here |
| Groq Qwen vision request | Implemented in source; live request not verified here |
| Gemini vision fallback | Implemented in source; model availability and runtime behavior not verified here |
| Visual description passed to planner | Implemented in source; end-to-end behavior not verified here |
| Screenshot bytes cleared after analysis | Cleanup step added; not a guarantee that all copies are erased |
| Arbitrary visual-coordinate tapping | **Not implemented in the recorded baseline** |
| Successful full end-to-end screen-automation test on a real phone | **Not established** |
| Latest GitHub Actions build passing | **Not established** |
| All desktop J.A.R.V.I.S. capabilities ported | **Not implemented as a complete set** |
| Exact JARVIS-style cloud voice | Not established; native Android TTS preference exists |

The wording in the table is deliberate: adding source code is different from proving the feature works on the target device.

---

# 16. How you should test Theo right now

After checking the latest build, installing the resulting APK, and saving both API keys, the first tests should isolate individual parts of the system.

## Test 1 — Screen understanding only

Open another app and say:

> Look at my current screen and describe what app is open, what I can see, and which buttons or controls I can interact with. Don't tap anything yet.

This checks whether Theo can:

- Capture the screen.
- Send it to a vision provider.
- Receive a useful description.
- Combine the description with accessible controls.
- Avoid performing an unintended action.

If the result describes only accessibility labels and misses obvious visual content, check whether screenshot capture and the cloud request actually ran.

## Test 2 — Open an app and wait

Say:

> Open Discord. Wait until it finishes loading, then tell me what is displayed. Don't open any chats yet.

This tests:

- App resolution and launching.
- Accessibility service availability.
- Loading retries.
- Stable-screen detection.
- Final-state reporting.

A launch alone is not enough. Check whether Theo waits for the interface to become usable.

## Test 3 — Navigate to a specific conversation

Say:

> In Discord, find the chat with [your friend's name] and open it. If you see multiple matches or can't identify the right chat, ask me instead of guessing. Don't send any messages.

This tests:

- Visible text and control identification.
- App navigation.
- Ambiguity handling.
- The connection between visual context and accessibility node selection.
- Verification of the final destination.

Use a recipient that is unambiguous, and do not authorize message sending as part of this initial test.

## Test 4 — Loading-state handling

Say:

> Open YouTube and wait for the home screen to finish loading. If you see a loading screen or spinner, wait rather than treating it as the finished screen.

This tests whether the system distinguishes a temporary loading state from the completed screen.

## How to interpret failures

- **Theo cannot describe the screen:** investigate screenshot capture, API configuration, request errors, and response parsing.
- **Theo describes the screen correctly but cannot tap a control:** investigate accessibility nodes and the action executor.
- **Theo opens the app but claims it is ready too soon:** investigate loading detection and snapshot retries.
- **Groq testing succeeds but vision fails:** the key may be valid while the selected model, image request, or response parsing is wrong.
- **Groq fails but Gemini works:** investigate Groq credentials, endpoint access, model availability, and fallback selection.
- **Both vision providers fail:** confirm whether the app correctly falls back to accessibility-only operation.
- **Theo reports success without evidence:** fix the verification and reporting logic before adding more capabilities.

---

# 17. Recommended continuation plan

The following order minimizes the risk of building additional features on top of an unverified foundation.

## Phase 1 — Establish a known-good build

1. Open the repository and check the current branch head.
2. Inspect the latest GitHub Actions run for that exact commit.
3. Read the full logs if the build failed.
4. Fix compilation or test errors directly in the branch.
5. Re-run the workflow.
6. Do not merge the draft pull request.

The first objective is to establish a clean build, not to add more features.

## Phase 2 — Verify API settings

1. Install the current build.
2. Enter the Groq key in Theo's Groq settings field.
3. Use the Groq connection-test button.
4. Confirm that the result reflects the actual HTTP response.
5. Confirm that the key survives restarting the app.
6. Configure Gemini through the existing Gemini settings.
7. Verify that both values are being read by the intended code paths.

Do not use a successful connection test as proof of vision functionality.

## Phase 3 — Test screenshot capture

1. Enable the accessibility service.
2. Open a separate application.
3. Run the screen-description command.
4. Confirm screenshot capture succeeds on Android 11+.
5. Confirm unsupported or failed capture falls back safely.
6. Check for crashes, callback lifecycle errors, or resource leaks.
7. Ensure screenshots and API keys are not being written to logs.

## Phase 4 — Verify each cloud provider

Test Groq and Gemini separately where possible.

Confirm:

- The expected model is available.
- The request format is accepted.
- Image encoding is correct.
- Response parsing works.
- HTTP errors are surfaced accurately.
- Timeouts and network failures are handled.
- Fallback occurs when the primary provider fails.

The Gemini model identifier should be corrected if the service reports it is unavailable.

## Phase 5 — Test the complete automation loop

Start with a non-destructive app-launching task. Then test navigation to a known screen.

Verify the entire sequence:

**Instruction → observation → planning → action → new observation → verification → truthful report.**

Do not move directly to high-impact actions such as sending messages, deleting content, or changing account settings.

## Phase 6 — Improve vision-guided interaction

The current design does not support arbitrary coordinate taps based only on a screenshot.

If you want Theo to handle controls that are visible but missing from the accessibility tree, this needs a deliberate next development phase.

Possible implementation work includes:

- A structured visual-target representation.
- Coordinate mapping from resized JPEGs back to screen coordinates.
- Device rotation and display-scaling handling.
- Bounds checks and target validation.
- Confidence thresholds and ambiguity handling.
- A clear confirmation policy for consequential actions.
- A safe mechanism for performing coordinate gestures through Android accessibility APIs.
- Re-observation and verification after every action.

The system should never treat an unverified coordinate guessed by a model as automatically safe.

## Phase 7 — Broader JARVIS-style features

Once the core agent is reliable, prioritize additional capabilities such as:

- Improved speech input and output.
- A consistent British voice option.
- Interruptible speech and barge-in behavior.
- Persistent conversational memory.
- Search and web tools.
- Calendar and email integrations.
- A polished floating assistant interface.
- Better diagnostics and automation history.

These should be added incrementally, with permissions and test coverage appropriate to each feature.

---

# 18. Important historical details and commits

For continuity, here are the known identifiers recorded during development.

| Item | Identifier or location |
|---|---|
| Repository | `Kentaakakashi/Theo-AI-mobile` |
| Development branch | `feature/screen-aware-automation` |
| Draft PR | `#1` |
| App-launching commit | `6fe6fefd` |
| Action-dispatch commit | `308e1276` |
| Package-discovery commit | `e846a089` |
| Solar-core UI commit | `6e37406e9d02297864d2ae6f670a55ad739e7d3a` |
| Native TTS preference commit | `11e748aca68f499c056b3197bf35c276de90f3e6` |
| Vision analyzer fix commit | `a92ea19298fa5aff915f53ca32f7e33cff95b2ee` |
| Latest recorded settings fix commit | `edd0b051519d7acec99f923a17b5bdfa6e7a0c39` |
| Earlier failing workflow | `38765104787` |
| Earlier failing workflow | `38765122057` |
| Last recorded in-progress workflow | `38765370421` |
| Last recorded in-progress job | `116527056648` |
| Automation documentation | `SCREEN_AUTOMATION.md` |
| Automation unit tests | `app/src/test/java/com/example/ScreenAutomationTest.kt` |

Short SHAs are included where that was the level of detail retained in the history. Resolve them against GitHub before using them in a script or relying on them as exact commit identifiers.

---

# 19. Final handoff: what the next session needs to know

The most recent work is an attempt to connect three previously separate pieces into a useful screen-aware assistant:

1. **Android automation** provides the ability to launch apps and interact with accessible controls.
2. **Cloud vision** supplies a description of what is visually displayed.
3. **The AI planner** combines that information with the user's instruction to decide what should happen next.

Groq and Gemini credentials are now represented in the intended configuration flow. The Groq connection-test action has been added, screenshot capture has been integrated for supported Android versions, and the vision description is passed into the planner.

The two known compilation issues involving the Gemini JSON structure and the expanded settings callback were fixed in commits. However, the last recorded workflow was still in progress, so the current build status remains unconfirmed in this handoff.

The biggest technical limitation is that visual understanding does not yet equal unrestricted visual control. The current action path still relies on accessibility controls and does not implement general-purpose coordinate tapping based solely on the screenshot.

The immediate priorities are therefore:

1. **Verify and fix the latest build.**
2. **Test Groq and Gemini separately.**
3. **Verify screenshot capture on a real Android device.**
4. **Test screen description, app launching, loading waits, and navigation.**
5. **Confirm every action's outcome before Theo reports success.**
6. **Only then expand into more advanced vision-guided interaction and other JARVIS-inspired features.**

The most important requirement remains unchanged: **Theo must be a real, reliable Android assistant—not a chatbot that merely claims to control the phone.** Every capability should be tested, every failure should be reported honestly, and no new feature should compromise the safety of the existing automation system.

_Handoff compiled from the available project history and recorded implementation notes. Build and device-test statuses are explicitly distinguished from code changes that were merely committed._