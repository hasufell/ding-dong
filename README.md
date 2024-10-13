
# Ding Dong - Nostr web client in haskell

WIP


# General
* The crypto part has not gone through any review, let alone audit. It's fine for playing, but just to be safe, assume the generated keys to be weak and leak easily.
* Many thanks to [terrorJack](https://github.com/TerrorJack/) and [amesgen](https://github.com/amesgen) and the rest of #haskell-wasm and #haskell channels for advice/troubleshooting.
* Parts of this project (mostly data records and their *JSON instances) are taken from [prolic](https://github.com/prolic/)'s Futr project.

# Current state

* Click on stuff and see what happens, that's the userguide.

* I have made some default "following", by choosing some popular accounts as well as some other accounts I run across by chance. That does not mean I endorse any of their posts.

* The feed is only displaying original posts and replies from the people you follow (i.e. you will not see content from people you are not following, unless some of the people you follow replies to such content).

# Architecture
* Haskell compiled to WebAssembly. Using Miso as a frontend library
* To get a better idea about how this combo works, first take a look at a more didactic example here https://github.com/tweag/ghc-wasm-miso-examples 
* WIP actual architecture of ding-dong

# Building
* start nix shell with haskell WASM buildtools 
    * I am using this one 
         https://gitlab.haskell.org/ghc/ghc-wasm-meta/-/archive/wasm-th-tmp/ghc-wasm-meta-wasm-th-tmp.tar.gz 
   
    * fire it up by running 
     ```
     nix shell https://gitlab.haskell.org/ghc/ghc-wasm-meta/-/archive/wasm-th-tmp/ghc-wasm-meta-wasm-th-tmp.tar.gz      --extra-experimental-features nix-command --extra-experimental-features flakes
     ```
 All the next steps assume you are inside the above shell
* you need to build libsecp256k1 C library for WASM platform first
    * download or clone from here https://github.com/bitcoin-core/secp256k1/
    * configure and install  (change  `some-directory` to where you want to library to land after make install)
     ```
     ./autogen.sh
     ./configure --prefix=some_directory CC=wasm32-wasi-clang --host=wasm32-unknown-wasi --enable-module-schnorrsig  CPPFLAGS=-D__OpenBSD__ CFLAGS="$CONF_CC_OPTS_STAGE2"
     ./make
     ./make install
     ```
* open ding-dong.cabal and change `extra-lib-dirs:` to `some_directory` (or wherever you installed libsecp256k1 to)
* go to `frontend` directory and do 
    * ```./build.sh && pushd dist; NIXPKGS_ALLOW_INSECURE=1 nix-shell -p python --run 'python -m SimpleHTTPServer'; popd```
         * this will build and deploy to localhost:8000
