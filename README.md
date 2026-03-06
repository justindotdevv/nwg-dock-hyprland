# nwg-dock-hyprland (fork)

> This is a fork of [nwg-piotr/nwg-dock-hyprland](https://github.com/nwg-piotr/nwg-dock-hyprland) with additional features and quality-of-life improvements. All credit for the original project goes to [Piotr Miller](https://github.com/nwg-piotr) and the [nwg-shell](https://nwg-piotr.github.io/nwg-shell) contributors.

Configurable (w/ command line arguments and css) dock, written in Go, aimed exclusively at the [Hyprland](https://github.com/hyprwm/Hyprland) Wayland compositor. It features pinned buttons, client buttons and the launcher button. The latter by default starts [nwg-drawer](https://github.com/nwg-piotr/nwg-drawer).

![2023-04-22-021230_hypr_screenshot](https://user-images.githubusercontent.com/20579136/233751336-b5c6abdd-72f7-43c7-b34d-e2f64248eb86.png)

![image](https://user-images.githubusercontent.com/20579136/233751391-97f8f685-55ae-4078-badf-b8c3d7c41ab4.png)

## Fork Additions

The following features are **not available** in the upstream project and are unique to this fork:

### `-hi` — Hide Indicators

Hides the active application state indicator dots that appear below (or beside) dock icons. Useful for a cleaner, minimal dock appearance.

```text
nwg-dock-hyprland -d -hi
```

### `-hdd` — Configurable Hide Delay

Controls how long (in milliseconds) the dock waits before hiding after the cursor leaves. The upstream project has this hardcoded to 1000ms. This flag lets you tune it to your preference.

```text
# Hide after 500ms instead of the default 1000ms
nwg-dock-hyprland -d -hdd 500

# Hide immediately
nwg-dock-hyprland -d -hdd 0
```

### Low-Priority Build

The Makefile uses `nice -n 19` during compilation so that builds don't hog CPU resources — helpful on lower-powered machines or when building alongside other tasks.

## Installation

### Requirements

- `go` >= 1.20 (just to build)
- `gtk3`
- `gtk-layer-shell`
- [nwg-drawer](https://github.com/nwg-piotr/nwg-drawer): optional. You may use another launcher (see `-c` flag), or none at all. The launcher button won't show up if no launcher is configured.

### Building from Source

```bash
git clone https://github.com/justindotdevv/nwg-dock-hyprland.git
cd nwg-dock-hyprland
make get       # Install Go dependencies (may take a while the first time)
make build     # Compile the binary
sudo make install
```

To uninstall:

```bash
sudo make uninstall
```

## Usage

Either start the dock permanently in `hyprland.conf`:

```text
exec-once = nwg-dock-hyprland [arguments]
```

or assign the command to a key binding. Running the command again kills the existing program instance, so you can use the same key to open and close the dock.

### Running Residently

Start the dock with `-d` or `-r` (preferably in autostart) to keep it running in the background.

#### `-d` for autohiDe

```text
exec-once = nwg-dock-hyprland -d
```

Move the mouse pointer to the expected dock location for the dock to appear. It will hide after the cursor leaves (delay configurable with `-hdd`). Invisible hotspots are created on all outputs unless you specify one with `-o`.

#### `-r` for just Resident

```text
exec-once = nwg-dock-hyprland -r
```

No hotspot is created. To show/hide the dock, bind `exec nwg-dock-hyprland` to a key. Re-execution toggles visibility. You can also use signals:

```text
pkill -SIGRTMIN+1 nwg-dock-hyprland  # Toggle visibility
pkill -SIGRTMIN+2 nwg-dock-hyprland  # Show
pkill -SIGRTMIN+3 nwg-dock-hyprland  # Hide
```

### All Flags

```text
$ nwg-dock-hyprland -h
Usage of nwg-dock-hyprland:
  -a string
    	Alignment in full width/height: "start", "center" or "end" (default "center")
  -c string
    	Command assigned to the launcher button (default "nwg-drawer")
  -d	auto-hiDe: show dock when hotspot hovered, close when left or a button clicked
  -debug
    	turn on debug messages
  -f	take Full screen width/height
  -g string
    	quote-delimited, space-separated class list to iGnore in the dock
  -hd int
    	Hotspot Delay [ms]; the smaller, the faster mouse pointer needs to enter hotspot for the dock to appear; set 0 to disable (default 20)
  -hdd int
    	Hide Delay [ms]: delay before the dock hides after cursor leaves (default 1000)
  -hi	hide Indicators: don't show active application state indicator dots
  -hl string
    	Hotspot Layer "overlay" or "top" (default "overlay")
  -i int
    	Icon size (default 48)
  -ico string
    	alternative name or path for the launcher ICOn
  -iw string
    	Ignore the running applications on these Workspaces based on the workspace's name or id, e.g. "special,10"
  -l string
    	Layer "overlay", "top" or "bottom" (default "overlay")
  -lp string
    	Launcher button position, 'start' or 'end' (default "end")
  -m	allow Multiple instances of the dock (skip lock file check)
  -mb int
    	Margin Bottom
  -ml int
    	Margin Left
  -mr int
    	Margin Right
  -mt int
    	Margin Top
  -nolauncher
    	don't show the launcher button
  -o string
    	name of Output to display the dock on
  -p string
    	Position: "bottom", "top" "left" or "right" (default "bottom")
  -r	Leave the program resident, but w/o hotspot
  -s string
    	Styling: css file name (default "style.css")
  -v	display Version information
  -w int
    	number of Workspaces you use (default 10)
  -x	set eXclusive zone: move other windows aside; overrides the "-l" argument
```

## Styling

Edit `~/.config/nwg-dock-hyprland/style.css` to your taste.

## Troubleshooting

### An application icon is not displayed

The dock identifies apps by their class name (check with `hyprctl clients`). It looks for an icon matching the class name, then falls back to searching for a `.desktop` file.

If an app has no icon in the dock:

1. Check the app class name: `hyprctl clients`
2. Find the app's `.desktop` file
3. Copy it to `~/.local/share/applications/` and rename to `<class_name>.desktop`

If the `.desktop` file contains a proper `Icon=` definition, it should work.

## Documentation

- [nwg-shell project](https://nwg-piotr.github.io/nwg-shell) — The parent project and documentation hub
- [Upstream repository](https://github.com/nwg-piotr/nwg-dock-hyprland) — Original nwg-dock-hyprland project
- [Hyprland Wiki](https://wiki.hyprland.org/) — Hyprland compositor documentation
- [Video walkthrough](https://youtu.be/16KX3vnbNcg?si=POGOVwYxPXDIrwrT) by Stephan Raabe on the "My Linux For Work" YT channel

## Credits

This is a fork of [nwg-dock-hyprland](https://github.com/nwg-piotr/nwg-dock-hyprland) by [Piotr Miller (nwg-piotr)](https://github.com/nwg-piotr), released under the [MIT License](LICENSE).

### Libraries

- [gotk4](https://github.com/diamondburned/gotk4) by [diamondburned](https://github.com/diamondburned) — [GNU AGPL v3.0](https://github.com/diamondburned/gotk4/blob/4/LICENSE.md)
- [go-singleinstance](https://github.com/allan-simon/go-singleinstance) by Allan Simon
