#!/usr/bin/env node

"use strict";

const docsRoot = process.cwd();

const assert = require("assert");
const minimist = require("minimist")
const path = require("path");
const fs = require("fs");

const argv = minimist(process.argv.slice(2), {
  "--": true, // We'll pass the arguments after '--', if any, to Hexo.
});

// When input is a string, wrap it in an array.
// If input is undefined, hello empty array!
const ensureArray = (input) =>
  input &&
    typeof input === "string" ?
    [input] :
    input
  || [];

let configsToUse =
  ensureArray(argv.from) // Any --from arguments...
  .concat(argv._) // Along with any un-flagged params, which are also modules.
  .map(moduleId => {
    const absoluteConfigPath = require(moduleId);

    assert.strictEqual(typeof absoluteConfigPath, "string",
      "The config npm entry point must return an absolute path to its config.");

    assert(fs.existsSync(absoluteConfigPath),
      `Couldn't find ${absoluteConfigPath} specified by ${moduleId}`);

    return path.relative(docsRoot, absoluteConfigPath);
  });

// By default, we'll include the _config.yml in the root of the docs repository
// as the last config to be inherited.  This gives it ultimate control.
// Disable this behavior (and resort to using _only_ npm configed options), by
// passing the --skip-root-config option.
if (!argv["skip-root-config"] &&
  fs.existsSync(path.resolve(docsRoot, "_config.yml"))
) {
  configsToUse.push("_config.yml");
}

// Set the config flag to use the relative paths to the modules we've selected.
// Hexo also uses minimist, so we can play with its args, slightly.
const hexoArgv = minimist(argv["--"]);

// This is a stretch, but we'll also prepend any --config they passed to hexo
// directly, so we don't inadvertently ignore a config the user defined.
if (hexoArgv.config) {
  if (typeof hexoArgv.config === "string") {
    configsToUse.push(hexoArgv.config);
  } else if (Array.isArray(hexoArgv.config)) {
    configsToUse = configsToUse.concat(hexoArgv.config);
  }
}

// Combine all the configs as a comma-delimited list, as expected by Hexo.
hexoArgv.config = configsToUse.join(",");

// Defer to Hexo for the rest of the processing!
require("hexo-cli")(undefined, hexoArgv);
