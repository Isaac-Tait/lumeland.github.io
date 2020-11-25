---
title: Configuration
---

**Lume** uses the `_config.js` file that gives you a lot of flexibility to customize how to build your site. This file must be placed in the site's root directory, and you can create it by your own or with the following command:

```sh
lume --init
```

The `_config.js` file is a javascript module that exports the lume instance. The minimal required code is:

```js
import lume from "https://deno.land/x/lume/mod.js";
  
const site = lume();

export default site;
```

You can pass an object with configuration data to your site. This is an example with the default values:

```js
import lume from "https://deno.land/x/lume/mod.js";

const site = lume({
  // The "src" and "dest" folders are relative to this path
  cwd: Deno.cwd(),

  // The source directory of your site
  src: ".",

  // The destination output of the site
  dest: "_site",

  // The base location where the site will be published.
  // Useful to generate absolute urls or if your site is published in a subfolder
  // for example: https://username.github.io/project-name/
  location: new URL("http://localhost"),

  // Set true to build the site in development mode.
  // You can also override this value with the `--dev` flag from CLI
  dev: false

  // To generate pretty urls, for example `/about-us/` instead `/about-us.html`.
  // Set `false` to disable it.
  prettyurls: false,

  // Local server configuration
  server: {
    port: 3000,

    // The HTML page to display for 404 errors.
    // You can use, for example "/index.html" if you are building a webapp with dynamic urls
    page404: "/404.html",
  }
});

export default site;
```

## Installing plugins

Plugins add extra functionality or support for new formats. There are some basic plugins installed by default (like support for `markdown`, `yaml` or `json` formats), but there are other plugins that you can enable. For example, to add the `svg` plugin (that optimize svg files), you have to import and load in the config file:

```js
import lume from "https://deno.land/x/lume/mod.js";
import svg from "https://deno.land/x/lume/plugins/svg.js";
  
const site = lume();

//Add svg plugin
site.use(svg());

export default site;
```

## Copy static files

Static files are files that don't have to be processed, like images, pdfs, videos or audios. So it's better (and faster) to copy directly these files to dest folder with the `copy` function:

```js
// Copy the "img" folder to _site/img
site.copy("img");

// Copy the file to _site/favicon.ico
site.copy("favicon.ico");
```

The path is relative to the root's of the src directory and the files and folders are copies as is, maintaining the same folder structure. If you want to change the output directory, use the second argument:

```js
// Copy the "img" folder to _site/images
site.copy("img", "images");

// Copy the "static-files/favicons/favicon.ico" to _site/favicon.ico
site.copy("static-files/favicons/favicon.ico", "favicon.ico");
```

## Ignore files and directories

By default all files and folders starting with `.` or `_` are ignored. But you can add other files or folders using the `ignore()` function:

```js
site.ignore("README.md", "CHANGELOG.md", "node_modules");
```

Note: `node_modules` folder is ignored by default too.

## Template filters

Template filters are functions that you can use in your layouts to modify content. Some template engines like Nunjucks [have several builtin filters](https://mozilla.github.io/nunjucks/templating.html#builtin-filters), but you can add your owns:

```js
// Filter to convert a string to uppercase
site.filter("uppercase", (value) => value.toUpperCase());
```

Now, use it in your Nunjucks templates:

```html
<h1>{{ title | uppercase }}</h1>
```

## Custom scripts

Lume includes a basic script runner. Use the `script()` function to register new scripts:

```js
//Run one script
site.script("deploy", "rsync -r _site/** user@server.com:/var/www/");

//Use an array to run several scripts in parallel
site.script("compress", [
  "gzip -r _site/images images.gz",
  "gzip -r _site/videos videos.gz"
]);

//Use multiple arguments to run several script secuencially
site.script("uncompress", "gzip -d images.gz", "gzip -d videos.gz");

//Create a new script that run the previous scripts secuencially
site.script("run-all", "compress", "uncompress", "deploy");
```

To run a script from cli, use the `--run` command:

```sh
lume --run deploy
```

## Events

Lume has the following events:

- `beforeBuild`: To perform any action before build the site
- `afterBuild`: To perform any action when the build is complete
- `beforeUpdate`: To perform any action before update files (used on watch files under `--serve`)
- `afterUpdate`: To perform any action when the site is updated (used on watch files under `--serve`)

Use `_config.js` to create event listeners:

```js
site.addEventListener("beforeBuild", () => console.log("Preparing to update the site"));
```

You can also assign scripts to events:

```js
//Create a script
site.script("deploy", "rsync -r _site/** user@server.com:/var/www/");

//Run the script after build the site
site.addEventListener("afterBuild", "deploy");
```