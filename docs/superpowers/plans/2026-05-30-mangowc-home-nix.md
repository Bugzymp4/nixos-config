# MangoWC home.nix Migration Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Migrate `/home/bugzy/.config/nixos/home.nix` from Hyprland to MangoWC, with matching keybindings, waybar with ext/workspaces, and the same dark monochrome aesthetic.

**Architecture:** Three edits to a single file (`home.nix`): disable the Hyprland window manager block, add a `xdg.configFile."mango/config.conf"` block with the full MangoWC config, and update the waybar workspace module from `hyprland/workspaces` to `ext/workspaces`. MangoWC is already enabled system-wide in `configuration.nix` and greetd already launches `mango`.

**Tech Stack:** NixOS flakes, home-manager (as NixOS module), MangoWC 0.12.8, Waybar, Kitty, swaybg, rofi

**Rebuild command:** `cd /home/bugzy/.config/nixos && sudo nixos-rebuild switch --flake .#nixos`
**Dry-run command:** `cd /home/bugzy/.config/nixos && sudo nixos-rebuild build --flake .#nixos`

---

## File Map

| File | Change |
|------|--------|
| `home.nix` | Modify — three sections changed (hyprland block, waybar, new xdg.configFile block) |

---

### Task 1: Disable the Hyprland block

**Files:**
- Modify: `/home/bugzy/.config/nixos/home.nix:278`

- [ ] **Step 1: Open home.nix and add `enable = false` to the hyprland block**

The hyprland block starts at line 278. Add `enable = false;` as the first line inside the block:

```nix
  wayland.windowManager.hyprland = {
    enable = false;
    configType = "hyprlang";
    extraConfig = ''
      ...
    '';
  };
```

- [ ] **Step 2: Dry-run build to confirm the Nix syntax is valid**

```bash
cd /home/bugzy/.config/nixos && sudo nixos-rebuild build --flake .#nixos
```

Expected: build succeeds (or fails only on unrelated issues). If it fails with a Nix eval error, check the `enable = false;` line is inside the outer `{` of the hyprland block and has a semicolon.

- [ ] **Step 3: Commit**

```bash
cd /home/bugzy/.config/nixos
git add home.nix
git commit -m "feat: disable hyprland block in home.nix"
```

---

### Task 2: Add the MangoWC xdg.configFile block

**Files:**
- Modify: `/home/bugzy/.config/nixos/home.nix` — add new section after the hyprland block (around line 444, before the closing `}`)

- [ ] **Step 1: Add the xdg.configFile block to home.nix**

Insert the following block immediately before the final closing `}` of home.nix (after the hyprland block closes at `};`):

```nix
  # ── MangoWC ───────────────────────────────────────────────────────────────
  xdg.configFile."mango/config.conf".text = ''
    monitor=,preferred,auto,auto

    exec-once=waybar
    exec-once=swaybg -i /home/bugzy/Pictures/wallpapers/nixos.png -m fill

    env=XCURSOR_THEME,Bibata-Modern-Classic
    env=XCURSOR_SIZE,24

    general {
        gaps_in=5
        gaps_out=20
        border_width=2
        active_border_color=#e8e8e8
        inactive_border_color=#2a2a2a
    }

    decoration {
        rounding=0
        blur {
            enabled=true
            size=3
            passes=1
        }
        shadow {
            enabled=true
            range=4
            color=#1a1a1a
        }
    }

    animations {
        enabled=true
    }

    input {
        kb_layout=us
        follow_mouse=1
        sensitivity=0
        touchpad {
            natural_scroll=false
        }
    }

    $mainMod=SUPER

    bind=$mainMod,q,spawn,kitty
    bind=$mainMod,c,killactive
    bind=$mainMod,m,quit
    bind=$mainMod,e,spawn,dolphin
    bind=$mainMod,v,togglefloating
    bind=$mainMod,d,spawn,rofi -show drun
    bind=$mainMod,f,fullscreen

    bind=$mainMod,left,movefocus,l
    bind=$mainMod,right,movefocus,r
    bind=$mainMod,up,movefocus,u
    bind=$mainMod,down,movefocus,d

    bind=$mainMod SHIFT,t,setlayout,master
    bind=$mainMod SHIFT,g,setlayout,grid
    bind=$mainMod SHIFT,m,setlayout,monocle
    bind=$mainMod SHIFT,d,setlayout,deck

    bind=$mainMod,1,workspace,1
    bind=$mainMod,2,workspace,2
    bind=$mainMod,3,workspace,3
    bind=$mainMod,4,workspace,4
    bind=$mainMod,5,workspace,5
    bind=$mainMod,6,workspace,6
    bind=$mainMod,7,workspace,7
    bind=$mainMod,8,workspace,8
    bind=$mainMod,9,workspace,9
    bind=$mainMod,0,workspace,10

    bind=$mainMod SHIFT,1,movetoworkspace,1
    bind=$mainMod SHIFT,2,movetoworkspace,2
    bind=$mainMod SHIFT,3,movetoworkspace,3
    bind=$mainMod SHIFT,4,movetoworkspace,4
    bind=$mainMod SHIFT,5,movetoworkspace,5
    bind=$mainMod SHIFT,6,movetoworkspace,6
    bind=$mainMod SHIFT,7,movetoworkspace,7
    bind=$mainMod SHIFT,8,movetoworkspace,8
    bind=$mainMod SHIFT,9,movetoworkspace,9
    bind=$mainMod SHIFT,0,movetoworkspace,10

    bind=$mainMod,s,togglespecialworkspace,magic
    bind=$mainMod SHIFT,s,movetoworkspace,special:magic

    bind=$mainMod,mouse_down,workspace,e+1
    bind=$mainMod,mouse_up,workspace,e-1

    mousebind=$mainMod,btn_left,moveresize,curmove
    mousebind=$mainMod,btn_right,moveresize,curresize

    bindel=,XF86AudioRaiseVolume,exec,wpctl set-volume -l 1 @DEFAULT_AUDIO_SINK@ 5%+
    bindel=,XF86AudioLowerVolume,exec,wpctl set-volume @DEFAULT_AUDIO_SINK@ 5%-
    bindel=,XF86AudioMute,exec,wpctl set-mute @DEFAULT_AUDIO_SINK@ toggle
    bindel=,XF86AudioMicMute,exec,wpctl set-mute @DEFAULT_AUDIO_SOURCE@ toggle
    bindel=,XF86MonBrightnessUp,exec,brightnessctl -e4 -n2 set 5%+
    bindel=,XF86MonBrightnessDown,exec,brightnessctl -e4 -n2 set 5%-

    bindl=,XF86AudioNext,exec,playerctl next
    bindl=,XF86AudioPause,exec,playerctl play-pause
    bindl=,XF86AudioPlay,exec,playerctl play-pause
    bindl=,XF86AudioPrev,exec,playerctl previous

    bind=,Print,exec,mkdir -p ~/Pictures/screenshots && grim ~/Pictures/screenshots/$(date +%Y%m%d_%H%M%S).png
    bind=$mainMod,Print,exec,mkdir -p ~/Pictures/screenshots && grim -g "$(slurp)" ~/Pictures/screenshots/$(date +%Y%m%d_%H%M%S).png
  '';
```

> **Note on Nix strings:** `$mainMod` (no curly braces) is not Nix interpolation — it passes through literally as-is. Same for `$(date ...)` and `$(slurp)` — these use `$(` not `${` so Nix ignores them.

> **Note on `bindel`/`bindl`:** These are MangoWC bind flags (repeatable and locked respectively, same as Hyprland). If after switching you find media/brightness keys don't repeat, change `bindel` → `bind` for those lines.

- [ ] **Step 2: Dry-run build**

```bash
cd /home/bugzy/.config/nixos && sudo nixos-rebuild build --flake .#nixos
```

Expected: build succeeds. If it fails with a Nix eval error mentioning `xdg`, check that the block is inside the top-level `{` of `home.nix` (not inside another block) and that the closing `'';` is present.

- [ ] **Step 3: Commit**

```bash
cd /home/bugzy/.config/nixos
git add home.nix
git commit -m "feat: add MangoWC xdg.configFile config to home.nix"
```

---

### Task 3: Update waybar workspace module

**Files:**
- Modify: `/home/bugzy/.config/nixos/home.nix:130-137` (waybar modules-left and workspace module block)

- [ ] **Step 1: Replace `hyprland/workspaces` with `ext/workspaces` in two places**

Change `modules-left` (line 130):
```nix
      modules-left = [ "ext/workspaces" ];
```

Replace the entire `"hyprland/workspaces"` module config block (lines 134–137):
```nix
      "ext/workspaces" = {
        format = "{name}";
        on-click = "activate";
        sort-by-number = true;
      };
```

The CSS in `programs.waybar.style` uses `#workspaces` and `#workspaces button` — these selectors work for both `hyprland/workspaces` and `ext/workspaces` so no CSS changes are needed.

- [ ] **Step 2: Dry-run build**

```bash
cd /home/bugzy/.config/nixos && sudo nixos-rebuild build --flake .#nixos
```

Expected: build succeeds.

- [ ] **Step 3: Commit**

```bash
cd /home/bugzy/.config/nixos
git add home.nix
git commit -m "feat: switch waybar workspaces from hyprland to ext/workspaces"
```

---

### Task 4: Apply and verify

- [ ] **Step 1: Apply the full config**

```bash
cd /home/bugzy/.config/nixos && sudo nixos-rebuild switch --flake .#nixos
```

Expected: switch completes without errors. Home Manager generation is created and activated.

- [ ] **Step 2: Verify the mango config file was written**

```bash
cat ~/.config/mango/config.conf | head -20
```

Expected: shows the monitor, exec-once, and env lines at the top.

- [ ] **Step 3: Log out and log back in**

MangoWC is already the greetd default session (`command = "mango"` in configuration.nix). Log out and the session should start MangoWC.

- [ ] **Step 4: Verify waybar and wallpaper appear**

After login, confirm:
- Waybar is visible at the top with workspaces, clock, and system stats
- Wallpaper (nixos.png) is displayed via swaybg

- [ ] **Step 5: Verify core keybindings**

| Test | Expected |
|------|----------|
| SUPER+Q | Kitty opens |
| SUPER+D | Rofi launcher appears |
| SUPER+C | Focused window closes |
| SUPER+F | Window goes fullscreen |
| SUPER+1 through 0 | Workspace switches and waybar updates |
| SUPER+SHIFT+T | Layout switches to master-stack |
| SUPER+SHIFT+G | Layout switches to grid |
| SUPER+SHIFT+M | Layout switches to monocle |
| Volume keys | System volume changes |
| Brightness keys | Screen brightness changes |
| Print | Screenshot saved to ~/Pictures/screenshots/ |

- [ ] **Step 6: If `bindel`/`bindl` don't work for media keys**

Open `~/.config/mango/config.conf` directly (or edit `home.nix` and rebuild) and change:
```
bindel=,XF86Audio...  →  bind=,XF86Audio...
bindl=,XF86Audio...   →  bind=,XF86Audio...
```

Then rebuild: `sudo nixos-rebuild switch --flake .#nixos`
