# Trealla for Spin

This is a collection of [Spin](http://spin.fermyon.dev) templates for using the WebAssembly build of [Trealla Prolog](http://github.com/trealla-prolog/trealla).

Currently a work in progress. [See here](https://github.com/guregu/trealla#spin-components) for more info about what's supported.

## Usage

#### Installing/updating

You'll need to [install Spin](https://developer.fermyon.com/spin/install) first.

Then, add the templates from this repository to Spin:

```bash
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

Write to the `http_body` stream or use one of the *_content predicates to respond.

```prolog
format(http_body, "Welcome, visitor #<b>~d!</b>", [N]).
```

#### http_handler/4

`http_handler/4` is a multifile predicate, just make sure to `:- use_module(library(spin))` to import it.

```prolog
%% http_handler(+Spec, +Headers, +Body, -Status).
%
% Spec is a term representing the request method, path, and query parameters.
% For example, a GET request to /foo?id=xyz&a=123 is get("/foo", ["a"-"123","id"-"xyz"]).
% Headers are the request headers.
% Body is the request body.
% Status is the response status code.
http_handler(Spec, Headers, Body, Status) :- ... .
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
Note that Fermyon Cloud doesn't support key-value stores yet, so the default visitor counter example won't work.

```console
$ spin deploy
```

You can also [create OCI images](https://developer.fermyon.com/spin/spin-oci), and [Fermyon Platform](https://www.fermyon.dev) is a self-hosted platform for running Spin.

## Spin library

```prolog
% library(spin)

% response handler
http_handler/4 (+spec,+list,+body,-integer) [multifile]
% request info
current_http_uri/1      (?string)
current_http_method/1   (?string)
current_http_body/1     (?string)
current_http_param/2
current_http_header/2   (?string,?string) % name,value
% response i/o
http_header_set/2       (+string,+string) % name,value
http_body_output/1      (-stream)
html_content/0  % sets mime header
html_content/1          (+string)
text_content/0
text_content/1          (+string)
prolog_content/0
prolog_content/1        (+term)
% key-value store
store_open/1            (-handle)
store_open/2            (+atom,-handle)
store_close/1           (+handle)
store_get/3             (+handle,?term,?term) % key,value
store_exists/2          (+handle,+term) % key
store_keys/2            (+handle,-list) % all keys 
store_set/3             (+handle,+term,+term) % key,value
store_delete/2          (+handle,+term) % key
% outbound http
http_fetch/3    (+string,-body,+options)
```

## What's next?

- Supporting more Spin components like Redis and SQL.
- Considering stealing the HTTP library's syntax from Scryer Prolog
- Porting [php (Prolog Home Page)](http://github.com/guregu/php) from CGI to Spin components.
- Document the [Spin-related predicates](https://github.com/guregu/trealla/blob/main/library/spin.pl)

![Trealla Logo](https://user-images.githubusercontent.com/131059/190109875-7eb65bf5-feef-41e1-b19c-7fbcab8887ae.png)
