# Cocaine HTTP proxy
[![Build Status][ci-img]][ci-url]

An entry point to the Cocaine Cloud.

Cocaine HTTP proxy terminates HTTP traffic and transforms it into the binary protocol, allowing to communicate with Cloud services and applications.

ВСТАВИТЬ КАРТИНКУ ГРАФ HTTP-PROXY-COCAINE-APP

It's a high-performance solution, developed to transparently replace the [cocaine-tornado-proxy][cocaine-tornado-proxy] that is written in Python. 

### Installation
The HTTP proxy can now be built only from sources.

You can install a binary compiled with Rust nightly using `cargo`. Note that this requires you to have Rust **nightly** with version 1.17 or newer installed.

```bash
cargo install --path=PATH
```

Additionally, if you're a Debian user you may find convenient to build a Debian package using the following command.

```bash
cargo install --git=https://github.com/mmstick/cargo-deb
cargo deb
```

The resulted debian package lies in `./target/debian` directory.

### Features

##### High performance and low memory footprint.
Backed with [Cocaine Framework Rust][cocaine-framework-rust] and [MessagePack][rmp] this HTTP proxy allows to communicate asynchronously with the Cloud applications and services.
    
##### Cloud Logging.
...

##### Metrics
...

##### Tracing
The proxy is aware of Google Dapper tracing mechanism. Each request is marked with three special internal headers: **trace_id**, **span_id** and **parent_id**, which are transported with it, allowing to build full tracing path to ease debugging.
  
...
  
##### JSON-RPC
The proxy supports [JSON RPC][jsonrpc] protocol for calling Cocaine services with the following restrictions: 

- The "method" parameter must be represented in form of `SERVICE.EVENT`.
- The "params" parameter may be omitted when a service takes no arguments, an array with positional arguments when a service protocol terminates immediately, named arguments with a fixed format otherwise (see below).

For example let's call "info" method from an "echo" application service (no "params" parameter):

```bash
esafronov@local:~$ curl http://localhost:8080/ -H"X-Cocaine-JSON-RPC: 1" -d '{"jsonrpc": "2.0", "method": "echo.info", "id": 0}' | python -m json.tool
{
    "error": {
        "code": -32001,
        "data": "[1]: service is not available",
        "message": "Service is not connected"
    },
    "id": 0,
    "jsonrpc": "2.0"
}
```

Calling single-shot event will look like:

```bash
esafronov@local:~$ curl http://localhost:8080/ -H"X-Cocaine-JSON-RPC: 1" -d '{"jsonrpc": "2.0", "method": "storage.read", "params": ["collection", "key"], "id": 0}'
{"jsonrpc":"2.0","result":[{"event":"value","value":["Work-work"]}],"id":0}
```

...

### Examples

#### Planning
- [ ] Server back-pressure.

[rmp]: https://github.com/3Hren/msgpack-rust
[cocaine-framework-rust]: https://github.com/3Hren/cocaine-framework-rust
[jsonrpc]: http://www.jsonrpc.org/specification
[cocaine-tornado-proxy]: https://github.com/cocaine/cocaine-tools/tree/master/cocaine/proxy
[ci-img]: https://travis-ci.org/3Hren/cocaine-http-proxy.svg?branch=master
[ci-url]: https://travis-ci.org/3Hren/cocaine-http-proxy
