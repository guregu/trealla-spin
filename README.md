# Trealla for Spin

This is a collection of [Spin](http://spin.fermyon.dev) templates for using the WebAssembly build of Trealla Prolog.

Currently a work in progress. [See here](https://github.com/guregu/trealla#spin-components) for more info about what's supported.

## Usage

#### Installing/updating

You'll need to [install Spin](https://developer.fermyon.com/spin/install) first.

```bash
# Add templates from this repo to Spin
spin templates install --git https://github.com/guregu/trealla-spin --update
```

#### Start a new project

```console
$ spin new http-prolog
Enter a name for your new application: hello-world
Description: prolog is web scale
HTTP base: /
HTTP path: /...

$ cd hello-world; ls 
spin.toml src
```

#### Write some Prolog

The Prolog program in `src/init.pl` will be consulted automatically.
From here, you can use/consult other files. A couple example handlers for HTTP are included.

Here's a minimal example of an HTTP handler that returns a JSON message with the current time.

```prolog
:- use_module(library(spin)).

http_handler(get("/json", _), _, _, 200) :-
	wall_time(Time),
	% json_content({"time": Time}) works too
	json_content(pairs([string("time")-number(Time)])).
```

#### http_handler/4

`http_handler/4` is a multifile predicate, just make sure to `:- use_module(library(spin))` to import it.

```prolog
%% http_handler(+Spec, +Headers, +Body, -Status).
```

#### Run it

```console
$ spin up
Serving http://127.0.0.1:3000
Available Routes:
  my-cool-project: http://127.0.0.1:3000 (wildcard)
```

#### Deploy it

The easiest way is to use [Fermyon Cloud](http://cloud.fermyon.com).
Note that Fermyon Cloud doesn't support key-value stores yet, so the default visitor counter won't work.

```console
$ spin deploy
```

You can also [create OCI images](https://developer.fermyon.com/spin/spin-oci).

## What's next?

- Supporting more Spin components like Redis and SQL.
- Considering stealing the HTTP library's syntax from Scryer Prolog
- Porting [php (Prolog Home Page)](http://github.com/guregu/php) from CGI to Spin components.
