---
title: "Unambiguous Webpack config with Typescript"
description: "You can write your Webpack config in Typescript, and it’ll save you a huge amount of pain. Webpack’s docs would lead you to believe that using Typescript requires a hacky customized set up, but in…"
date: "2017-06-18T19:14:19.928Z"
categories: 
  - JavaScript
  - Typescript
  - Webpack
  - Sublimetext
  - Debugging

published: true
canonicalLink: https://medium.com/webpack/unambiguous-webpack-config-with-typescript-8519def2cac7
---

![](./asset-1.jpeg)

_You can write your Webpack config in Typescript, and it’ll save you a huge amount of pain. Webpack’s docs would lead you to believe that using Typescript requires a hacky customized set up, but in fact it’s as simple as installing a single module and changing your extensions from _`_.js_` _to_`_.ts_`_!_

If you’re familiar with the pains of Webpack and you just want to know how to write your config in Typescript, you can skip to the second section, [_Typescript to the Rescue_](#38b8).

---

Webpack is notoriously complex. Just [ask the authors](https://github.com/webpack/webpack/issues/2797):

> **Problems to solve:** Webpack is too low level; There are no reasonable set of defaults; Terminology doesn’t make sense; Too much boilerplate in handling environment logic; The CLI lacks scaffolding and initialization; Configuration is ‘convoluted’

And you don’t have to look far for Medium posts like [this](https://medium.com/@jhabdas/webpack-is-your-achilles-heel-d3cd80821a4f):

> How much time have you spent messing around with your Webpack configuration to try make it performant, and work seamlessly with your HMR, your linter, TypeScript, Babel — so you could finally achieve developer Zen. How much time have you wasted finagling Webpack into doing what you want, instead of actually shipping product?

[This ongoing thread on usability](https://github.com/webpack/webpack/issues/2797) has some great ideas about how to improve the build tool. It makes me happy, because I for one have spent far too much of my energy messing around with my config instead of actually building cool stuff. To name a few issues that stand out:

-   **Many ways to represent the same thing** — To take just one example, Webpack’s `[loaders](https://webpack.github.io/docs/loaders.html)` [interface](https://webpack.github.io/docs/loaders.html) is incredibly permissive. A loader can be a string of just the name, an object containing a bunch of options, or a string with query params. You can have `preLoaders` and `postLoaders`, and you can have keys named `loaders` (plural) and/or `loader` (singular) or `[use](https://stackoverflow.com/questions/41750715/when-do-i-use-use-and-loader-in-webpack-2-module-rules)` (because why not?). You can pass options with `[options](https://github.com/webpack/loader-utils/issues/69)`[, or sometimes with](https://github.com/webpack/loader-utils/issues/69) `[query](https://github.com/webpack/loader-utils/issues/69)`. I could go on. There are many ways to get the effect you want, though some ways are more correct than others. This is overwhelming for a newcomer, and it can lead to frustrating bugs for anyone. Here are just a few of the possibilities:

<Embed src="https://gist.github.com/devonzuegel/8919154c7f333fb784dcae361b6ba468.js" aspectRatio={0.357} />

-   **Opaque or non-existent error messages** — It’s hard to track down bugs in your config when the only constraint enforced is that it exports a Javascript object. A shocking number of Stack Overflow answers about Webpack boil down to “you have a typo”, and checking for that is the first suggestion in this post entitled [_How to fix Webpack when it can’t find your modules_](https://cabanalabs.com/2016/05/04/how-to-fix-webpack-when-it-cant-find-your-modules/). This is the tip of the iceberg when it comes to the ways Webpack silently allows you break things.
-   **Easy to accidentally use old syntax** — Webpack’s authors have taken pains to ensure backwards compatibility. This is nice of them, because it allows developers to be confident that Webpack won’t break underneath you (and this would be a _disaster_ if it did, because it is the build tool of choice for an increasing portion of the internet). Plus, it doesn’t put any constraints on people’s designs or preferences. But this lack of constraints is also a nightmare, because it’s way too easy to use outdated APIs. New projects should use Webpack 2, but it’s nearly impossible to differentiate between them when viewing examples online. The two versions share a lot of naming conventions, and they don’t enforce usage consistency. As a result, most people are using a mix of Webpack 1 and 2 without knowing it.
-   **Lack of sensible defaults** — Webpack’s customizability is powerful, but it also leaves the newbie (or even someone who just wants to whip up something quickly) out in the cold. The interface does not guide you to reasonable defaults; it requires upfront knowledge of the underlying tool before you can even get started.

Each of these areas present serious challenges, but they are also emblematic of Webpack’s greatest strength: its flexibility. This is a classic instance of trade offs. Focusing on these shortcomings could sacrifice the other characteristics that make it such a powerful build tool. Webpack’s usability will continue to improve, but at the end of the day we have to accept the complexity that comes with anything this configurable.

Luckily, this is a problem we can solve with types, without sacrificing flexibility!

### Typescript to the rescue

Around the time I started using Webpack, a friend introduced me to [Typescript](https://www.typescriptlang.org/), and I’ve since used it in all of my side projects. I won’t gush about it here — I’ll let the Typescript core team [do that](https://www.youtube.com/watch?v=XoxHsspCFyQ&t=669s) — but I will say that it’s transformed the way I build software. Within weeks, I replaced every line of Javascript with strict Typescript. The only exception was my Webpack config files, because the tool did not support TS, as far as I could tell from documentation. I thought I’d have to manually compile the config into JS files before every compilation, which would be slow and a whole other source of frustration around Webpack.

So you can imagine my delight upon discovering that I was wrong! You can write your Webpack config in Typescript, and it’s easy. As far as I know, Webpack’s docs don’t mention this anywhere, but after some poking around I found [this lone Stack Overflow](https://stackoverflow.com/questions/40075269/is-it-possible-to-write-webpack-config-in-typescript) post, which noted that there is [a hint in the source code](https://github.com/webpack/webpack/blob/7871b6b3dc32425406b3938be490a87aeb6fa794/bin/convert-argv.js#L27-L39).

_Edit: Since I wrote this post in June 2017, the Webpack team has added great documentation on how to write config files in Typescript. You can find it_ [_here_](https://webpack.js.org/configuration/configuration-languages/#typescript)_._

All you have to do is:

1.  Add `[ts-node](https://www.npmjs.com/package/ts-node)` as a `devDependency`.
2.  Replace your config’s extension from `.js` to `.ts`.
3.  Run webpack — config `webpack.config.ts` as usual.

It’s seriously that simple.

### Discovery with types

You can do more, but even with these two tiny changes you’ll immediately see benefits. (I’ll get into extensions later.) In particular, the [type definitions for Webpack](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/718c4df8f6ad50c15eb0334eb804d3f091c46eef/types/webpack/index.d.ts) are fantastic, and you get these for free. Typescript’s [Autocomplete](https://github.com/Microsoft/TypeScript-Sublime-Plugin) and [type guards](https://www.typescriptlang.org/docs/handbook/advanced-types.html) guide you to the right usage, without the friction of a Google search or guessing how what something is called. Rather than googling for overloaded terms like “resolve” or “module”, which will turn up many irrelevant or outdated results, you can see the options exposed by the interface in your text editor. And if you want visibility into what’s going on under the covers, [Go To Definition](https://github.com/Microsoft/TypeScript-Sublime-Plugin/tree/eb57cebd62d0c2ea2c17d222c8c1c3cefa40f31b#features) jumps you right to the location in the `.d.ts` file.

> The Typescript package for Sublime is great. You can [add a shortcut to your keymap](https://gist.github.com/devonzuegel/c62e628d04c88678ba6d6d4ec395d91b#file-devon-default-sublime-keymap-L28) that makes code navigation effortless, even into `/node_modules` and other directories excluded by your `sublime-project` file:

```
{ "keys": ["ctrl+d"], "command": "typescript_go_to_definition" }
```

Let’s take the loader interface mentioned at the beginning of this post. Its flexibility can be mind-boggling. Everyone seems to make up their own thing, and it’s unclear why you’d use one approach over the other.

Digging into the types offers a lot of clarity. Types can make Webpack’s flexibility work with you, rather than against you. For illustration, let’s build out a simple pipeline to compile CSS files. We’ll start off this simple skeleton:

> Note that in the following examples, I’ve pared down the comments and extra lines for the sake of focus. You can find the unabridged Webpack type definitions [here](https://github.com/DefinitelyTyped/DefinitelyTyped/blob/718c4df8f6ad50c15eb0334eb804d3f091c46eef/types/webpack/index.d.ts).

<Embed src="https://gist.github.com/devonzuegel/25505ec9c0b3a30a37a0760c27769b7d.js" aspectRatio={0.357} />

By defining that config is of type `webpack.Configuration`, we can follow the types to fill in a huge amount of information.

**1) Jump to definition for** `**webpack.Configuration**`

Click into the type and Typescript’s Go To Definition (GTD) will jump us right to where the `webpack.Configuration` interface is defined. We can see that it takes an optional module of type `Module`, which we know is where we want to define our loader rules. Again, use GTD to jump to the `Module` interface definition.

<Embed src="https://gist.github.com/devonzuegel/d3f0364c41a064bcf0cface5e956c5a9.js" aspectRatio={0.357} />

**2) Constrain module to the Webpack 2 interface**

Here, we see that `Module` is a union type of `OldModule` or a `NewModule`. This is interesting. If we were new to Webpack, we might not have known that the `webpack` node module simultaneously supports both Webpack 1 and 2. We might not even know that there are two separate versions. This would give us a first hint.

<Embed src="https://gist.github.com/devonzuegel/697c62a37242438bd8bbc3e4d201690c.js" aspectRatio={0.357} />

Ah ha! Here’s our first peek at some of the complexity in the interface. We can see that `OldModule` and `NewModule` have different key names for the array of transformations applied to our files: `loaders` and `rules`. And both of these both take an array of `Rules`, implying they do mostly the same thing. Cool. Now when we see examples online with different key names, we can know a few things: (1) their interface is equivalent besides the different naming convention, and (2) if someone is using `loaders`, they’re using Webpack 1.

Let’s stick with the Webpack 2 `NewModule` way of doing things. If we GTD for Rule, we see:

<Embed src="https://gist.github.com/devonzuegel/0ed2f07abecfb30772d4babe7db0f0d1.js" aspectRatio={0.357} />

Let’s start at the bottom. We see that `Rule` is a union of several specific types: `LoaderRule`, `UseRule`, `RulesRule`, and `OneOfRule`. Some of these are the union of `Old*` and `New*` rules. We only want to use Webpack 2 features, so let’s remove the `Old*` interfaces in order to focus on the options we care about.

The `NewLoaderRule` is just shorthand for the `NewUseRule`. The former applies a single loader, while the latter applies an array of loaders. I prefer to use `use` for all rules, including those with a single loader, because it makes usage more consistent throughout the config. It’s much more clear to a future developer that they doing the same thing, rather than having to dig through the types to understand that `loader` is just shorthand.

<Embed src="https://gist.github.com/devonzuegel/2835987e6affb1ea2e2d64b7c8b5f558.js" aspectRatio={0.357} />

So let’s constrain our interface further by removing `NewLoaderRule` from our consolidated types. This leaves us with:

<Embed src="https://gist.github.com/devonzuegel/f7cd0870e11bdff1ae9151544ae05641.js" aspectRatio={0.357} />

**3) Constrain the rule interface to just the type we need**

This simplifies our options a bit. Now, let’s dig into the question of what the “delegate rules” are doing. Until writing this post, I had never actually come across usage of `RulesRule` or `OneOfRule`, so this was new for me, too. They both take an array of `Rule`s, so we could have nested `Rule`s of arbitrary depth. Strange. The comments on the attributes of `BaseRule` explain:

-   `rules` is “an array of `Rule`s that is also used when the \[parent\] `Rule` matches”, and
-   `oneOf` is “an array of `Rule`s from which only the first matching `Rule` is used when the \[parent\] `Rule` matches”

In other words, the parent `Rule` acts as a filter, and any files that match that filter are then piped through the lists of `Rule`s in `rules` and `oneOf`. For example, you might want to apply different loaders to the same file extension [based on issuer](https://survivejs.com/webpack/loading/loader-definitions/#loading-based-on-issuer-) (i.e. where a resource was imported). You could use rules to specify the use of `style-loader` only when the CSS file is imported in a Javascript file:

<Embed src="https://gist.github.com/devonzuegel/5188b8fd6072c1dfccd82ef819702924.js" aspectRatio={0.357} />

You could use `oneOf` to route to a specific loader based on a resource related match:

<Embed src="https://gist.github.com/devonzuegel/36aa56cc7baa85f0db4cad5652a6a0f4.js" aspectRatio={0.357} />

It’s good to know about `rules` and `oneOf` in case we stumble upon them in the future, but they’re overkill for what we’re trying to do: compile CSS files. So let’s remove `RulesRule` and `OneOfRule` from our consolidated types. This leaves us with:

<Embed src="https://gist.github.com/devonzuegel/71ad5590b4aea3ebb7e250342cc8ff68.js" aspectRatio={0.357} />

_Even if we were doing something more complex, we might still prefer to keep our rules defined at the top level. For instance, the_ `_oneOf_` _example is equivalent to the following snippet, which is more explicit, more concise, and easier to understand:_

**4) Write your** `**NewUseRule**`

Now, it’s clear what we need to do. We need to write a rule that defines a test and a use. We can get fancy and define other stuff, but those two keys are the bare minimum for satisfying the interface. Let’s go back to the skeleton we defined at the beginning and fill this in:

<Embed src="https://gist.github.com/devonzuegel/aa5fb6f4bc39ca90f9e83adca74eaaeb.js" aspectRatio={0.357} />

Awesome! Now we have a simple Webpack config that compiles CSS files. To summarize what we did here:

1.  Use GTD to view the definition for `webpack.Configuration`.
2.  Constrain the `Module` interface to just the Webpack 2 features.
3.  Constrain the rule interface to `NewUseRule`.
4.  Write the rule to pipe `.css` files through the `style-loader`.

This was a wall of text, but the process of paring down the interface to understand what you want to do is quick in practice. These steps can take seconds once you get the hang of diving into type definitions.

### Documentation in your text editor

Not only are the Webpack type definitions thorough, but the maintainers have added extensive comments to everything. If you’ve ever wondered what the impact of changing your `devtool` option would be, you’ll see a nice comment like this above that line in the definition along with all of the possible values:

<Embed src="https://gist.github.com/devonzuegel/90c137bac0315bd4438062a1a4aaa199.js" aspectRatio={0.357} />

The type definitions often serve as better documentation than the project site. Examples and tutorials are invaluable, but the real challenge of Webpack comes whenever you’re doing something custom. The official docs can only enumerate so many of the permutations of config options. Even if they were to note each and every one, surfacing these examples would be a nightmare.

The type definition files have the added advantage of allowing you to stay within your text editor. I can often solve my problem without leaving Sublime, even when I’m learning about a brand new tool in Webpack’s extensive feature set. These tools allow me to discover the answers latent in the type definition when before I had to hunt them down.

On top of all this, the “documentation” provided by the types don’t go stale. Blog posts and examples in documentation can get out of sync with the tool as its version gets bumped. Webpack has improved rapidly in the past few years, so when working off of an example I’ve found online, I’m always concerned that it no longer applies. I never have this concern when working off the types. Their definitions are mapped directly to the interface you depend on, and updating them is part of changing the version. You never have to worry if your types are wrong.

### Constraining the interface to what you need

To get the full benefits of writing your config in Webpack, you’ll want to add a few more things. One addition is you can add return types to various components of the config. For instance, I like [adding the](https://github.com/devonzuegel/clarity/blob/16ec48cfb55b3832aa3d4c6429d543efc667bc99/webpack/loaders/css-loader.ts#L19) `[webpack.Configuration](https://github.com/devonzuegel/clarity/blob/16ec48cfb55b3832aa3d4c6429d543efc667bc99/webpack/loaders/css-loader.ts#L19)` [type to partials](https://github.com/devonzuegel/clarity/blob/16ec48cfb55b3832aa3d4c6429d543efc667bc99/webpack/loaders/css-loader.ts#L19), because it ensures that they can the be `[webpackMerge](https://github.com/devonzuegel/clarity/blob/16ec48cfb55b3832aa3d4c6429d543efc667bc99/webpack/frontend/index.ts#L36)`[d](https://github.com/devonzuegel/clarity/blob/16ec48cfb55b3832aa3d4c6429d543efc667bc99/webpack/frontend/index.ts#L36) in the top-level config file.

I also like to create a [custom](https://github.com/devonzuegel/clarity/blob/16ec48cfb55b3832aa3d4c6429d543efc667bc99/webpack/models/Options.ts) `[Options](https://github.com/devonzuegel/clarity/blob/16ec48cfb55b3832aa3d4c6429d543efc667bc99/webpack/models/Options.ts)` [type](https://github.com/devonzuegel/clarity/blob/16ec48cfb55b3832aa3d4c6429d543efc667bc99/webpack/models/Options.ts), which can be passed into partial functions to build up the greater config. This is nice, because usually you only care about tweaking a few of the Webpack options between development, testing, and live environments. This allows you to constrain the massive number of possibilities to a much smaller interface. Of course you can already do this with typical Javascript by simply creating a wrapper function that takes a subset of the options as arguments, but with Typescript you have the added ability to follow which options are explicitly defined by the top-level config and which are downstream effects of those options. In short, **Typescript allows you to constrain the interfaces you’re working with to just the parts that you care about**.

In the earlier loader example, we went through the process of mentally consolidating the `Loader` interface. This was useful for minimizing the surface area of the interface to just what we needed. We can go one step further and _enforce_ these types.

For instance, we can constrain the interface to use only Webpack 2 features. A common complaint is that it’s hard to distinguish documentation for Webpack 1 and 2, and it’s easy to inadvertently use an outdated approach. To avoid this, you can extend the `webpack.Configuration` type to limit the interface to just the style you want to use. Typescript lets you do:

<Embed src="https://gist.github.com/devonzuegel/9b5325546ea9522a26750a59164b64c5.js" aspectRatio={0.357} />

So you can do something like this, if you wanted to constrain your resolve config to only the `NewResolve` type rather than the default type which also allows `OldResolve`:

<Embed src="https://gist.github.com/devonzuegel/475c2a570124d09fbf4e6eb3f4e77d96.js" aspectRatio={0.357} />

You could enforce usage of only the `NewUseRule` just like in the loaders example at the beginning:

<Embed src="https://gist.github.com/devonzuegel/290d507a1cfb1a81427a34c91a2abaf4.js" aspectRatio={0.357} />

Now, your `Config` type makes sure that no one uses a different interface by accident. You won’t be able to breach the codebase’s convention without explicitly specifying the change in the interface. Webpack configuration is complex enough to hold in your own head, and disseminating the knowledge for how each piece should work is even harder in a large organization. Types can do a lot of this work for you. They take on much of the mental overhead associated with following conventions or disentangling all of the possibilities latent in Webpack.

---

Type annotations remove a lot of the pain created by Webpack’s complex and overly permissive interface. There are other ways the project could improve its usability, but Typescript goes a long way in solving these problems.

This post is just an initial set of ideas about how I’ve used Typescript’s tooling to improve my workflow — I’m excited to explore it further. I also want hear how others have used types (and other tools!) to improve Webpack’s developer experience, so don’t hesitate to reach out.

_Thanks to_ [_John Backus_](http://twitter.com/backus) _for his help brainstorming and revising this post with me._ ❤

---

_No time to help contribute? Want to give back in other ways? Become a Backer or Sponsor to webpack by_ [_donating to our open collective_](https://opencollective.com/webpack)_. Open Collective not only helps support the Core Team, but also supports contributors who have spent significant time improving our organization on their free time! ❤_