<div align="center">
	<img src="assets/icon.svg" width="128" height="128" />
	<h1>crystalline</h1>
  <h3>A Language Server for Crystal.</h3>
  <a href="https://github.com/elbywan/crystalline/tags"><img alt="GitHub tag (latest SemVer)" src="https://img.shields.io/github/v/tag/elbywan/crystalline"></a>
  <a href="https://github.com/elbywan/crystalline/blob/master/LICENSE"><img alt="GitHub" src="https://img.shields.io/github/license/elbywan/crystalline"></a>
</div>

<hr/>

#### `Crystalline` is an implementation of the [Language Server Protocol](https://microsoft.github.io/language-server-protocol/) written in and for the [Crystal Language](https://crystal-lang.org/).

#### It aims to provide some language features (like go-to, autocompletion, syntax and semantic checking) and ease development with any compatible code editor.

<div align="center">
<h3>Work in progress!</h3>
<img src="assets/demo-small.gif" />
</div>

## Installation

**Warning: this can take a long time! (several minutes - up to 15 minutes, depending on your hardware)**

### Scoped install

In the `.shard.yml` file:

```yml
development_dependencies:
  crystalline:
    github: elbywan/crystalline
    branch: main
```

Then:

```sh
shards build crystalline --release --no-debug --progress -Dpreview_mt
```

### Global install

```sh
git clone https://github.com/elbywan/crystalline
cd crystalline
crystal build ./src/crystalline.cr  -o ./bin/crystalline --release --no-debug --progress  -Dpreview_mt
# Then, copy or move the binary to any location covered by your $PATH. For instance:
mv ./bin/crystalline /usr/local/bin
```

### llvm-config

`llvm` is required in order to build `crystalline`, if you get the following error message it means that the crystal compiler is unable to locate the `llvm-config` binary:

```sh
--: : command not found
Showing last frame. Use --error-trace for full trace.

In /usr/local/Cellar/crystal/0.35.1/src/llvm/lib_llvm.cr:13:17

 13 | VERSION = {{`#{LibLLVM::LLVM_CONFIG} --version`.chomp.stringify}}
                  ^
Error: error executing command: "" --version, got exit status 127
```

This can be solved by adding the location of the `llvm-config` binary to the `LLVM_CONFIG` environment variable. (or the containing directory to the `PATH` env. variable)

For instance on a typical MacOS setup, prefixing the command with the following declaration would solve the issue:

```sh
# Prepend the command with this:
env LLVM_CONFIG=/usr/local/opt/llvm/bin/llvm-config
# For Example:
env LLVM_CONFIG=/usr/local/opt/llvm/bin/llvm-config crystal build ./src/crystalline.cr  -o ./bin/crystalline --release --no-debug -Dpreview_mt
```

## Usage

`Crystalline` is meant to be used alongside an editor extension.

For instance if you are a VSCode user, add the [Crystal Language extension](https://marketplace.visualstudio.com/items?itemName=faustinoaq.crystal-lang).
Then in the configuration, type the **absolute** location of the Crystalline binary in the following field:

![vscode screen](assets/vscode_extension_screen.png)

### Entry point

**Important:** Crystalline will try to determine which file is best suited as an entry point when providing language features.

The default behaviour is to check the `shards.yml` file for a `target` entry with the same name as the shard.

```yml
name: my_shard

targets:
  my_shard:
    main: src/entry.cr
```

Then every file required by the compilation of `src/entry.cr` are going to be considered as "workspace dependencies" and trigger the compilation of `src/entry.cr`.

If this `shard.yml` entry is not present, or if the file is not considered a "workspace dependency" `crystalline` will use it as the entry point.

**To override this behaviour**, you can add a configuration key in the `shard.yml` file.

```yml
crystalline:
  main: .crystalline_main.cr
```

This can be extremely important to understand when you are writing a code library (meaning that your own library code will not call any of its own methods - which will prevent the code analysis). In this case, and if you are writing `specs`, you can just point to a file that require the specs and it will use it as the entry point.

```crystal
# Contents of a file at the root of the project.
# Will require the specs that call the library methods and enable the code analysis.
require "./spec/**"
```

## Features

### Code Diagnostics

Syntax and semantic checks on save.

### Autocompletion

Triggered when typing the `.` or `:` characters and list (depending on the target) method definitions, macros or module/class/struct names.

### Formatting

A whole document or a text selection.

### Goto definition

By clicking on a symbol with the Cmd or Ctrl key pressed (editor/platform dependent).

### Hover information

Hovering should display either a variable type, a function definition signature or the expanded macro.

## Caveats

- Due to Crystal having a wide type inference system (which is incredibly convenient and practical), compilation times can unfortunately be relatively long for big projects and depending on the hardware. This means that the LSP will be stuck waiting for the compiler to finish before being able to provide a response.
Crystalline tries to mitigate that by caching compilation outcome when possible.

- Methods that are not called anywhere will not be analyzed, as this is how the Crystal compiler works.

## Development

###  Dev build

[Sentry](https://github.com/samueleaton/sentry) is used to re-build crystalline in debug mode on code change.

```sh
# To build sentry (once):
shards build --release sentry
# Then, to launch it and watch the filesystem:
./bin/sentry -i
```

### Logs

Logging is the most practical way to debug the LSP.

```crystal
# Use the LSP logger to display logs in the editor.
LSP::Log.info { "log" }
```
Debug logs are deactivated by default, uncomment this line in `src/server.cr` to enable them:

```crystal
# Uncomment:
# ::Log.setup(:debug, LSP::Log.backend.not_nil!)
```

## Contributing

1. Fork it (<https://github.com/elbywan/crystalline/fork>)
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create a new Pull Request

**Please always `crystal tool format` your code!**

## Contributors

- [elbywan](https://github.com/elbywan) - creator and maintainer

## Credit

- [Scry](https://github.com/crystal-lang-tools/scry), the original LSP for Crystal has been a great source of inspiration. I also re-used tiny bits of code from there.
- Icon made by [Smashicons](https://www.flaticon.com/authors/smashicons) from [www.flaticon.com](https://www.flaticon.com).

## Trivia

#### Why the name `cristalline`?

Aside of the obvious reasons (crystal-lang), `cristaline` is a famous bottled water brand in France.

![guy roux](assets/guyroux.gif)