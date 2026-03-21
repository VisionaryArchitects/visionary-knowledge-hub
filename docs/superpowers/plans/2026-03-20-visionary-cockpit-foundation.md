# Visionary Cockpit Foundation Implementation Plan

> **For agentic workers:** REQUIRED: Use superpowers:subagent-driven-development (if subagents available) or superpowers:executing-plans to implement this plan. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Build the first working WinUI foundation for Visionary Cockpit inside `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS`, including a real flagship shell, a real home workspace with chat rail + adaptive canvas, and a real operations core that supervises the local stack.

**Architecture:** Start from a clean unpackaged WinUI 3 app scaffold, keep the app WinUI-native, and isolate testable state/supervisor logic into a `.Core` library. Deliver one thin-but-real slice through shell, home workspace, and operations so the app launches as a credible daily-driver foundation instead of a placeholder shell.

**Tech Stack:** WinUI 3, Windows App SDK, C#/.NET, xUnit, unpackaged `dotnet new winui`, MVVM-lite view models, local process/service supervision, OpenClaw/VTS HTTP integration for the foundation slice.

**Scope Boundary:** This plan delivers the flagship **foundation slice**, not the entire final V1 feature envelope from the design spec. It must produce a real native shell, real home workspace, and real operations supervision, but full OpenClaw page-parity expansion and live AI transport stay in follow-on plans.

**Git Safety Contract:** `Visionary_Architects_OS` is nested under the parent git root `D:\DEV_PROJECTS\GitHub`. Every commit step in this plan must:

1. run `git -C D:\DEV_PROJECTS\GitHub status --short -- <pathspecs>`
2. stage with `git -C D:\DEV_PROJECTS\GitHub add -- <pathspecs>`
3. commit with `git -C D:\DEV_PROJECTS\GitHub commit -m "<message>" -- <pathspecs>`

Do not use an unscoped `git commit` from the parent repo.

If a task creates a brand new subtree that did not exist before execution, staging that exact new subtree is allowed. Otherwise, stage the exact file list declared in the task instead of directory roots.

---

## Chunk 1: Environment Audit and Repo Bootstrap

### Task 1: Audit WinUI readiness without host installs

**Files:**
- Modify: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\README.md`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\docs\foundation\WINUI_READINESS.md`

- [ ] **Step 1: Run a full non-mutating WinUI readiness audit**

Run:

```powershell
Get-ComputerInfo | Select-Object WindowsProductName, WindowsVersion, OsBuildNumber
Get-ItemProperty -Path 'HKLM:\SOFTWARE\Microsoft\Windows\CurrentVersion\AppModelUnlock' -ErrorAction SilentlyContinue
dotnet --list-sdks
dotnet new list winui
Get-Command msbuild -ErrorAction SilentlyContinue
& "${env:ProgramFiles(x86)}\Microsoft Visual Studio\Installer\vswhere.exe" -latest -format json
Get-ChildItem 'C:\Program Files (x86)\Windows Kits\10\bin' -ErrorAction SilentlyContinue
```

Expected:

- supported Windows 11 build is present
- Visual Studio is installed and discoverable
- Windows SDK 10.0.19041.0 or later is present
- `msbuild` is available for XAML compilation
- `dotnet --list-sdks` shows at least one supported SDK
- `dotnet new list winui` lists the WinUI template when the CLI template is actually installed
- Developer Mode state is recorded as `present`, `missing`, or `uncertain`

- [ ] **Step 2: Fail closed if any required WinUI prerequisite is missing or uncertain**

If any of these are missing or uncertain:

- Visual Studio with WinUI-capable desktop tooling
- Windows SDK 10.0.19041.0+
- `msbuild`
- a supported .NET SDK
- the `winui` template

then:

- mark execution as **BLOCKED**
- do **not** proceed to Task 2
- capture the exact failing output in `WINUI_READINESS.md`
- classify the audit under `present`, `missing`, `uncertain`, and `recommended optional tools`
- stop implementation until the missing prerequisite problem is resolved by an approved path
- do **not** run host-level `winget configure` or other host installs unless BALLER explicitly overrides the host-package rule

- [ ] **Step 3: Record readiness findings**

Write `docs/foundation/WINUI_READINESS.md` with:

- Windows build and Developer Mode state
- Visual Studio detection result
- Windows SDK detection result
- `msbuild` detection result
- current SDKs present
- whether the WinUI template is available
- whether the environment satisfies Microsoft WinUI 3 prerequisites as a whole
- whether implementation can proceed without host installation
- whether execution is blocked right now
- explicit note: do **not** run host-level `winget configure` unless BALLER explicitly overrides the host-package rule

- [ ] **Step 4: Update repo README to define the new app target**

Add a short section to `README.md` that states:

- this repo will host the WinUI flagship app
- source code will live under `src/`
- planning docs live in the knowledge hub repo
- git operations for this folder must be run from `D:\DEV_PROJECTS\GitHub`, because `Visionary_Architects_OS` is nested inside the parent git repo

- [ ] **Step 5: Commit**

```bash
git -C D:\DEV_PROJECTS\GitHub status --short -- Visionary_Architects_OS/README.md Visionary_Architects_OS/docs/foundation/WINUI_READINESS.md
git -C D:\DEV_PROJECTS\GitHub add -- Visionary_Architects_OS/README.md Visionary_Architects_OS/docs/foundation/WINUI_READINESS.md
git -C D:\DEV_PROJECTS\GitHub commit -m "docs: define Visionary Cockpit foundation target" -- Visionary_Architects_OS/README.md Visionary_Architects_OS/docs/foundation/WINUI_READINESS.md
```

### Task 2: Scaffold the app and testable core projects

**Files:**
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\global.json`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\docs\foundation\TARGET_FRAMEWORK.md`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\VisionaryCockpit.csproj`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit.Core\VisionaryCockpit.Core.csproj`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\tests\VisionaryCockpit.Core.Tests\VisionaryCockpit.Core.Tests.csproj`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\scripts\Launch-VisionaryCockpit.ps1`

- [ ] **Step 1: Scaffold the WinUI app through the canonical unpackaged path**

Precondition:

- Task 1 confirmed the full WinUI prerequisite set is present
- if not, this task remains blocked and is not allowed to guess around the missing template or missing Visual Studio/MSBuild state

Run:

```powershell
cd D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS
dotnet new winui -o src/VisionaryCockpit
```

Expected:

- `src/VisionaryCockpit/VisionaryCockpit.csproj` exists

- [ ] **Step 2: Convert the scaffold to the chosen unpackaged model**

Modify `src/VisionaryCockpit/VisionaryCockpit.csproj` so the packaging contract is explicit:

- set `<WindowsPackageType>None</WindowsPackageType>`
- preserve the WinUI template's generated target framework and Windows SDK floor
- record that target framework as the app source of truth for the rest of the plan

- [ ] **Step 3: Persist the SDK and TFM contract at the repo level**

Create:

- `global.json` pinned to the exact SDK version used during Task 1/Task 2
- `docs/foundation/TARGET_FRAMEWORK.md` recording:
  - scaffolded app `TargetFramework`
  - Windows SDK floor
  - chosen unpackaged contract
  - matching `.Core` / test TFM family

- [ ] **Step 4: Create the core class library and tests with a pinned TFM family**

Use the WinUI app target framework major/minor as the shared contract:

- if the app targets `net10.0-windows10.0.19041.0`, create the core/test projects with `net10.0`
- if the scaffolded app targets a different major/minor, stop and substitute that exact major/minor in the commands below before continuing
- do **not** use bare `dotnet new classlib` or `dotnet new xunit` defaults on this machine

Run:

```powershell
cd D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS
dotnet new classlib -f net10.0 -n VisionaryCockpit.Core -o src/VisionaryCockpit.Core
dotnet new xunit -f net10.0 -n VisionaryCockpit.Core.Tests -o tests/VisionaryCockpit.Core.Tests
dotnet add tests/VisionaryCockpit.Core.Tests/VisionaryCockpit.Core.Tests.csproj reference src/VisionaryCockpit.Core/VisionaryCockpit.Core.csproj
dotnet add src/VisionaryCockpit/VisionaryCockpit.csproj reference src/VisionaryCockpit.Core/VisionaryCockpit.Core.csproj
```

Expected:

- the core library and test project both restore successfully
- the core library/test TFM family matches the WinUI app's major/minor runtime family
- the WinUI app references the core library

- [ ] **Step 5: Build the fresh scaffold**

```powershell
dotnet build D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\VisionaryCockpit.csproj -c Debug -p:Platform=x64
dotnet test D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\tests\VisionaryCockpit.Core.Tests\VisionaryCockpit.Core.Tests.csproj
```

Expected:

- WinUI app build passes
- test project passes its empty baseline

- [ ] **Step 6: Create the launch helper**

Create `scripts/Launch-VisionaryCockpit.ps1` that:

- uses the fixed build contract `Debug + x64`
- reads the exact `TargetFramework` from `src\VisionaryCockpit\VisionaryCockpit.csproj`
- resolves the executable from the exact unpackaged build output `src\VisionaryCockpit\bin\x64\Debug\<TargetFramework>\VisionaryCockpit.exe`
- fails if the exact target file is missing or if multiple candidate executables are found for that `TargetFramework`
- optionally removes stale output under that exact target path before verification
- terminates prior `VisionaryCockpit` instances before launching so stale windows cannot fake success
- starts the executable
- polls for up to 15 seconds, refreshing the process object until `MainWindowHandle` is non-zero
- returns `Id`, `ProcessName`, `MainWindowTitle`, `MainWindowHandle`, `Responding`, `StartedAtUtc`, and resolved executable path
- supports an explicit switch to leave the verified instance running for final validation
- fails if `MainWindowHandle` stays `0`, the process exits, or the process is not responding

- [ ] **Step 7: Launch-verify the fresh scaffold**

Run:

```powershell
dotnet build D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\VisionaryCockpit.csproj -c Debug -p:Platform=x64
powershell -ExecutionPolicy Bypass -File D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\scripts\Launch-VisionaryCockpit.ps1
```

Verify:

- a real top-level window appears
- the process remains responsive
- the window title is visible and not crashing on startup

- [ ] **Step 8: Commit**

```bash
git -C D:\DEV_PROJECTS\GitHub status --short -- Visionary_Architects_OS/global.json Visionary_Architects_OS/docs/foundation/TARGET_FRAMEWORK.md Visionary_Architects_OS/src/VisionaryCockpit Visionary_Architects_OS/src/VisionaryCockpit.Core Visionary_Architects_OS/tests/VisionaryCockpit.Core.Tests Visionary_Architects_OS/scripts/Launch-VisionaryCockpit.ps1
git -C D:\DEV_PROJECTS\GitHub add -- Visionary_Architects_OS/global.json Visionary_Architects_OS/docs/foundation/TARGET_FRAMEWORK.md Visionary_Architects_OS/src/VisionaryCockpit Visionary_Architects_OS/src/VisionaryCockpit.Core Visionary_Architects_OS/tests/VisionaryCockpit.Core.Tests Visionary_Architects_OS/scripts/Launch-VisionaryCockpit.ps1
git -C D:\DEV_PROJECTS\GitHub commit -m "feat: scaffold Visionary Cockpit foundation" -- Visionary_Architects_OS/global.json Visionary_Architects_OS/docs/foundation/TARGET_FRAMEWORK.md Visionary_Architects_OS/src/VisionaryCockpit Visionary_Architects_OS/src/VisionaryCockpit.Core Visionary_Architects_OS/tests/VisionaryCockpit.Core.Tests Visionary_Architects_OS/scripts/Launch-VisionaryCockpit.ps1
```

## Chunk 2: Shell Foundation

### Task 3: Create the shell structure and shared resources

**Files:**
- Modify: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\App.xaml`
- Modify: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\App.xaml.cs`
- Modify: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\MainWindow.xaml`
- Modify: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\MainWindow.xaml.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Styles\Colors.xaml`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Styles\Typography.xaml`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Styles\Shell.xaml`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\ViewModels\ShellViewModel.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit.Core\Navigation\AppDestination.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\tests\VisionaryCockpit.Core.Tests\Navigation\AppDestinationTests.cs`

- [ ] **Step 1: Write the failing navigation/core tests**

Create `tests/VisionaryCockpit.Core.Tests/Navigation/AppDestinationTests.cs`:

```csharp
using VisionaryCockpit.Core.Navigation;

namespace VisionaryCockpit.Core.Tests.Navigation;

public class AppDestinationTests
{
    [Fact]
    public void FounderCockpitDestinations_AreStableAndOrdered()
    {
        var actual = AppDestination.All.Select(x => x.Key).ToArray();
        Assert.Equal(
            new[] { "home", "operations", "projects", "agents", "knowledge", "system" },
            actual);
    }
}
```

- [ ] **Step 2: Run the test to verify failure**

Run:

```powershell
dotnet test D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\tests\VisionaryCockpit.Core.Tests\VisionaryCockpit.Core.Tests.csproj --filter AppDestinationTests
```

Expected:

- FAIL because `AppDestination` does not exist yet

- [ ] **Step 3: Implement the destination model and shell view model**

Create:

- `src/VisionaryCockpit.Core/Navigation/AppDestination.cs`
- `src/VisionaryCockpit/ViewModels/ShellViewModel.cs`

Implementation requirements:

- static ordered destination list
- icon glyph/label metadata
- selected destination state
- app title and status-strip properties

- [ ] **Step 4: Wire global resources and shell styles**

Implement:

- `Styles/Colors.xaml` for theme-aware brushes
- `Styles/Typography.xaml` for heading/body hierarchy
- `Styles/Shell.xaml` for `NavigationView`, command bar, badges, and status strip styling
- merge dictionaries in `App.xaml`

- [ ] **Step 5: Replace the template `MainWindow` with the flagship shell**

Implement in `MainWindow.xaml`:

- `NavigationView` with six top-level destinations
- top command bar
- environment/status strip
- content host frame
- Mica-backed shell treatment

Implement in `MainWindow.xaml.cs`:

- shell initialization
- title bar setup
- navigation change handling

- [ ] **Step 6: Verify**

Run:

```powershell
dotnet test D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\tests\VisionaryCockpit.Core.Tests\VisionaryCockpit.Core.Tests.csproj --filter AppDestinationTests
dotnet build D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\VisionaryCockpit.csproj -c Debug -p:Platform=x64
powershell -ExecutionPolicy Bypass -File D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\scripts\Launch-VisionaryCockpit.ps1
```

Expected:

- test passes
- app builds with the new shell
- the app still launch-verifies through `scripts\Launch-VisionaryCockpit.ps1`

- [ ] **Step 7: Commit**

```bash
git -C D:\DEV_PROJECTS\GitHub status --short -- Visionary_Architects_OS/src/VisionaryCockpit/App.xaml Visionary_Architects_OS/src/VisionaryCockpit/App.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/MainWindow.xaml Visionary_Architects_OS/src/VisionaryCockpit/MainWindow.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Styles/Colors.xaml Visionary_Architects_OS/src/VisionaryCockpit/Styles/Typography.xaml Visionary_Architects_OS/src/VisionaryCockpit/Styles/Shell.xaml Visionary_Architects_OS/src/VisionaryCockpit/ViewModels/ShellViewModel.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Navigation/AppDestination.cs Visionary_Architects_OS/tests/VisionaryCockpit.Core.Tests/Navigation/AppDestinationTests.cs
git -C D:\DEV_PROJECTS\GitHub add -- Visionary_Architects_OS/src/VisionaryCockpit/App.xaml Visionary_Architects_OS/src/VisionaryCockpit/App.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/MainWindow.xaml Visionary_Architects_OS/src/VisionaryCockpit/MainWindow.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Styles/Colors.xaml Visionary_Architects_OS/src/VisionaryCockpit/Styles/Typography.xaml Visionary_Architects_OS/src/VisionaryCockpit/Styles/Shell.xaml Visionary_Architects_OS/src/VisionaryCockpit/ViewModels/ShellViewModel.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Navigation/AppDestination.cs Visionary_Architects_OS/tests/VisionaryCockpit.Core.Tests/Navigation/AppDestinationTests.cs
git -C D:\DEV_PROJECTS\GitHub commit -m "feat: add Visionary Cockpit shell foundation" -- Visionary_Architects_OS/src/VisionaryCockpit/App.xaml Visionary_Architects_OS/src/VisionaryCockpit/App.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/MainWindow.xaml Visionary_Architects_OS/src/VisionaryCockpit/MainWindow.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Styles/Colors.xaml Visionary_Architects_OS/src/VisionaryCockpit/Styles/Typography.xaml Visionary_Architects_OS/src/VisionaryCockpit/Styles/Shell.xaml Visionary_Architects_OS/src/VisionaryCockpit/ViewModels/ShellViewModel.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Navigation/AppDestination.cs Visionary_Architects_OS/tests/VisionaryCockpit.Core.Tests/Navigation/AppDestinationTests.cs
```

### Task 4: Add the module pages as real thin pages, not placeholders

**Files:**
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\HomePage.xaml`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\HomePage.xaml.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\OperationsPage.xaml`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\OperationsPage.xaml.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\ProjectsPage.xaml`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\ProjectsPage.xaml.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\AgentsPage.xaml`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\AgentsPage.xaml.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\KnowledgePage.xaml`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\KnowledgePage.xaml.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\SystemPage.xaml`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\SystemPage.xaml.cs`

- [ ] **Step 1: Create the pages with real page headers and module framing**

Each page must include:

- page title
- short purpose subtitle
- one real functional region, not lorem ipsum

Examples:

- `OperationsPage` gets service status cards
- `ProjectsPage` gets seeded product cards
- `KnowledgePage` gets truth-pack/decision/session entry points

- [ ] **Step 2: Wire navigation routes**

Update `MainWindow.xaml.cs` so each destination navigates to the correct page class.

- [ ] **Step 3: Build and launch-verify**

Run:

```powershell
dotnet build D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\VisionaryCockpit.csproj -c Debug -p:Platform=x64
powershell -ExecutionPolicy Bypass -File D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\scripts\Launch-VisionaryCockpit.ps1
```

Then verify:

- the nav items switch pages
- the shell remains responsive
- no page is a blank placeholder

- [ ] **Step 4: Commit**

```bash
git -C D:\DEV_PROJECTS\GitHub status --short -- Visionary_Architects_OS/src/VisionaryCockpit/Pages/HomePage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/HomePage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/OperationsPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/OperationsPage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/ProjectsPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/ProjectsPage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/AgentsPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/AgentsPage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/KnowledgePage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/KnowledgePage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/SystemPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/SystemPage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/MainWindow.xaml.cs
git -C D:\DEV_PROJECTS\GitHub add -- Visionary_Architects_OS/src/VisionaryCockpit/Pages/HomePage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/HomePage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/OperationsPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/OperationsPage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/ProjectsPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/ProjectsPage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/AgentsPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/AgentsPage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/KnowledgePage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/KnowledgePage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/SystemPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/SystemPage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/MainWindow.xaml.cs
git -C D:\DEV_PROJECTS\GitHub commit -m "feat: add flagship module pages" -- Visionary_Architects_OS/src/VisionaryCockpit/Pages/HomePage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/HomePage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/OperationsPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/OperationsPage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/ProjectsPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/ProjectsPage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/AgentsPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/AgentsPage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/KnowledgePage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/KnowledgePage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/SystemPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/SystemPage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/MainWindow.xaml.cs
```

## Chunk 3: Home Workspace

### Task 5: Create testable home workspace state

**Files:**
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit.Core\Workspace\CanvasMode.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit.Core\Workspace\RecentAction.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit.Core\Workspace\HomeWorkspaceState.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\tests\VisionaryCockpit.Core.Tests\Workspace\HomeWorkspaceStateTests.cs`

- [ ] **Step 1: Write failing tests**

Create tests for:

- default mode is `Operations`
- switching to `Projects` updates current mode
- recent AI actions are appended in order
- recent action stack caps at a fixed length

- [ ] **Step 2: Run the tests to verify failure**

Run:

```powershell
dotnet test D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\tests\VisionaryCockpit.Core.Tests\VisionaryCockpit.Core.Tests.csproj --filter HomeWorkspaceStateTests
```

Expected:

- FAIL because workspace state types do not exist yet

- [ ] **Step 3: Implement the workspace state**

Add:

- canvas mode enum
- recent action record
- home workspace state with deterministic mode switching and capped action history

- [ ] **Step 4: Re-run tests**

Expected:

- PASS

- [ ] **Step 5: Commit**

```bash
git -C D:\DEV_PROJECTS\GitHub status --short -- Visionary_Architects_OS/src/VisionaryCockpit.Core/Workspace/CanvasMode.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Workspace/RecentAction.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Workspace/HomeWorkspaceState.cs Visionary_Architects_OS/tests/VisionaryCockpit.Core.Tests/Workspace/HomeWorkspaceStateTests.cs
git -C D:\DEV_PROJECTS\GitHub add -- Visionary_Architects_OS/src/VisionaryCockpit.Core/Workspace/CanvasMode.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Workspace/RecentAction.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Workspace/HomeWorkspaceState.cs Visionary_Architects_OS/tests/VisionaryCockpit.Core.Tests/Workspace/HomeWorkspaceStateTests.cs
git -C D:\DEV_PROJECTS\GitHub commit -m "feat: add home workspace state model" -- Visionary_Architects_OS/src/VisionaryCockpit.Core/Workspace/CanvasMode.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Workspace/RecentAction.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Workspace/HomeWorkspaceState.cs Visionary_Architects_OS/tests/VisionaryCockpit.Core.Tests/Workspace/HomeWorkspaceStateTests.cs
```

### Task 6: Implement the Home page chat rail and adaptive canvas

**Files:**
- Modify: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\HomePage.xaml`
- Modify: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\HomePage.xaml.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\ViewModels\HomeViewModel.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Services\Persistence\WorkspaceStateStore.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Controls\ChatRail.xaml`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Controls\ChatRail.xaml.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Controls\AdaptiveCanvasHost.xaml`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Controls\AdaptiveCanvasHost.xaml.cs`

- [ ] **Step 1: Implement `HomeViewModel` and workspace persistence**

It should expose:

- current canvas mode
- sample/pinned context items
- recent AI actions
- commands to switch among Operations, Projects, and Knowledge modes

`WorkspaceStateStore` should:

- save the current home workspace mode and recent context on change
- restore the last saved state during app start/home page initialization
- use the unpackaged-safe local file path `%LocalAppData%\VisionaryCockpit\Foundation\workspace-state.json`, not package-identity APIs

- [ ] **Step 2: Build the chat rail control**

`ChatRail.xaml` should include:

- session header
- conversation list region
- quick action chips or buttons
- input composer shell

Do **not** connect to the real assistant transport yet; keep it structurally real but locally seeded.

- [ ] **Step 3: Build the adaptive canvas control**

`AdaptiveCanvasHost.xaml` should show different content regions for:

- operations mode
- projects mode
- knowledge mode

Each mode must have distinct surfaces, not just swapped titles.

- [ ] **Step 4: Compose the two-pane Home page**

Implement:

- left fixed/resizable chat rail
- right adaptive canvas
- recent action strip
- responsive collapse behavior for narrow widths
- restore the last saved workspace mode on load
- persist workspace-mode changes during use

- [ ] **Step 5: Build and launch-verify**

Run:

```powershell
dotnet build D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\VisionaryCockpit.csproj -c Debug -p:Platform=x64
powershell -ExecutionPolicy Bypass -File D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\scripts\Launch-VisionaryCockpit.ps1
```

Verify:

- Home opens by default
- mode switches update the right pane
- layout holds together in wide and narrow window sizes

- [ ] **Step 6: Commit**

```bash
git -C D:\DEV_PROJECTS\GitHub status --short -- Visionary_Architects_OS/src/VisionaryCockpit/Pages/HomePage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/HomePage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/ViewModels/HomeViewModel.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Persistence/WorkspaceStateStore.cs Visionary_Architects_OS/src/VisionaryCockpit/Controls/ChatRail.xaml Visionary_Architects_OS/src/VisionaryCockpit/Controls/ChatRail.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Controls/AdaptiveCanvasHost.xaml Visionary_Architects_OS/src/VisionaryCockpit/Controls/AdaptiveCanvasHost.xaml.cs
git -C D:\DEV_PROJECTS\GitHub add -- Visionary_Architects_OS/src/VisionaryCockpit/Pages/HomePage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/HomePage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/ViewModels/HomeViewModel.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Persistence/WorkspaceStateStore.cs Visionary_Architects_OS/src/VisionaryCockpit/Controls/ChatRail.xaml Visionary_Architects_OS/src/VisionaryCockpit/Controls/ChatRail.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Controls/AdaptiveCanvasHost.xaml Visionary_Architects_OS/src/VisionaryCockpit/Controls/AdaptiveCanvasHost.xaml.cs
git -C D:\DEV_PROJECTS\GitHub commit -m "feat: add home workspace surface" -- Visionary_Architects_OS/src/VisionaryCockpit/Pages/HomePage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/HomePage.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/ViewModels/HomeViewModel.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Persistence/WorkspaceStateStore.cs Visionary_Architects_OS/src/VisionaryCockpit/Controls/ChatRail.xaml Visionary_Architects_OS/src/VisionaryCockpit/Controls/ChatRail.xaml.cs Visionary_Architects_OS/src/VisionaryCockpit/Controls/AdaptiveCanvasHost.xaml Visionary_Architects_OS/src/VisionaryCockpit/Controls/AdaptiveCanvasHost.xaml.cs
```

## Chunk 4: Operations Core

### Task 7: Build the supervisor state model and policy

**Files:**
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit.Core\Supervisor\ServiceKind.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit.Core\Supervisor\ServiceHealthState.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit.Core\Supervisor\ServiceStatusSnapshot.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit.Core\Supervisor\ServiceActionResult.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit.Core\Supervisor\SupervisorPolicy.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\tests\VisionaryCockpit.Core.Tests\Supervisor\SupervisorPolicyTests.cs`

- [ ] **Step 1: Write failing tests**

Cover:

- services classify into healthy/degraded/offline/starting/stale/recovering
- VTS and OpenClaw are critical
- AIKit is optional and must not poison green runtime state
- startup order is Ollama -> VTS -> ngrok -> OpenClaw
- steady-state failures require retry thresholds before flipping to offline
- bounded backoff governs retry cadence after failures

- [ ] **Step 2: Run the tests to verify failure**

Run:

```powershell
dotnet test D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\tests\VisionaryCockpit.Core.Tests\VisionaryCockpit.Core.Tests.csproj --filter SupervisorPolicyTests
```

Expected:

- FAIL because supervisor policy types do not exist

- [ ] **Step 3: Implement the supervisor core**

Add:

- service kind enum
- health state enum
- status snapshot record
- service action result record with command/probe receipts
- policy class for criticality and startup order

Policy requirements:

- poll interval: 5 seconds
- stale threshold: 15 seconds without a successful probe
- offline threshold: 3 consecutive failed probes or 30 seconds without a successful probe, whichever comes first
- recovering state: first successful probe after stale/offline until two consecutive successes land
- starting state: explicit in-flight start/restart action before first success
- retry backoff: 3 seconds, then 6 seconds, then cap at 10 seconds
- green runtime excludes AIKit from failure scoring

- [ ] **Step 4: Re-run tests**

Expected:

- PASS

- [ ] **Step 5: Commit**

```bash
git -C D:\DEV_PROJECTS\GitHub status --short -- Visionary_Architects_OS/src/VisionaryCockpit.Core/Supervisor/ServiceKind.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Supervisor/ServiceHealthState.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Supervisor/ServiceStatusSnapshot.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Supervisor/ServiceActionResult.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Supervisor/SupervisorPolicy.cs Visionary_Architects_OS/tests/VisionaryCockpit.Core.Tests/Supervisor/SupervisorPolicyTests.cs
git -C D:\DEV_PROJECTS\GitHub add -- Visionary_Architects_OS/src/VisionaryCockpit.Core/Supervisor/ServiceKind.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Supervisor/ServiceHealthState.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Supervisor/ServiceStatusSnapshot.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Supervisor/ServiceActionResult.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Supervisor/SupervisorPolicy.cs Visionary_Architects_OS/tests/VisionaryCockpit.Core.Tests/Supervisor/SupervisorPolicyTests.cs
git -C D:\DEV_PROJECTS\GitHub commit -m "feat: add supervisor policy core" -- Visionary_Architects_OS/src/VisionaryCockpit.Core/Supervisor/ServiceKind.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Supervisor/ServiceHealthState.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Supervisor/ServiceStatusSnapshot.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Supervisor/ServiceActionResult.cs Visionary_Architects_OS/src/VisionaryCockpit.Core/Supervisor/SupervisorPolicy.cs Visionary_Architects_OS/tests/VisionaryCockpit.Core.Tests/Supervisor/SupervisorPolicyTests.cs
```

### Task 8: Implement local service adapters and the Operations page

**Files:**
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Services\Supervisor\IServiceAdapter.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Services\Supervisor\OwnedProcessRecord.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Services\Supervisor\VtsServiceAdapter.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Services\Supervisor\OpenClawServiceAdapter.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Services\Supervisor\OllamaServiceAdapter.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Services\Supervisor\NgrokServiceAdapter.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Services\Supervisor\SupervisorService.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\ViewModels\OperationsViewModel.cs`
- Modify: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\OperationsPage.xaml`
- Modify: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\OperationsPage.xaml.cs`

- [ ] **Step 1: Implement the service adapter contract**

`IServiceAdapter` should expose:

- `ServiceKind Kind`
- `Task<ServiceStatusSnapshot> GetStatusAsync(...)`
- `Task<ServiceActionResult> StartAsync(...)`
- `Task<ServiceActionResult> StopAsync(...)`
- `Task<ServiceActionResult> RestartAsync(...)`

`ServiceActionResult` must include:

- actor
- target service
- requested action
- command line or script path actually used
- whether the action used detached process launch
- probe endpoint used for completion verification
- started/completed timestamps
- progress entries
- success/failure result
- outcome summary
- final observed health state
- operator-readable receipt message
- rollback guidance

- [ ] **Step 2: Implement supervisor-safe process launch semantics**

Do **not** use `Start-VisionaryStack.ps1` or `Stop-VisionaryStack.ps1` as the primary adapter implementation path in the app. Those scripts are operator-facing references, and `Stop-VisionaryStack.ps1` is not automation-safe because it waits for a keypress at the end.

Foundation implementation contract:

- `SupervisorService` owns full-stack orchestration directly
- detached foreground commands with `Start-Process` when the underlying command is long-running
- use `-WindowStyle Hidden` for detached background launches
- use explicit working directories
- never treat command dispatch alone as success; every action must finish on probe receipt
- keep the existing scripts as documentation/fallback references only
- every adapter tracks the PID and command identity for the instance it launched via `OwnedProcessRecord`
- default stop/restart paths must act only on the owned PID/identity for that adapter
- if a healthy external/shared instance exists but is not owned by the adapter, surface it as `ExternalInstanceDetected` and do not broad-kill it
- broad `Stop-Process` / port-kill behavior is allowed only as an explicit force action surfaced to the operator, not as the default path

- [ ] **Step 3: Implement the first adapters**

Use the known stack endpoints for reads:

- VTS health: `http://localhost:8082/health`
- OpenClaw health: `http://127.0.0.1:18789/health`
- Ollama tags: `http://localhost:11434/api/tags`
- ngrok inspector: `http://localhost:4040/api/tunnels`

Use these exact action contracts:

- full stack start: orchestrate service-specific starts in canonical order `Ollama -> VTS -> ngrok -> OpenClaw`
- full stack stop: orchestrate service-specific stops for `OpenClaw -> ngrok -> VTS`, surfacing Ollama as intentionally shared and left running in the foundation slice unless the adapter owns the launched PID
- full stack restart: run full stack stop, then full stack start
- OpenClaw start: `Start-Process -FilePath openclaw -ArgumentList 'gateway','start' -WindowStyle Hidden -PassThru`
- OpenClaw restart: `Start-Process -FilePath openclaw -ArgumentList 'gateway','restart' -WindowStyle Hidden -Wait -PassThru`
- OpenClaw stop: `Start-Process -FilePath openclaw -ArgumentList 'gateway','stop' -WindowStyle Hidden -Wait -PassThru`
- VTS start: `cmd.exe /c "set TOOL_PROFILE=extended&& python main.py"` with working directory `D:\DEV_PROJECTS\GitHub\Claude_Opus_ChatGPT_App_Project`
- VTS stop: owned PID first; only if the owned process no longer exists and the operator explicitly chooses force-stop may the adapter use the port-8082 kill snippet
- VTS restart: run VTS stop, then VTS start
- Ollama start: `Start-Process -FilePath ollama -ArgumentList 'serve' -WindowStyle Hidden -PassThru`
- Ollama stop: owned PID first; if Ollama was already running as a shared external runtime, return a no-op/shared receipt instead of killing it
- Ollama restart: run Ollama stop, then Ollama start
- ngrok start: `Start-Process -FilePath ngrok -ArgumentList 'start','--all' -WindowStyle Hidden -PassThru`
- ngrok stop: owned PID first; broad process kill only on explicit force-stop
- ngrok restart: run ngrok stop, then ngrok start

For the foundation slice:

- implement HTTP polling now
- implement stack-level start/stop/restart now
- implement OpenClaw-specific restart/stop now
- defer WebSocket subscriptions and live stream transport to a follow-on plan

Use these action completion contracts:

- start action timeout: 30 seconds total, probe every 3 seconds
- stop action timeout: 20 seconds total, probe every 2 seconds
- restart action timeout: stop timeout + start timeout
- VTS start succeeds when `http://localhost:8082/health` returns success within the timeout
- VTS stop succeeds when `http://localhost:8082/health` stops responding within the timeout
- OpenClaw start succeeds when `http://127.0.0.1:18789/health` returns success within the timeout
- OpenClaw stop succeeds when `http://127.0.0.1:18789/health` stops responding within the timeout
- Ollama start succeeds when `http://localhost:11434/api/tags` returns success within the timeout
- Ollama stop succeeds when `http://localhost:11434/api/tags` stops responding within the timeout
- ngrok start succeeds when `http://localhost:4040/api/tunnels` returns success within the timeout
- ngrok stop succeeds when `http://localhost:4040/api/tunnels` stops responding within the timeout
- full stack start succeeds only when the four required probes pass in truth-pack startup order
- full stack stop mirrors current shared-runtime semantics and is considered successful when VTS, OpenClaw, and ngrok are down; Ollama remaining up must be surfaced as an intentional foundation behavior, not a failure

Steady-state visibility requirements for the Operations page:

- VTS card shows endpoint, response state, last success, and tool-profile/version data from `/health`
- VTS detail region shows live health metadata plus a clearly labeled configured summary for mounted-server/tool-count expectations when runtime discovery is not yet exposed
- OpenClaw card shows endpoint, response state, and last success from `/health`
- OpenClaw detail region shows gateway status plus real thin surfaces for Overview, Sessions, Usage, and Cron backed by currently available live health/action-receipt data and clearly labeled configured metadata where deeper runtime data is deferred
- service log/event area must show real action receipts generated by `ServiceActionResult`, not synthetic sample text
- any configured/not-live values must be labeled `Configured` or `Unavailable`, never presented as live runtime facts

- [ ] **Step 4: Implement `SupervisorService` and `OperationsViewModel`**

Responsibilities:

- poll status
- build the stack summary
- expose action commands
- expose last-refresh and stale-state info
- expose in-flight `starting` and `recovering` states
- stream action receipts into Operations and the shell/chat event surface

- [ ] **Step 5: Replace the thin Operations page with the real operations surface**

`OperationsPage.xaml` must include:

- stack summary cards
- service actions
- recent log/event area
- real thin OpenClaw-derived Overview/Sessions/Usage/Cron surfaces
- VTS/OpenClaw detail regions
- configured vs live labels wherever runtime discovery is not yet implemented

- [ ] **Step 6: Build and launch-verify**

Run:

```powershell
dotnet build D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\VisionaryCockpit.csproj -c Debug -p:Platform=x64
powershell -ExecutionPolicy Bypass -File D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\scripts\Launch-VisionaryCockpit.ps1
```

Verify:

- service states render
- operations page shows live or gracefully degraded data
- actions are visible and guarded correctly
- action receipts appear in the page
- no configured value is mislabeled as live runtime state

- [ ] **Step 7: Commit**

```bash
git -C D:\DEV_PROJECTS\GitHub status --short -- Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/IServiceAdapter.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/OwnedProcessRecord.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/VtsServiceAdapter.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/OpenClawServiceAdapter.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/OllamaServiceAdapter.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/NgrokServiceAdapter.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/SupervisorService.cs Visionary_Architects_OS/src/VisionaryCockpit/ViewModels/OperationsViewModel.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/OperationsPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/OperationsPage.xaml.cs
git -C D:\DEV_PROJECTS\GitHub add -- Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/IServiceAdapter.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/OwnedProcessRecord.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/VtsServiceAdapter.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/OpenClawServiceAdapter.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/OllamaServiceAdapter.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/NgrokServiceAdapter.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/SupervisorService.cs Visionary_Architects_OS/src/VisionaryCockpit/ViewModels/OperationsViewModel.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/OperationsPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/OperationsPage.xaml.cs
git -C D:\DEV_PROJECTS\GitHub commit -m "feat: add operations supervisor core" -- Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/IServiceAdapter.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/OwnedProcessRecord.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/VtsServiceAdapter.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/OpenClawServiceAdapter.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/OllamaServiceAdapter.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/NgrokServiceAdapter.cs Visionary_Architects_OS/src/VisionaryCockpit/Services/Supervisor/SupervisorService.cs Visionary_Architects_OS/src/VisionaryCockpit/ViewModels/OperationsViewModel.cs Visionary_Architects_OS/src/VisionaryCockpit/Pages/OperationsPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/OperationsPage.xaml.cs
```

## Chunk 5: Knowledge and Project Entry Points

### Task 9: Add the first real Knowledge and Projects surfaces

**Files:**
- Modify: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\ProjectsPage.xaml`
- Modify: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Pages\KnowledgePage.xaml`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\Services\KnowledgeHub\KnowledgeHubIndexService.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\ViewModels\ProjectsViewModel.cs`
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\ViewModels\KnowledgeViewModel.cs`

- [ ] **Step 1: Implement seeded projects view model**

Include:

- VTS
- OpenClaw / BabyClaw
- Project Eden
- VKeys
- visionaryarchitects.dev

- [ ] **Step 2: Implement knowledge hub indexing service**

It should read from:

- `D:\DEV_PROJECTS\GitHub\visionary-knowledge-hub\truth-pack`
- `D:\DEV_PROJECTS\GitHub\visionary-knowledge-hub\DECISIONS.md`
- `D:\DEV_PROJECTS\GitHub\visionary-knowledge-hub\ACTIVE_PLANS.md`
- `D:\DEV_PROJECTS\GitHub\visionary-knowledge-hub\SESSION_LOG.md`

- [ ] **Step 3: Render the first real Projects and Knowledge pages**

Projects page:

- flagship product cards
- status/next-step layout

Knowledge page:

- truth-pack index
- decision and plan entry points
- recent session log entry list

- [ ] **Step 4: Build and launch-verify**

Run:

```powershell
dotnet build D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\VisionaryCockpit.csproj -c Debug -p:Platform=x64
powershell -ExecutionPolicy Bypass -File D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\scripts\Launch-VisionaryCockpit.ps1
```

Verify:

- Projects page renders all five seeded products with status/next-step content
- Knowledge page renders truth-pack, decisions, plans, and recent session-log entry points
- missing files degrade cleanly instead of blanking the page

- [ ] **Step 5: Commit**

```bash
git -C D:\DEV_PROJECTS\GitHub status --short -- Visionary_Architects_OS/src/VisionaryCockpit/Pages/ProjectsPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/KnowledgePage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Services/KnowledgeHub/KnowledgeHubIndexService.cs Visionary_Architects_OS/src/VisionaryCockpit/ViewModels/ProjectsViewModel.cs Visionary_Architects_OS/src/VisionaryCockpit/ViewModels/KnowledgeViewModel.cs
git -C D:\DEV_PROJECTS\GitHub add -- Visionary_Architects_OS/src/VisionaryCockpit/Pages/ProjectsPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/KnowledgePage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Services/KnowledgeHub/KnowledgeHubIndexService.cs Visionary_Architects_OS/src/VisionaryCockpit/ViewModels/ProjectsViewModel.cs Visionary_Architects_OS/src/VisionaryCockpit/ViewModels/KnowledgeViewModel.cs
git -C D:\DEV_PROJECTS\GitHub commit -m "feat: add projects and knowledge entry points" -- Visionary_Architects_OS/src/VisionaryCockpit/Pages/ProjectsPage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Pages/KnowledgePage.xaml Visionary_Architects_OS/src/VisionaryCockpit/Services/KnowledgeHub/KnowledgeHubIndexService.cs Visionary_Architects_OS/src/VisionaryCockpit/ViewModels/ProjectsViewModel.cs Visionary_Architects_OS/src/VisionaryCockpit/ViewModels/KnowledgeViewModel.cs
```

## Chunk 6: Final Foundation Verification

### Task 10: Run the flagship foundation verification pass

**Files:**
- Create: `D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\docs\foundation\FOUNDATION_VERIFICATION.md`

- [ ] **Step 1: Run test suite**

```powershell
dotnet test D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\tests\VisionaryCockpit.Core.Tests\VisionaryCockpit.Core.Tests.csproj
```

- [ ] **Step 2: Build and launch through the real local workflow**

```powershell
dotnet build D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\src\VisionaryCockpit\VisionaryCockpit.csproj -c Debug -p:Platform=x64
powershell -ExecutionPolicy Bypass -File D:\DEV_PROJECTS\GitHub\Visionary_Architects_OS\scripts\Launch-VisionaryCockpit.ps1
```

- [ ] **Step 3: Launch and verify**

Verify:

- real top-level window
- shell navigation works
- Home / Operations / Projects / Knowledge all render
- light and dark themes both hold up
- high contrast does not break the shell
- large text/text scaling remains usable
- keyboard navigation reaches the main shell actions and page surfaces
- narrow window mode remains usable
- last active home workspace restores after relaunch
- supervisor retries, stale-state transitions, and startup-order enforcement behave as specified
- module fallback behavior works when one or more local services are offline
- unpackaged workflow is still valid and package-readiness risks are recorded for later

- [ ] **Step 4: Write verification notes**

Create `docs/foundation/FOUNDATION_VERIFICATION.md` with:

- commands run
- pass/fail results
- known issues
- next recommended chunk

- [ ] **Step 5: Commit**

```bash
git -C D:\DEV_PROJECTS\GitHub status --short -- Visionary_Architects_OS/docs/foundation/FOUNDATION_VERIFICATION.md
git -C D:\DEV_PROJECTS\GitHub add -- Visionary_Architects_OS/docs/foundation/FOUNDATION_VERIFICATION.md
git -C D:\DEV_PROJECTS\GitHub commit -m "docs: record foundation verification" -- Visionary_Architects_OS/docs/foundation/FOUNDATION_VERIFICATION.md
```

---

## Follow-on Plans Required After This Plan

This plan intentionally stops after the first real flagship foundation. Additional plans should follow for:

1. **Agents deep management**
2. **System deep configuration and diagnostics**
3. **Home page live AI transport and receipts**
4. **OpenClaw page parity expansion**
5. **Knowledge editing and semantic memory integration**

Plan saved to `docs/superpowers/plans/2026-03-20-visionary-cockpit-foundation.md`.

Current status:

- planning complete
- execution blocked until the WinUI template exists on this machine or BALLER approves a different bootstrap path
