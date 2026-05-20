# Collapse All Folders in Apple Notes

## Intro
macOS Tahoe has a bug where Apple Notes expands every folder and subfolder in the sidebar on every launch. Apple hasn't fixed it as of macOS 26.5 (May 2026). Some of the Apple Community threads about the bug:
- [Apple Notes expands all folders on launch](https://discussions.apple.com/thread/256216467)
- [Notes App Folders Always Expanded on macOS Tahoe 26.0.1](https://discussions.apple.com/thread/256165172)
- [macOS 26: On launch, Apple Notes opens every single folder](https://discussions.apple.com/thread/256154409)

I have not been able to find a ready-to-use solution. So I made this Apple Shortcut, that uses AppleScript to collapse all folders back in one click. This shortcut works both with iCloud notes and On My Mac notes.

**Note:** This shortcut only works on macOS. It relies on AppleScript and System Events, which are not available on iOS or iPadOS.

## Preview
Placeholder.

## How It Works
The AppleScript in the shortcut uses System Events to simulate **Option ⌥ + Left Arrow ◀** keyboard shortcut in the Apple Notes sidebar to collapse folder and all its subfolders and other keyboard shortcuts for navigation within the folders list.

The algorithm is simple:
1. Focus on the Notes window.
2. Focus on the sidebar.
3. **Option ⌥ + Up Arrow ▲** - jumps to the top of the folders list.
4. **Down Arrow ▼** - skips past a set number of system/smart folders.
5. **Option ⌥ + Left Arrow ◀** - collapses each folder and all its subfolders recursively.
6. **Down Arrow ▼** - moves to the next folder.
7. **Option ⌥ + Up Arrow ▲** - returns to the top of the folders list when done.

## How to Install

#### Important
1. Before running, go to **"System Settings > Privacy & Security > Accessibility"** and enable **"Shortcuts"**. The script simulates keyboard input, so macOS requires this permissions.
2. The Notes app must be running, and the sidebar must be visible when you run the Shortcut. If it's hidden, toggle it with **"Option ⌥ + Command ⌘ + S"** keyboard shortcut or through the menu **"View > Show Folders"**.

#### Option 1 - Apple Shortcuts File
1. Download the [`Collapse Notes Folders.shortcut`](https://github.com/alex-mikhailov-design/apple-notes-collapse-all-folders/raw/main/Collapse%20All%20Folders%20in%20Notes.shortcut) file.
2. Double-click the file. The Shortcuts app will open with an "Add Shortcut" prompt.
3. Click "Add Shortcut".
4. Open Shortcuts app, find the shortcut, and adjust the numbers in the Text block, see [Shortcut Configuration](#shortcut-configuration) below.
5. Run the shortcut from the Shortcuts app, or assign it a keyboard shortcut.

#### Option 2 - Manually
1. Open the Shortcuts app on your Mac.
2. Create a new shortcut.
4. Add a **Text** block and type `2, 10` or your own values, see [Shortcut Configuration](#shortcut-configuration) below.
5. Add a **Run AppleScript** action and paste the code from the [AppleScript](#applescript) section below.
6. Save the shortcut.


#### Tip: Add as a Desktop Widget
For quick one-click access, you can add the shortcut as a widget on your macOS desktop:
1. Right-click anywhere on your desktop.
2. Click "Edit Widgets".
3. In the widget gallery, search for "Shortcuts" in the search bar or scroll down to find it in the list.
4. Find the Shortcuts widget and drag it onto your desktop (the small size works best for a single shortcut).
5. Once placed on the desktop, right-click the widget and choose "Edit "Shortcuts"".
6. Select "Collapse All Folders in Notes" from the list.
7. Click "Done".

You can now run the shortcut directly from your desktop by clicking the widget.

## Shortcut Configuration

The Shortcut has a **Text** block at the top with two comma-separated numbers, e.g. `2, 10`.

The **first number** in the Text block is a total number of non-collapsible folders to skip (2 by default), starting from the top most “All Notes” or “All iCloud” folder. In case you have smart folders or any other system folders on top of the list.

The **second number** in the Text block is a total number of folders you want to collapse (10 by default). Smart folders and folders with no sub-folders still count towards the total number.

Screenshot placeholder

## AppleScript
```applescript
on run {input, parameters}
	
	-- Parse comma-separated text input
	set AppleScript's text item delimiters to ","
	set inputValues to text items of (input as text)
	set skipFoldersCount to (item 1 of inputValues) as integer
	set collapseFoldersCount to (item 2 of inputValues) as integer
	
	-- Focus on Notes app
	tell application "System Events"
		tell process "Notes"
			set frontmost to true
			delay 0.3
			
			-- Focus the sidebar folder outline
			try
				set theOutline to outline 1 of scroll area 1 of group 1 of splitter group 1 of window 1
				set focused of theOutline to true
			on error
				try
					set theOutline to outline 1 of scroll area 1 of splitter group 1 of window 1
					set focused of theOutline to true
				end try
			end try
			delay 0.3
			
			-- Jump to top of the list
			key code 126 using {option down}
			delay 0.2
			
			-- Skip a set number of folders
			repeat skipFoldersCount times
				key code 125
				delay 0.03
			end repeat
			
			-- Collapse a set number of folders
			repeat collapseFoldersCount times
				key code 123 using {option down}
				delay 0.05
				key code 125
				delay 0.05
			end repeat
			
			-- Jump back to top of the list
			key code 126 using {option down}
			delay 0.2
			
		end tell
	end tell
	
end run
```

## License
MIT

## Disclaimer
This script is provided as-is, without warranty of any kind. The sidebar UI element path was tested on macOS Tahoe 26.5 and may break if Apple changes the Notes app structure in a future update.
