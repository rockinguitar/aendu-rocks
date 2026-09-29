<p align="center">
    <a href="https://www.getzola.org/">
        <img src="https://img.shields.io/badge/powered_by-Zola-brightgreen?style=flat-square&labelColor=202b2d&color=087e96" alt="Built with Zola"></a>
    <a href="https://github.com/welpo/tabi">
        <img src="https://img.shields.io/badge/theme-tabi-0?style=flat-square&labelColor=202b2d&color=087e96" alt="tabi theme"></a>
    <a href="https://welpo.github.io/tabi/blog/mastering-tabi-settings/">
        <img src="https://img.shields.io/badge/docs-here-0?style=flat-square&labelColor=202b2d&color=087e96" alt="Documentation"></a>
</p>

# aendu.rocks 🌍

This repository contains the source code for [aendu.rocks](https://aendu.rocks) — a personal blog and photo journal by André Wittwer.

## 🚀 Stack

The site is built using the [Zola](https://www.getzola.org/) static site generator and the beautiful [Tabi theme](https://github.com/welpo/tabi), with a few custom tweaks. It's published to the web via [Cloudflare Pages](https://pages.cloudflare.com/) and the source is version-controlled here on GitHub.


## 🛠️ Local Development

The toolchain is managed with [mise](https://mise.jdx.dev/), which pins the
Zola version used locally and in CI. Install [mise](https://mise.jdx.dev/) and
run the tasks it defines:

```bash
mise run start     # serve the site at http://127.0.0.1:1111
mise run build     # build into ./public
mise run verify    # zola check — links, frontmatter, and build errors
```

`mise run start` is the normal way to preview the site. For other Zola
subcommands, go through mise so the pinned version is used:

```bash
mise exec -- zola serve --help
```

## 📄 License

© 2025 André Wittwer ✧ All rights reserved. Contact for permissions.