Rust-kcov
=========

Provides coverage reports for your Rust projects using kcov.

Usage:
------

### Running the container:

```bash
$ docker run --rm -it -v "$PWD":/app --security-opt seccomp=unconfined apolitical/rust-kcov
```

Explained:
- `--rm` to automatically remove the container when finished
- `-it` to bind the terminal (not needed if you're going tell the container what command to run)
- `-v "$PWD":/app` bind the current working directory of the host to the `/app` directory inside
  the container. **Note:** `/app` is the default working directory we've set in the Dockerfile. If
  you want to put your app somewhere else, you can use `-v "$PWD":/elsewhere -w /elsewhere` to
  change the working directory.
- `--security-opt seccomp=unconfined` allows kcov to change the `personality` of the file being run,
  this unfortunately seems to be required.

### Once you're in the container:

Build the tests but don't run them yet

```bash
$ cargo test --no-run
```

This will have to download all dependencies unless you've somehow included them in your project
directory.

```bash
$ shopt -s extglob
$ kcov --include-pattern=src --verify target/cov target/debug/<your-app-name>-+([[:alnum:]])
```


Explained:
- `shopt` is for shell options, `-s` is to turn an option on, `extglob` is for extended pattern
- `--include-pattern` is used to limit which files will be included in the report (without it, it
  will report on all dependencies etc, which isn't useful information and will throw off coverage %)
- `--verify` checks for problems when the app was compiled, weirdly coverage doesn't work without
  it.
- You must then provide two locations:
  1. Where your coverage report to go, in this case `target/cov`
  2. The file to run. When `cargo test` runs it will create a file named after your project but with
     some random characters after it, but it creates a second file, almost the same but with a `.d`
     after it. This means you can nout use `<your-app-name>-*` because kcov will fail. Using
     `extglob` though, we're able to select a file with an alpha numeric suffix (i.e. no `.`).

## Contributing

This is a very loosley cobbled together project, I really hope you'll find it useful. If you have
any problems with it, please open an issue. If you'd like to make a Pull Request please do!
Checkout the [Contributing](CONTRIBUTING.md) guide and [Code of Conduct](CODE_OF_CONDUCT.md).
