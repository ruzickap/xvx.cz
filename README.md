# Personal pages

[![Build Status](https://github.com/ruzickap/xvx.cz/workflows/gh-pages-build/badge.svg)](https://github.com/ruzickap/xvx.cz)

[https://xvx.cz](https://xvx.cz)

## Project Description

This repository hosts the code for the personal landing page
[xvx.cz](https://xvx.cz).
The landing page serves as a central hub for Petr Ruzicka's and Bozena
Ruzickova's various websites and blogs.

## Technologies Used

The website is built using the following technologies:

* **Hugo:** A fast and flexible static site generator.
* **Bootstrap:** A popular CSS framework for responsive design, via the
  `bootstrap-bp-hugo-startpage` theme.
* **GitHub Pages:** Used for hosting the static website.

## Building Locally for GitHub Pages

To build the site locally for GitHub Pages:

1. **Install Hugo:**

   ```bash
   # macOS (using Homebrew)
   brew install hugo

   # Or download from https://gohugo.io/installation/
   ```

2. **Build the site:**

   ```bash
   hugo
   ```

   This generates the static site in the `public/` directory.

3. **Preview locally:**

   ```bash
   hugo server
   ```

   The site will be available at `http://localhost:1313/`

4. **Build for production:**

   ```bash
   hugo --minify
   ```

   This creates an optimized build with minified assets in the `public/`
   directory, ready for deployment to GitHub Pages.

## Repository Structure

Key files and directories in this repository include:

* `hugo.toml`: The main Hugo configuration file, defining site
  parameters, themes, and other settings.
* `data/`: Contains data files (e.g., YAML or TOML) used by Hugo to
  generate site content, such as the links on the start page.
* `static/`: Stores static assets like images (e.g., `favicon.ico`),
  custom CSS, or JavaScript files that are served as-is.
* `themes/`: Contains the Hugo theme(s) used for the site. This site uses
  the `bootstrap-bp-hugo-startpage` theme.
