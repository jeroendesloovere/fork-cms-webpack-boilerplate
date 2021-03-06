# Fork CMS theme boilerplate using Webpack 3

[![Greenkeeper badge](https://badges.greenkeeper.io/jessedobbelaere/fork-cms-webpack-boilerplate.svg)](https://greenkeeper.io/)
[![Dependency Status](http://img.shields.io/david/jessedobbelaere/fork-cms-webpack-boilerplate.svg?style=flat)](https://david-dm.org/jessedobbelaere/fork-cms-webpack-boilerplate#info=dependencies)
[![DevDependency Status](http://img.shields.io/david/dev/jessedobbelaere/fork-cms-webpack-boilerplate.svg?style=flat)](https://david-dm.org/jessedobbelaere/fork-cms-webpack-boilerplate#info=devDependencies)
[![PRs Welcome](https://img.shields.io/badge/PRs-welcome-brightgreen.svg?style=flat)](http://makeapullrequest.com)

Simple and optimized boilerplate for Fork CMS themes. It includes:

- [x] ES2017 support using Babel
- [x] Latest Webpack (v3.10.0) and Webpack Dev Server (v2.9.7) with Scope Hoisting enabled
- [x] Hot Module Replacement (HMR)
- [x] SASS support & PostCSS
- [x] Production config
- [ ] ES6 linting with continuous linting on file changes

Why? You need some basic tooling to get started with a current-generation frontend web development project. You may,
or may not, be sure if you need all the bells and whistles but you'd much rather have a setup that is easily extensible
and does the basics (build a distribution package, bundle JS, transpile ES2017 into ES5) than starting from zero.

## How do I use it?
Super simple, just clone or download the repository into your Fork CMS Themes directory and start hacking away!

1. Make sure you have [Node.js](https://nodejs.org/) and NPM installed (latest LTS recommended).
2. Copy this boilerplate to your `src/Frontend/Themes/MyThemeName` folder in your new Fork CMS project.
3. Install dependencies by running `npm install` in your theme directory.
4. Run `npm run build` and browse to your website. When doing local development, run `npm run watch` to start a dev server which you can visit on http://localhost:3000.

### Available commands
* `npm run build` - create a production-ready build in the `dist` folder.
* `npm run build:dev` - start a development build.
* `npm run watch` - start the webpack-dev-server with HMR.

## How it works
The `build` operation will clear the `dist` folder and compile/transpile and place fresh files in the `dist` folder.
The `npm run watch` command will use webpack-dev-server to start the dev server at `0.0.0.0:3000`. Webpack will use
`Core/Js/app.js` as the entrypoint and from there it will import the `screen.scss` file and other components and dependencies. With the server active, any files you work on and update will trigger a live update using Hot Module Replacement (no refreshing!).

## Fork CMS changes
### Manifest-based asset versioning in Symfony & Fork CMS
Webpack generates a `dist/manifest.json` file, mapping all source file names to their corresponding output file. For example:

```
{
  "app.css": "/src/Frontend/Themes/MyTheme/dist/app.89b2d31313389a466bb524e9051378bc.css",
  "app.js": "/src/Frontend/Themes/MyTheme/dist/app.7532415ade478926494f.js",
  "vendor.js": "/src/Frontend/Themes/MyTheme/dist/vendor.795cbea922c9c995ea51.js"
}
```

The random-looking part of the paths is called "chunk hash" in Webpack and it's a hash of the file contents.
This is the best strategy for long-term asset caching, because the hash, and therefore the asset path, will change
as soon as you make any change in the asset file, busting any existing cache. If we do not change our css or vendor code,
the hash will stay the same after a rebuild and no cache busting is needed.

To be able to include your `.js` and `.css` files in your `Head.html.twig` regardless of the version, you can use the
[Asset component](https://symfony.com/components/Asset) of Symfony to manage the assets. Define a new `json_manifest_path`
asset config option:

```
# app/config/config.yml
framework:
    # ...
    assets:
        json_manifest_path: '%kernel.root_dir%/../src/Frontend/Themes/MyTheme/dist/manifest.json'
```

Then, use the `asset()` function in your Twig files:

```
<link rel="stylesheet" type="text/css" href="{{ asset('app.css') }}">
<script src="{{ asset('vendor.js') }}"></script>
<script src="{{ asset('app.js') }}"></script>
```

The new version strategy will turn that link into `<link href="/src/Frontend/Themes/MyTheme/dist/app.89b2d31313389a466bb524e9051378bc.css">` and it will update it as soon as you change the original asset file and rebuild using Webpack.

## Learn more about Webpack?
* https://webpack.academy
* https://egghead.io/courses/using-webpack-for-production-javascript-applications
* https://survivejs.com/webpack/appendices/hmr/
* https://codeburst.io/long-term-caching-of-static-assets-with-webpack-1ecb139adb95
