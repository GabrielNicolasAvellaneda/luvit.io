{ title = "Luvit Reborn",
  subtitle = "Announcing Luvit 2.0",
  author = {
    name = "Tim Caswell",
    email = "tim@creationix.com",
    twitter = "creationix",
    github = "creationix"
  },
  tags = { "news", "tutorial" },
  published = 1426787704
}

The original luvit (started 2011 by [Tim Caswell][]) was a node.js-like
programming environment, but using Luajit instead of V8. This meant a
change in scripting language and a huge change in memory overhead. Speed
between node and luvit was on the same order of magnitude with V8 being
faster sometimes and Luajit faster sometimes. But memory was far more
efficient in luvit. A small node.js program used about 20 times more
memory than a similar luvit program. Luvit found its niche in places
like [cloud monitoring][] and scripting on slower devices like Raspberry
PIs. It had nearly identical APIs to node and thus was easy to learn for
developers looking for something like node, but less memory hungry.

![](luvit-reborn/a-new-era.jpg)

Luvit 2.0 is a reboot of this idea but far more flexible and
configurable. The new system consists of many parts that can be used
with or without the new luvit framework.

 - [luv][] - New [libUV][] bindings for [Lua][] and [Luajit][].
 - [luvi][] - Pre-compiled [luajit][] + [luv][] + [openssl][] + [zlib][] with zip asset bundling and self-executing apps.
 - [lit][] - The **L**uvit **I**nvention **T**oolkit is a multi-tool
   for building apps, running apps, installing, publishing, and
   serving libraries and apps.  Contains [luvi][] and can embed it in
   new apps.

These three projects offer layers of abstraction and control.  You can
use [luv][] standalone with any lua based runtime.  You can build apps
with [luvi][], which includes [luv][], without using [lit][] or Luvit.
The [lit][] tool embeds [luvi][] and adds higher-level commands and
workflows.

Luvit 2.0 is one more layer on top of this that implements the
[node.js][] [APIs](http://nodejs.org/api/) in lua as a collection of
standalone [lit libraries][].  Luvit can be used several different
ways from lit.

## Luvit 2.0 the Platform

You can build the `luvit/luvit` metapackage as an app directly to
create the `luvit` command-line tool that mimics the `node` tool and
lets you run arbitrary lua scripts.

```sh
lit make github://luvit/luvit
sudo install luvit /usr/local/bin
luvit
```

This works much like the original luvit platform which works like node.

## Luvit 2.0 the Framework

You can use luvit as a metapackage that includes the luvit runtime as
a library as well as including all the luvit standard library as
recursive dependencies to your app.  Simply declare `luvit/luvit` as a
dependency to your lit app and use the luvit library in your
`main.lua` and your standalone executable will live inside a luvit
style environment.

A sample `package.lua` that includes luvit might look like the
following:

```lua
return {
  name = "my-cool-app",
  version = "1.2.3",
  dependencies = {
    "luvit/luvit@2.0.0",
    "creationix/git@1.2.3",
  }
}
```

And the luvit bootstrap in your app's `main.lua` will look something
like:

```lua
-- Create a luvit powered main
return require('luvit')(function (...)
  -- Your app main logic starts here
end, ...)
```

Then when you build your app with `lit make`, luvit and all it's
libraries will be included in your app.  Also if you install your
app's deps to disk using `lit install`, luvit and all it's deps will
be included in the `deps` folder.

```sh
~/my-cool-app $ lit make
~/my-cool-app $ ./my-cool-app
```

You app will have it's own custom main, but will have all the same
builtins and globals as luvit (plus whatever other globals and
builtins you added).

## Luvit 2.0 the Library

The individual packages that make up the luvit 2.0 metapackage can be
used on their own without buying into the whole ecosystem.  Perhaps
you love the pretty-printer and and advanced readline repl but abhor
callbacks and want to use coroutines instead.  Just mix and match
luvit libraries with other lit libraries in your app or library.  Each
component of the luvit metapackage can be used directly and will
automatically pull in any inter-dependencies it needs.

For example, the `creationix/rye` app uses parts of luvit, but not
it's globals and full set of modules.

```lua
return {
  name = "creationix/rye",
  private = true,
  version = "0.0.1",
  dependencies = {
    "luvit/require@0.2.1",
    "luvit/http-codec@0.1.4",
    "luvit/pretty-print@0.1.0",
    "luvit/json@0.1.0",
    "creationix/git@0.1.1",
    "creationix/hex-bin@1.0.0",
    "creationix/coro-tcp@1.0.4",
    "creationix/coro-fs@1.2.3",
    "creationix/coro-wrapper@0.1.0",
  },
}
```

[cloud monitoring]: https://github.com/virgo-agent-toolkit
[Tim Caswell]: https://github.com/creationix
[libuv]: http://docs.libuv.org/en/v1.x/
[luvi]: https://github.com/luvit/luvi
[luv]: https://github.com/luvit/luv
[lit]: https://github.com/luvit/lit
[lit libraries]: http://lit.luvit.io/packages/luvit
[lua]: http://www.lua.org/
[luajit]: http://luajit.org/
[openssl]: https://www.openssl.org/
[zlib]: http://www.zlib.net/
[node.js]: http://nodejs.org/
