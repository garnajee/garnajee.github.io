# About

This is my personal website build with [Hugo](https://gohugo.io/) and with the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme.

The live website is available at https://garnajee.github.io.

![build check](https://github.com/garnajee/garnajee.github.io/actions/workflows/gh-pages.yml/badge.svg)

### Note

I made some small changes to the theme. 

In order to *override* the actual theme, you just have to put your changed files in the root of the working folder.

In my case:

```sh
# I change these files:
/themes/PaperMod/assets/css/common/header.css
/themes/PaperMod/layouts/_default/single.html
# And to override them, I add changed files here:
/assets/css/common/header.css
/layouts/_default/single.html
```

# Build

You first need to [install Hugo](https://gohugo.io/categories/installation/).

If you want to build this website for your own purpose:

```sh
$ git clone --recurse-submodules https://github.com/garnajee/garnajee.github.io.git
$ cd garnajee.github.io/
$ git submodule init
$ git submodule update
```

And then start the Hugo server:

```sh
$ hugo server -D --minify
# To buid static pages
$ hugo -D --minify 
```

## Useful commands

To create a template for your main categories (e.g. "posts"): `$ vim archetypes/posts.md` and fill it with [my template](archetypes/posts.md) for example.

To create a new page in the "posts" category:

```sh
$ hugo new posts/<post-name>.md
Content "/your/path/garnajee.github.io/content/posts/<post-name>.md" created
```

Now, edit this new page: `$ vim content/posts/<post-name>.md`.

