# Speech Recognition - Whisper Microphone

<https://github.com/huggingface/candle/tree/main/candle-examples/examples/whisper-microphone>

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
# https://github.com/huggingface/candle/blob/main/candle-examples/Cargo.toml
# https://github.com/huggingface/candle/blob/main/Cargo.toml

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

# curl -kLo src/lib.rs https://github.com/huggingface/candle/row/32544a2ad691fa0fe6e77ade4f5b3232e8d311c1/candle-examples/src/lib.rs
curl -kLo src/lib.rs https://github.com/huggingface/candle/raw/main/candle-examples/src/lib.rs
sed -i '' 's/candle::/candle_core::/;s/, Tensor//;1,4d;29,$d' src/lib.rs

# whisper_mic bin

# curl -kLo src/main.rs https://github.com/huggingface/candle/blob/5e526abc8c0ecad2bd110a34d128e1e6d5333c68/candle-examples/examples/whisper-microphone/main.rs
curl -kLo src/main.rs https://github.com/huggingface/candle/raw/main/candle-examples/examples/whisper-microphone/main.rs
sed -i '' '1,6d;s/candle::/candle_core::/;s/candle_examples::/whisper_mic::/;s|../whisper/||' src/main.rs

curl -kLo src/melfilters.bytes https://github.com/huggingface/candle/raw/main/candle-examples/examples/whisper/melfilters.bytes
curl -kLo src/melfilters128.bytes https://github.com/huggingface/candle/raw/main/candle-examples/examples/whisper/melfilters128.bytes

curl -kLo src/multilingual.rs https://github.com/huggingface/candle/raw/main/candle-examples/examples/whisper-microphone/multilingual.rs
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
