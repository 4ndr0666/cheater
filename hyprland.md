# Hyprland Cheat-Sheet

**Hyprland** is a highly customizable, dynamic tiling Wayland compositor that emphasizes beautiful animations and a GPU-accelerated experience. It's built for those who love a modern, fast, and visually appealing workflow.

---

## 1. Core Concepts

*   **Wayland**: The modern display server protocol that Hyprland uses, offering improved security and performance over X11.
*   **Workspaces (Virtual Desktops)**: Separate areas for organizing your windows. Hyprland uses a dynamic model where workspaces can be assigned to monitors.
*   **Dynamic Tiling**: Windows are automatically arranged in a non-overlapping fashion. You can still float windows.
*   **Window Rules**: Define specific behaviors for applications based on criteria like class or title.
*   **Dispatchers**: Commands used with `hyprctl` or keybindings to perform actions (e.g., move window, switch workspace).

---

## 2. Configuration Files

Hyprland's main configuration file is typically located at:
`~/.config/hypr/hyprland.conf`

This file can `source` other configuration files, allowing for modular organization (e.g., `~/.config/hypr/keybinds.conf`, `~/.config/hypr/windowrules.conf`).

```ini
# ~/.config/hypr/hyprland.conf
# Source other configuration files
source = ~/.config/hypr/env_vars.conf
source = ~/.config/hypr/keybinds.conf
source = ~/.config/hypr/windowrules.conf
source = ~/.config/hypr/monitors.conf

# General settings (example)
general {
    gaps_in = 5
    gaps_out = 10
    border_size = 2
    col.active_border = rgb(89b4fa) rgb(a6e3a1) 45deg
    col.inactive_border = rgb(59697d)
    resize_on_border = false
}

# Add your keybindings, window rules, etc., or source them from separate files.
```

---

## 3. Keybindings

Keybindings are defined using the `bind` or `bindr` (bind repeating) command.
Syntax: `bind = MODIFIER, KEY, DISPATCHER, ARGUMENT`

*   **Common Modifiers**: `SUPER` (Windows/Meta key), `ALT`, `SHIFT`, `CTRL`

### General Window Management

| Modifier | Key      | Action                               | Command in `hyprland.conf`                                     |
| :------- | :------- | :----------------------------------- | :------------------------------------------------------------- |
| `ALT`    | `Q`      | Close active window                  | `bind = ALT, Q, killactive,`                                   |
| `ALT`    | `F`      | Toggle fullscreen                    | `bind = ALT, F, fullscreen, 0`                                 |
| `SUPER`  | `M`      | Toggle maximize (full window, still tiled) | `bind = SUPER, M, maximize, `                                  |
| `ALT`    | `T`      | Toggle float/tiling                  | `bind = ALT, T, togglefloating, `                              |
| `SUPER`  | `N`      | Toggle scratchpad (hide/show window) | `bind = SUPER, N, movetoworkspacesilent, special`              |
| `ALT`    | `G`      | Toggle grouped window                | `bind = ALT, G, togglegroup, `                                 |
| `ALT`    | `Tab`    | Cycle through windows                | `bind = ALT, Tab, cyclenext, `                                 |
| `ALT`    | `P`      | Toggle Picture-in-Picture (PIP)      | `bind = ALT, P, pin, `                                         |
| `SUPER`  | `S`      | Toggle split ratio for tiling        | `bind = SUPER, S, toggleoversplit, `                           |

### Focus & Movement

| Modifier | Key        | Action                   | Command in `hyprland.conf`         |
| :------- | :--------- | :----------------------- | :--------------------------------- |
| `ALT`    | `Left`     | Focus left               | `bind = ALT, Left, movefocus, l`   |
| `ALT`    | `Right`    | Focus right              | `bind = ALT, Right, movefocus, r`  |
| `ALT`    | `Up`       | Focus up                 | `bind = ALT, Up, movefocus, u`     |
| `ALT`    | `Down`     | Focus down               | `bind = ALT, Down, movefocus, d`   |
| `ALT+SHIFT` | `Left`   | Move window left         | `bind = ALT_SHIFT, Left, movewindow, l` |
| `ALT+SHIFT` | `Right`  | Move window right        | `bind = ALT_SHIFT, Right, movewindow, r` |
| `ALT+SHIFT` | `Up`     | Move window up           | `bind = ALT_SHIFT, Up, movewindow, u` |
| `ALT+SHIFT` | `Down`   | Move window down         | `bind = ALT_SHIFT, Down, movewindow, d` |

### Workspace Management

| Modifier | Key        | Action                                 | Command in `hyprland.conf`                                        |
| :------- | :--------- | :------------------------------------- | :---------------------------------------------------------------- |
| `ALT`    | `1` - `10` | Switch to workspace N                  | `bind = ALT, 1, workspace, 1` (and so on for 2-10)                |
| `SUPER+SHIFT` | `1` - `10` | Move active window to workspace N      | `bind = SUPER_SHIFT, 1, movetoworkspace, 1` (and so on for 2-10) |
| `SUPER`  | `mouse_down` | Switch to next workspace               | `bind = SUPER, mouse_down, workspace, e+1`                        |
| `SUPER`  | `mouse_up`   | Switch to previous workspace           | `bind = SUPER, mouse_up, workspace, e-1`                          |

### System & App Launchers

| Modifier | Key       | Action                                 | Command in `hyprland.conf`                           |
| :------- | :-------- | :------------------------------------- | :--------------------------------------------------- |
| `ALT`    | `Return`  | Launch Terminal (Kitty)                | `bind = ALT, Return, exec, kitty`                    |
| `ALT`    | `E`       | Launch Rofi (App Launcher)             | `bind = ALT, E, exec, rofi -show drun`               |
| `SUPER`  | `L`       | Lock screen (requires `swaylock`)      | `bind = SUPER, L, exec, swaylock`                    |
| `SUPER`  | `R`       | Restart Hyprland                       | `bind = SUPER, R, exec, hyprctl reload`              |
| `SUPER+SHIFT` | `E`    | Exit Hyprland                          | `bind = SUPER_SHIFT, E, exec, hyprctl dispatch exit` |

---

## 4. Window Rules

Define behaviors for specific applications or windows.
Syntax: `windowrule = RULE, WINDOW_REGEX`
Or for advanced rules: `windowrule v2 = RULE, WINDOW_REGEX`

*   **`WINDOW_REGEX`**: Can be a window class (from `xprop` or `hyprctl clients`), title, or specific properties.

### Common Window Rules

```ini
# ~/.config/hypr/windowrules.conf

# Floating windows
windowrule = float, Alacritty
windowrule = float, Rofi
windowrule = float, feh
windowrule = float, ^(org.gnome.Calculator)$
windowrule = float, ^(org.kde.kwrite)$

# Specific sizes
windowrule = size 1000 600, Alacritty
windowrule = size 800 500, Rofi

# Specific positions
windowrule = center, Rofi # Center a floating window
windowrule = move 50 50, ^(Gimp)$ # Move Gimp to X=50, Y=50

# Always on top
windowrule = alwaysontop, ^(Rofi)$
windowrule = alwaysontop, ^(Peek)$ # For GIF recorder

# Workspace assignment
windowrule = workspace 2, ^(firefox)$ # Open Firefox on workspace 2
windowrule = workspace 5 silent, ^(Discord)$ # Open Discord on workspace 5 silently (without switching)

# Skip taskbar/dock (e.g., for notification pop-ups)
windowrule = noanim, ^(fcitx)$
windowrule = animation slide, ^(fcitx)$
windowrule = noborder, ^(fcitx)$
windowrule = nofocus, ^(fcitx)$

# Scratchpad window
windowrule = workspace special, ^(scratchpad_terminal)$
windowrule = size 800 600, ^(scratchpad_terminal)$
windowrule = center, ^(scratchpad_terminal)$
windowrule = animation slide top, ^(scratchpad_terminal)$
```
*To identify `WINDOW_REGEX` for an application, use `hyprctl clients` or `xprop` (for XWayland apps).*

---

## 5. Workspaces & Monitors

Hyprland handles workspaces dynamically and ties them to monitors.

### Monitor Configuration

Configure your monitors using the `monitor` command.

```ini
# ~/.config/hypr/monitors.conf

# Example for a single monitor
monitor=DP-1,1920x1080@144,0x0,1

# Example for multiple monitors
monitor=DP-1,2560x1440@144,0x0,1
monitor=HDMI-A-1,1920x1080@60,2560x0,1 # Placed to the right of DP-1

# Disable a monitor
monitor=VIRTUAL1,disable

# Auto-detect primary monitor (Hyprland will try to pick one)
# monitor=eDP-1,preferred,auto,1
```

### Dynamic Monitor Control (DPMS)

Turn monitors on/off using `hyprctl dispatch`.

```bash
# Turn off all monitors
hyprctl dispatch dpms off

# Turn on all monitors
hyprctl dispatch dpms on

# Turn off a specific monitor (e.g., DP-1)
hyprctl dispatch dpms off DP-1
```

### Workspace Persistence

Assign specific workspaces to monitors to have them always open there.

```ini
# Assign workspace 1 to monitor DP-1, and 2 to HDMI-A-1
workspace=1,monitor:DP-1
workspace=2,monitor:HDMI-A-1

# Automatically move specific apps to certain workspaces
# bind = SUPER, 1, movetoworkspace, 1
# windowrule = workspace 1, ^(Alacritty)$
```

---

## 6. Utilities & Debugging

### `hyprctl` (Hyprland Control)

A command-line tool to interact with the running Hyprland instance.

*   **Reload configuration**:
    ```bash
    hyprctl reload
    ```
*   **Get current configuration options**:
    ```bash
    hyprctl getoption general:gaps_in
    ```
*   **List all clients (windows)**:
    ```bash
    hyprctl clients
    ```
*   **List all workspaces**:
    ```bash
    hyprctl workspaces
    ```
*   **List all monitors**:
    ```bash
    hyprctl monitors
    ```
*   **Dispatch commands**: Execute any dispatcher (same as in `bind` commands).
    ```bash
    hyprctl dispatch exit
    hyprctl dispatch fullscreen 0
    hyprctl dispatch movetoworkspace 3
    ```

### `hyprpm` (Hyprland Plugin Manager)

For managing Hyprland plugins.

*   **Install a plugin**:
    ```bash
    hyprpm install <plugin_name>
    ```
*   **Update plugins**:
    ```bash
    hyprpm update
    ```

### Logging

View Hyprland's logs for debugging.

```bash
journalctl -b -g hypr # View Hyprland logs for the current boot
```

---

## 7. Troubleshooting & Tips

*   **XWayland vs. Native Wayland**: Some applications might run via XWayland (e.g., GIMP, older Electron apps). Use `xprop` to inspect their properties, or `hyprctl clients` which lists both.
*   **Scaling Issues**: If applications appear blurry or too small/large, check your monitor scale in `hyprland.conf` (e.g., `monitor=...,...,...,1.5`). Also, ensure your `QT_QPA_PLATFORMTHEME` is set to `qt5ct` or `qt6ct` if using Qt applications.
*   **Environment Variables**: Crucial for Wayland. Set them in `~/.config/hypr/env_vars.conf` (or similar sourced file).
    ```ini
    # ~/.config/hypr/env_vars.conf
    env = XCURSOR_SIZE,24
    env = QT_QPA_PLATFORMTHEME,qt6ct # or qt5ct
    env = GDK_SCALE,1
    env = WLR_NO_HARDWARE_CURSORS,1
    ```
*   **Animations**: Experiment with `animation` settings in `hyprland.conf` for a customized look and feel.
*   **`autostart`**: Use `exec-once` in your config to run commands once at startup (e.g., `exec-once = swaybg -i ~/Pictures/wallpaper.jpg`).
*   **Read the Wiki**: The [official Hyprland Wiki](https://wiki.hyprland.org/) is an excellent resource for detailed information.
