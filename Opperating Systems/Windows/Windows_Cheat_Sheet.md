# Windows Cheat Sheet
> [!Tip]- Github Web: Outline view
> If you view this on guithub an outline can be displayed via clicking on the list icon while viewing this file (top right, next to edit button)

--------------------------------------------
## Windows Optimisation

#### Tweak Tools:
- [Windows Tweak Tool by CTT](https://www.youtube.com/watch?v=tPRv-ATUBe4)
  -  iwr -useb https://christitus.com/win | iex
- [Power toys by Microsoft](https://github.com/microsoft/PowerToys)
- Tiling window manager: [komorebi](https://github.com/LGUG2Z/komorebi) or [glazewm](https://github.com/glzr-io/glazewm)

#### Enable Ultimate power mode:
-  ```powercfg -duplicatescheme e9a42b02-d5df-448d-aa00-03f14749eb61```

#### Resurces
- [Ultimate Windows 11 Gaming Performance Optimization Guide](https://www.youtube.com/watch?v=4o-SZSxygzY)
- [Oh My Posh and the Windows Terminal](https://www.hanselman.com/blog/my-ultimate-powershell-prompt-with-oh-my-posh-and-the-windows-terminal)

--------------------------------------------
## Fix Windows problems

DISM.exe [/Online | /Image] /Cleanup-Image /Restorehealth

sfc /scannow <- Do not use when you have UXT or custom icons installed

## Windows Taskbar to Top Settings
[Guide](https://appuals.com/change-taskbar-location-windows-11/)
1) regedit
2) ```HKEY_CURRENT_USER\Software\Microsoft\Windows\CurrentVersion\Explorer\StuckRects3```
2.1) Zeile 2 Spalte 5 (2-er Hexval.) ```03``` -> ```01``` // Set taskbar to Top für main display
   
----------------------------------------------------------------

## Usefull  Windows Shortcuts
- [Win] + [E] -> Open windows explorer
- [Win] + [R] -> "Run" comamnd (e.g. run `regedit.msc` for opening registry)
- [Win] + [Shift] + [Esc] -> Open Task Manager
- [Win] + [Shift] + [S] -> Screenshot Tool
- [Win] + [.] -> Special Text Symbols
- [SHIFT] + `<Windows restart>` -> Directly Boot into windows recovery

----------------------------------------------------------------
## Windows Paths & Commands

Shortcut                                   | Description
-------------------------------------------|---------------------------
```%USERPROFILE%                        ```| Current User home direct.
```%APPDATA%                            ```| App Configs etc.
```cd /D [Drive Letter]                 ```| Change current Disk Drive
```start [name] [exec] [args]           ```| Starts executables
```winget [command] [args]              ```| [Windows package manager](#Windows package manager)
```setx path "%path%"                   ```| !DANGEROUS! Refreshes path
```"TXT TXT" or TXT^ TXT                ```| Escape Spaces in e.g. cmd
```runas /user:$(hostname)\USER COMMAND ```| run COMMAND as USER

## Windows cmd
| Command             | Description                                                                                         |
|:--------------------|:----------------------------------------------------------------------------------------------------|
|```cd PATH        ```| Change path to given path (if pat is on different drive, it is need to also change the drive after) |
|``` DRIVE_LETTER: ```| Change to DRIVE_LETTER as root|
|``` dir           ```| List directory |
|``` SET foo=bar   ```| Variable foo mit parameter bar |

### Start Programms using Start command
START ["title"] [/D path] [/I] [/MIN] [/MAX] [/SEPARATE | /SHARED]
         [/LOW | /NORMAL | /HIGH | /REALTIME | /ABOVENORMAL | /BELOWNORMAL]
         [/NODE <NUMA node>] [/AFFINITY <hex affinity mask>] [/WAIT] [/B]
         [command/program] [parameters]

| Parameter   | Description |
|:------------|:----------------------------------------------------------------------------------------------------|
| "title"     | Title to display in the window's title bar. |
| path	      | Starting directory. |
| /B	        | Start an application without creating a new window. The application has ^C handling ignored. Unless the application enables ^C processing, ^Break is the only way to interrupt the application. |
| /I	        | The new environment will be the original environment passed to the cmd.exe and not the current environment. |
| /MIN	      | Start window minimized. |
| /MAX	      | Start window maximized. |
| /SEPARATE   |	Start 16-bit Windows program in separate memory space. |
| /SHARED	    | Start 16-bit Windows program in shared memory space. |
| /LOW	      | Start application in the IDLE priority class. |
| /NORMAL	    | Start application in the NORMAL priority class. |
| /HIGH	      |Start application in the HIGH priority class. |
| /REALTIME   |	Start application in the REALTIME priority class. |
| /ABOVENORMAL|	Start application in the ABOVENORMAL priority class. |
| /BELOWNORMAL|	Start application in the BELOWNORMAL priority class. |
| /NODE	      | Specifies the preferred NUMA (Non-Uniform Memory Architecture) node as a decimal integer. |
| /AFFINITY	  | Specifies the processor affinity mask as a hexadecimal number. The process is restricted to running on these processors. The affinity mask is interpreted differently when /AFFINITY and /NODE are combined. Specify the affinity mask as if the NUMA node's processor mask is right shifted to begin at bit zero. The process is restricted to running on those processors in common between the specified affinity mask and the NUMA node. If no processors are in common, the process is restricted to running on the specified NUMA node. |
| /WAIT	      | Start the application and wait for it to terminate. |
| command/program |	If it's an internal cmd command or a batch file, then the command processor is run with the /K switch to cmd.exe. The /K switch keeps the window open after the command is run. If it's not an internal cmd command or batch file, the command is a program that runs either as a windowed application or a console application. |
| parameters	| These are the parameters passed to the command/program. |

### Windows package manager
> [Winget](https://learn.microsoft.com/en-us/windows/package-manager/winget/) is the Windows 10+ terminal package manager delivered by windows and can be used in `powershell` to instll/remove/update packages similar to linux package managers.

```powershell
winget search XY
winget install XY --accept-package-agreements --accept-source-agreements
```

```powershell
 winget update --all -h --disable-interactivity --accept-package-agreements --accept-source-agreements
```

```powershell
 winget uninstall XY --purge --interactive --all-versions
```
