# Chexo - <u>C</u>onfigure <u>H</u>exo from an npm module!

> To learn about the Tibetan rice dish, see https://goo.gl/vjeJq7. Yum!

This allows an npm module to provide a Hexo configuration, rather than needing
to provide it from a static file.  This allows a managed Hexo configuration to
be updated and get updated automatically in a repository that depends on it.

While Hexo does support nested, inherited configurations by passing multiple
configuration files with a `--config` flag, this automatically includes the
configuration for each specified module, as well as the `_config.yml` which
might be present at the root of the project (if it's present).

## Usage

Essentially, pass the usual `hexo` arguments (e.g. `generate`, `server`, etc.),
after the `--` when calling `chexo`, which will pass them directly to `hexo`,
with the exception of the `--config` argument. Any `--config` passed after
`--` will be appended to the end of the list we are already setting.

    npx chexo npm-module-name another-npm-module -- generate

## The config modules

The configuration modules should have a configuration YAML file and the default
entry point for the module should return the absolute path to that module.

This means a configuration npm package needs two files:

### `_config.yml`

A typical Hexo configuration file!

### `index.js` (or the `main` attribute path in `package.json`)

```js
// This should return the path to the _config so then we
// require("this-package"), it returns "/path/to/cfg/node_modules/_config.yml"
module.exports = require("path").resolve(__dirname, "_config.yml");
```

## Arguments

Unflagged keywords will be interpreted as `--from` arguments.

* `--from`: A module to configure from.  Multiple `--from` flags may be passed.
* `--skip-root-config`: Don't include the project's root `_config.yml`.  This
  is the automatic behavior if the `_config.yml` doesn't exist in the root!
