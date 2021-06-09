---
title: Installation
description: Installing Lume
order: 1
---

**Lume** requires Deno (v1.10.3 or newer) being installed on your computer. Read
the [Deno installation](https://deno.land/#installation) instructions if you
don't have it yet.

## Install Lume on your computer

The easiest way to install Lume is by executing the following command:

```sh
deno run -A https://deno.land/x/lume/install.js
```

Now you have the `lume` command.\

<hr />

Note: If you get an error message (on a linux machine) similar to: 

```sh
command not found: lume
```

Check your path by running
```sh
echo $PATH
```

The colon delimited list should have `user/local/.deno/bin:$PATH` if not you can easily add it by running: 

```sh
export PATH="/Users/yourUserName/.deno/bin:$PATH"
```
Now, after running `echo $PATH` you should see `.deno/bin` in your path list. 

<hr />
Once installed, you can update Lume to the latest version by running:

```sh
lume upgrade
```

## Execute it without install

The `ci.js` file works exactly like the `lume` command but without installation.
It's useful for CI environments:

```sh
deno run -A https://deno.land/x/lume/ci.js
```
