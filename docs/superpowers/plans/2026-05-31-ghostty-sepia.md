# Ghostty Sepia Monochrome Theme Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Add deep sepia (`#2e1a08`) as the single ink color for all Ghostty terminal text, cursor, and ANSI palette entries, with parchment amber (`#c8a96e`) for selections.

**Architecture:** Single addition to the `xdg.configFile."ghostty/config".text` Nix string in `home.nix`. The existing `background-opacity = 0` line stays; all color settings are appended after it.

**Tech Stack:** Nix/home-manager. Apply with `sudo nixos-rebuild switch --flake /home/bugzy/.config/nixos#nixos`.

---

## Files

- **Modify:** `/home/bugzy/.config/nixos/home.nix` — `xdg.configFile."ghostty/config".text` block

---

## Task 1: Add sepia color settings to Ghostty config

**Files:**
- Modify: `/home/bugzy/.config/nixos/home.nix`

- [ ] **Step 1: Replace the Ghostty config block**

  Find this block in `home.nix`:

  ```nix
  xdg.configFile."ghostty/config".text = ''
    background-opacity = 0
  '';
  ```

  Replace it with:

  ```nix
  xdg.configFile."ghostty/config".text = ''
    background-opacity = 0
    foreground = #2e1a08
    cursor-color = #2e1a08
    selection-background = #c8a96e
    selection-foreground = #2e1a08
    palette = 0=#2e1a08
    palette = 1=#2e1a08
    palette = 2=#2e1a08
    palette = 3=#2e1a08
    palette = 4=#2e1a08
    palette = 5=#2e1a08
    palette = 6=#2e1a08
    palette = 7=#2e1a08
    palette = 8=#2e1a08
    palette = 9=#2e1a08
    palette = 10=#2e1a08
    palette = 11=#2e1a08
    palette = 12=#2e1a08
    palette = 13=#2e1a08
    palette = 14=#2e1a08
    palette = 15=#2e1a08
  '';
  ```

- [ ] **Step 2: Commit**

  ```bash
  cd /home/bugzy/.config/nixos
  git add home.nix
  git commit -m "feat: apply sepia monochrome color theme to ghostty"
  ```

- [ ] **Step 3: Apply and verify**

  ```bash
  sudo nixos-rebuild switch --flake /home/bugzy/.config/nixos#nixos
  ```

  Expected: open a new Ghostty window — all text (prompt, output, errors) renders in deep sepia `#2e1a08`. Select some text and verify the selection highlights in warm amber `#c8a96e`.
