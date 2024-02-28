# Speech Recognition - Whisper Microphone

> Candle 0.4.0 git hash
>
> <https://github.com/huggingface/candle/tree/6400e1b0a08d594e1448d522a41bddc98c584313>

<https://github.com/huggingface/candle/tree/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/examples/whisper-microphone>

```sh
mkdir whisper_mic
cd whisper_mic
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
cpal \
hf-hub \
rand \
serde \
serde_json \
tokenizers \
tracing-chrome \
tracing-subscriber \
--features \
"anyhow/backtrace",\
"clap/derive",\
"hf-hub/tokio",\
"serde/derive",\
"tokenizers/onig"

# whisper_mic lib

curl -kLo src/lib.rs https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/src/lib.rs
sed -i '' 's/candle::/candle_core::/;s/, Tensor//;1,4d;29,$d' src/lib.rs

# whisper_mic bin

curl -kLo src/main.rs https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/examples/whisper-microphone/main.rs
sed -i '' '1,6d;s/candle::/candle_core::/;s/candle_examples::/whisper_mic::/;s|../whisper/||' src/main.rs

curl -kLo src/melfilters.bytes https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/examples/whisper/melfilters.bytes
curl -kLo src/melfilters128.bytes https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/examples/whisper/melfilters128.bytes

curl -kLo src/multilingual.rs https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/examples/whisper-microphone/multilingual.rs
sed -i '' 's/candle::/candle_core::/' src/multilingual.rs

# Running

cargo run --release

# Transcribing audio...
# language_token: None
# 0.0s -- 0.6s:  you
# 1.0s -- 2.5s:  you
# 2.0s -- 4.4s:  you
# 3.0s -- 6.0s:  Hello

cargo run --bin whisper_mic --release -- --quantized

# Transcribing audio...
# language_token: None
# 0.0s -- 0.6s:  you
# 1.0s -- 2.5s:
# 2.0s -- 4.4s:  Hello
# 3.0s -- 6.6s:  Hello
# 4.0s -- 8.5s:  Hello
```
