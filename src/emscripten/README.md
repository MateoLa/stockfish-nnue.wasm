<div align="center">

	[![Stockfish][https://stockfishchess.org/images/logo/icon_128x128.png]]

	<h3>Stockfish</h3>

	A free and strong UCI chess engine. <br>	
	Analyzes chess positions and computes optimal moves.

</div>

#### Description of the universal chess interface - UCI protocol

It is a command line protocol.

You will need to write to stdin ([UCI commands](https://backscattering.de/chess/uci/)) and listen to stdout.

Compiling stockfish generates a binary file `make build ARCH=x86-64-avx2 > build.log 2>&1`

#### WebAssembly 

Wasm is a binary instruction format for a stack-based virtual machine.

The code can be run in a modern browser to build a virtual machine which allows developers to run compiled codes (C++, Rust, etc.) on the client.

WebAssembly is designed to complement and run alongside JavaScript, sharing functionality between them.


### Prerequisites

* Install Emscripten

* Set working directory to stockfish/src/

* Build  `make -C .. emscripten_build ARCH=wasm` or `make -j build ARCH=wasm`

Build options can be set in /src/emscripten/Makefile

A non NNUE version is compiled with this values:
```sh
wasm_simd = yes
embedded_nnue = no
minify_js = no
assertion = no 
```

If you use the "minify_js" option, the version is compiled with warnings.




## Run

- Node

```
# Check node version
$ node -e 'console.log(`node: ${process.version}\nv8: ${process.versions.v8}`)'
node: v16.5.0
v8: 9.1.269.38-node.20

# UCI console
$ node public/uci.js
```

- Browser

```
# Start server for http://localhost:5000/test-puppeteer.html
$ npm run serve

# Run UCI command inside of headless browser
$ npm install puppeteer
$ node public/uci-puppeteer.js bench # not interactive
```

## Build/Run in Docker

```
# Build
$ DOCKER_USER=$(id -u):$(id -g) docker-compose run emscripten bash
> make -C .. emscripten_build ARCH=wasm

# Run Node
$ docker-compose run node bash
> node public/uci.js

# Run headless browser
$ docker-compose run browser bash
> node public/uci-puppeteer.js bench
```

## Testing

See `.github/workflows/ci.yml`

```
$ UCI_EXE="node public/uci.js"           bash tests/bench-nps.sh
$ UCI_EXE="node public/uci.js"           bash tests/bench-signature.sh
$ UCI_EXE="node public/uci.js"           bash tests/stress.sh

$ UCI_EXE="node public/uci-puppeteer.js" bash tests/bench-nps.sh
$ UCI_EXE="node public/uci-puppeteer.js" bash tests/bench-signature.sh
$ UCI_EXE="node public/uci-puppeteer.js" bash tests/stress.sh
```

## Misc

- Publish npm package

```
$ npm run build:all
$ npm publish ./public
```

## Related projects

- https://github.com/hi-ogawa/stockfish-nnue-wasm-demo

  - Code for https://stockfish-nnue-wasm.vercel.app

- https://github.com/hi-ogawa/stockfish-nnue-wasm-match

  - Simple engine match testing using Github Actions

- https://github.com/niklasf/stockfish.wasm

  - Many emscripten related patches are originally from niklasf's port

- https://github.com/ornicar/lila

  - Lichess's client side analysis integration code is found at [`ui/ceval`](https://github.com/ornicar/lila/blob/master/ui/ceval/src/ctrl.ts).


## Acknowledgements

Thanks to the [Stockfish](https://github.com/official-stockfish/Stockfish) team and all its contributors.

The nnue-wasm compilation method (emscripten directory && Makefile) are based on [Hiroshi Ogawa](https://github.com/hi-ogawa/Stockfish) github repo.