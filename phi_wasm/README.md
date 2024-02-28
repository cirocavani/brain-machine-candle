# Language Model - Phi (WASM)

> Candle 0.4.0 git hash
>
> <https://github.com/huggingface/candle/tree/6400e1b0a08d594e1448d522a41bddc98c584313>

<https://github.com/huggingface/candle/tree/6400e1b0a08d594e1448d522a41bddc98c584313/candle-wasm-examples/phi>

```sh
mkdir phi_wasm
cd phi_wasm
cargo init

echo '
[profile.release]
strip = true
lto = true
codegen-units = 1
opt-level = "s"'>> \
Cargo.toml

cargo add \
candle-core \
candle-nn \
candle-transformers \
console_error_panic_hook \
js-sys \
serde \
serde_json \
wasm-bindgen \
--features \
"serde/derive"

cargo add \
tokenizers \
--no-default-features \
--features \
"tokenizers/unstable_wasm"

curl -kLo src/lib.rs https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-wasm-examples/phi/src/lib.rs
curl -kLo src/main.rs https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-wasm-examples/phi/src/bin/m.rs
sed -i '' 's/candle::/candle_core::/;s/candle_wasm_example_phi::/phi_wasm::/' src/main.rs

curl -kLO https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-wasm-examples/phi/build-lib.sh
sed -i '' 's|../../||;s/m.wasm/phi_wasm.wasm/' build-lib.sh

curl -kLO https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-wasm-examples/phi/index.html
curl -kLO https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-wasm-examples/phi/phiWorker.js
sed -i '' 's|build/m.js|build/phi_wasm.js|' phiWorker.js

rustup target add wasm32-unknown-unknown
cargo install wasm-bindgen-cli http-server

bash build-lib.sh

http-server --cors

# other terminal
open -a firefox -n --args --devtools --new-window http://127.0.0.1:7878/index.html?model=phi_2_0_q4k
```
