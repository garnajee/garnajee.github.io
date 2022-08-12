# About

This is my personal website buil with [Hugo](https://gohugo.io/) and with the [PaperMod](https://github.com/adityatelange/hugo-PaperMod) theme.

The live website is available at https://jeans-github.github.io.

### Note

I made some small changes to the theme. You can find them in the `/layouts/` directory.

# Build

You first need to [install Hugo](https://gohugo.io/getting-started/quick-start/#step-1-install-hugo).

If you want to build this website for your own purpose:

```sh
$ git clone --recurse-submodules https://github.com/JeanS-github/jeans-github.github.io.git
$ git submodule init
$ git submodule update
```

And then start the Hugo server:

```sh
$ hugo server -D --minify
# To buid static pages
$ hugo -D --minify 
```
