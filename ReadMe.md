# HearThis - Custom Pro Editor Variant
(Standard ReadMe.md is below this)

Branch Profile: Audio Editing Fork (R. Webb Custom)

This repository is a specialized custom fork of the core HearThis audio synchronization utility. It introduces a secure, key-locked Supervisor Editor Mode designed to prevent accidental modifications, deletion, or re-recording of existing audio clips during standard tracking sessions, while permitting full administrative access when explicitly unlocked.

---

## Key Architectural Modifications

### 1. Global State Guard
* File Modified: RecordingToolControl.cs
* Implementation: Added a central static tracking variable to act as the universal safety gate across system components.
* Variable Name: public static bool EditorModeActive = false;

### 2. Hotkey Toggle and Confirmation UI
* File Modified: RecordingToolControl.cs (Inside ProcessCmdKey)
* Behavior: Traps the Ctrl + Shift + E key combination to safely toggle the editing state. 
* Logic Framework:
  * Calculates the exact screen-center coordinates of the parent form wrapper using FindForm().
  * Spawns a clean, focused confirmation dialog box to notify the supervisor.
  * Uses C# reflection to break through the control container layers, find the custom toolbar indicator button, and immediately sync its visibility to match the runtime state.

### 3. Double-Click Safety Trap
* File Modified: RecordingToolControl.cs (Inside OnSliderBlobDoubleClicked)
* Behavior: Places a strict guard clause at the absolute entry point of the native waveform/slider blob double-click pipeline. If EditorModeActive is false, the method cleanly returns and blocks any editing execution before the native clip pipeline can begin.

### 4. Native UI Dashboard Indicator (The Crimson E Tile)
* Files Modified: Shell.Designer.cs and Shell.cs
* Positioning: Configured as a right-aligned ToolStripButton integrated directly into the upper-right menu bar array, nesting perfectly between the Save icon and the Project Folder icon.
* Layout Matrix: [Read and Record Menu] ... [Project Icon] [ E TILE ] [SAVE]
* Dynamic Graphic Rendering Engine:
  * Generates its 32x32 pixel design completely in memory at application runtime, bypassing external file-path dependencies.
  * Fills the canvas with a solid, subdued supervisor crimson tile background (RGB: 165, 45, 45).
  * Draws a distinct 2-pixel silver-grey frame border (RGB: 190, 190, 190) to cleanly blend with the application's native dark-mode theme.
  * Embosses a bold white capital letter E onto the absolute center of the red canvas block, utilizing custom horizontal and vertical baseline text-rendering math to offset row height limits.

---

## Build and Compilation Specifications
* Framework Target: Compiles smoothly using standard .NET development toolchains (Ctrl + Shift + B).
* Expected Runtime Behavior: On startup, the safety lock is fully active and the red dashboard tile is hidden. Pressing Ctrl + Shift + E prompts the confirmation window, resets title baseline padding dynamically, and safely arms or disarms the wave editing gates instantly without requiring a software reboot.

---


## ---- Standard Github HearThis Readme contents

Using HearThis™, communities can record and distribute their translated Scripture for themselves.

# Users

You're in the wrong place. Head over to https://software.sil.org/hearthis .

## Testers

Please see [Tips for Testing Palaso Software](https://docs.google.com/document/d/1dkp0edjJ8iqkrYeXdbQJcz3UicyilLR7GxMRIUAGb1E/edit)

# Developers

## IDE

- Download and install [Visual Studio](https://visualstudio.microsoft.com/downloads/)
- Download and Install the NUnit 3 Test Adapter extension from within Visual Studio

## Building

1. Run build/get-dependencies-windows.sh (e.g. from within Git Bash)
1. Build the solution. This will download and install NuGet dependencies.
1. Assemblies not available on the build server or via nuget can be checked in to the repo in lib/dotnet. When working on the code, the development team needs to decide if any of these assemblies should be replaced with the latest version. The same is true for the myriad nuget packages that HearThis depends on.

## Paratext and ICU DLLs

Note that a Release build cannot be run directly (i.e., without properly installing HearThis) -- at least on a system that has Paratext -- because of the way Paratext attempts to locate the ICU DLLs. If you need to track down a bug that is only reproducible in a Release build, run the Installer and then replace the installed EXE with a locally-built one if necessary.

## Running Tests

1. Ensure NUnit 3 Test Adapter extension is installed (see above)
1. Test -> Run All Tests
The "BreakIntoChunks_SpeedTest" test is CPU dependent and is safe to ignore

## RoadMap / Day-to-day progress

See the [HearThis Trello Board](https://trello.com/b/5ejUB2EF/hearthis)

## Continuous Build System

Each time code is checked in, an automatic build begins on our [TeamCity build server](https://build.palaso.org/project.html?projectId=HearThis&tab=projectOverview), running all the unit tests. This automatic build doesn't publish a new installer, however. That kind of build is launched manually, by pressing a button on the TeamCity page.  This "publish" process builds HearThis, makes an installer, rsyncs it to the distribution server, and writes out a a json file that the downloads page reads so that it can display options to the user.

## Coding Standards

Please avoid making spurious white space changes. HearThis uses tabs, not spaces, for indentation.
