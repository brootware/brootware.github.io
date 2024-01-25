---
layout: post
title: "Handy Sysadmin Cheatbook \U0001F4DC"
categories:
- General-tech
- Misc
tags:
- General-tech
- Powershell
- Linux
- Bash
- Cheat Sheet
date: 2023-07-29 11:08 +0800
---
This is a quick blog entry cheatsheet for system administrators to refer to quickly in their day to day work. I will be adding in more categories to the cheatbook in the future.

> For most of the long linux commands, I'd suggest using aliases that can be setup together with [dotfiles](https://github.com/brootware/dotfiles)
{: .prompt-tip }

## Sysadmin Cheatsheet

<!-- width="700" height="900" frameborder="0" scrolling="no" -->
<!-- <iframe width="700" height="900" src="https://docs.google.com/spreadsheets/d/e/2PACX-1vSiSgD6qKf3_YvW30kzh8F0TqxlrKnj-NHO60CM_Oj8fq8etO66BDEyWM329ztpO1biYcHnp1zSCg_C/pubhtml?widget=true&amp;headers=false"></iframe> -->

<iframe class="airtable-embed" src="https://airtable.com/embed/app1rMiiWF5uDu0DD/shrRZc1wJr8NU2XU2?backgroundColor=greenDusty" frameborder="0" onmousewheel="" width="100%" height="533" style="background: transparent; border: 1px solid #ccc;"></iframe>

## Neovim Cheatsheet

    <Leader> - (Spacebar)
        Open sidebar: 1
        Move a file: m
        Delete a file: d
        Toggle a terminal: ft

    File Navigation
        Top of file: gg
        Bottom of file: G
        Start of line: 0
        End of line: $
        Fast forward: e
        Fast Backward: Option <-

    Find and Replace
        Find a file: <leader>ff
        Search across codebase: <leader>sg
        Find and replace (local): :%s/toReplace/replace/g

    Splitting Windows
        Split horizontally: <leader>-
        Split vertically: <leader>|
        Navigate windows: ctrl + (h/j/k/l)

    LSP (Language Server Protocol)
        Go to definitions: gd
        Autocomplete
        Hover documentation: K
        Smart replace: <leader>cre
        Format: <leader>cff
        Code actions: <leader>cag
        Remove unused imports: <leader>cR
        Organize imports: <leader>co

    Other Operations
        Switch buffers
        Line numbers
            Disable/enable: <leader>ul
            Go to line number: linenumber (j/k)
        Open terminals: <leader>ft
        Explore
            Add a file: %
            Add a folder: d
        Undo, redo, delete, visual mode
        Find and reach
