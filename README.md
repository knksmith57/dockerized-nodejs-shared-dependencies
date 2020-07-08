## overview

Demo multi-container local development setup for node.js applications using a
shared volume with a file watcher to add externally managed dependencies to a
canned build image.

## quickstart

```sh
$ git clone gh:knksmith57/dockerized-nodejs-shared-dependencies \
    dockerized-nodejs-shared-dependencies \
  && cd $_

$ docker-compose up
```

## how it works

1. `build` container is designed to simulate a black box dev tool image capable
   of performing webpack builds
2. `dependencies` container runs a process watcher that install npm dependencies
   any time the local `package.json` file changes
3. a shared volume is mounted between the `build` and `dependencies` containers.
   `dependencies` installs `node_modules` into it, and `build` is able to load
   those installed packages because the volume is mounted at `/node_modules`,
   which the built-in node.js module loader will load from.

In this particular example, `style-loader` and `css-loader` are not provided by
the black box dev tool image. Instead, the host application specifies them as
`devDependencies` in its `package.json`.

The `webpack` process, running inside the `build` container, is configured to
use these loaders via `webpack.config.js`, and resolves those package
dependencies to `/node_modules/{style-loader,css-loader}`, which are installed
via the `npm install` command running inside of `dependencies`.
