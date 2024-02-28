# Speech Recognition - Whisper

> Candle 0.4.0 git hash
>
> <https://github.com/huggingface/candle/tree/6400e1b0a08d594e1448d522a41bddc98c584313>

<https://github.com/huggingface/candle/tree/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/examples/whisper>

```sh
mkdir whisper
cd whisper
cargo init

echo '
[profile.release]
strip = true
lto = true
codegen-units = 1
opt-level = 3'>> \
Cargo.toml

# dependencies
# https://github.com/huggingface/candle/blob/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/Cargo.toml
# https://github.com/huggingface/candle/blob/6400e1b0a08d594e1448d522a41bddc98c584313/Cargo.toml

cargo add candle-core --features "metal"
cargo add candle-nn --features "metal"
cargo add candle-transformers --features "metal"

cargo add \
anyhow \
byteorder \
clap \
hf-hub \
rand \
serde \
serde_json \
symphonia \
tokenizers \
tracing-chrome \
tracing-subscriber \
--features \
"anyhow/backtrace",\
"clap/derive",\
"hf-hub/tokio",\
"serde/derive",\
"symphonia/all",\
"tokenizers/onig"

# whisper lib

curl -kLo src/lib.rs https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/src/lib.rs
sed -i '' 's/candle::/candle_core::/;s/, Tensor//;1,4d;29,$d' src/lib.rs

# whisper bin

curl -kLo src/main.rs https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/examples/whisper/main.rs
sed -i '' '1,11d;s/candle::/candle_core::/;s/candle_examples::/whisper::/' src/main.rs

curl -kLo src/melfilters.bytes https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/examples/whisper/melfilters.bytes
curl -kLo src/melfilters128.bytes https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/examples/whisper/melfilters128.bytes

curl -kLo src/multilingual.rs https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/examples/whisper/multilingual.rs
sed -i '' 's/candle::/candle_core::/' src/multilingual.rs

curl -kLo src/pcm_decode.rs https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/examples/whisper/pcm_decode.rs

# Running

cargo run --release

# No audio file submitted: Downloading https://huggingface.co/datasets/Narsil/candle_demo/blob/main/samples_jfk.wav
# pcm data loaded 176000
# loaded mel: [1, 80, 3000]
# 0.0s -- 30.0s:  And so my fellow Americans ask not what your country can do for you ask what you can do for your country

cargo run --release -- --quantized

# No audio file submitted: Downloading https://huggingface.co/datasets/Narsil/candle_demo/blob/main/samples_jfk.wav
# pcm data loaded 176000
# loaded mel: [1, 80, 3000]
# 0.0s -- 30.0s:  And so my fellow Americans ask not what your country can do for you ask what you can do for your country
```
