# Language Model - Phi

> Candle 0.4.0 git hash
>
> <https://github.com/huggingface/candle/tree/6400e1b0a08d594e1448d522a41bddc98c584313>

<https://github.com/huggingface/candle/tree/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/examples/phi>

```sh
mkdir phi
cd phi
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
clap \
csv \
hf-hub \
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

# phi lib

curl -kLo src/lib.rs https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/src/lib.rs
sed -i '' 's/candle::/candle_core::/;s/, Tensor//;1,4d;30,119d' src/lib.rs

# phi bin

curl -kLo src/main.rs https://github.com/huggingface/candle/raw/6400e1b0a08d594e1448d522a41bddc98c584313/candle-examples/examples/phi/main.rs
sed -i '' '1,6d;s/candle::/candle_core::/;s/candle_examples::/phi::/' src/main.rs

# Running

# --cpu -> metal is too slow at the moment
cargo run --release -- \
--cpu --model 2 \
--prompt "A skier slides down a frictionless slope of height 40m and length 80m. What's the skier speed at the bottom?"

# avx: false, neon: true, simd128: false, f16c: false
# temp: 0.00 repeat-penalty: 1.10 repeat-last-n: 64
# retrieved the files in 19.286792ms
# loaded the model in 17.50672675s
# starting the inference loop
# A skier slides down a frictionless slope of height 40m and length 80m. What's the skier speed at the bottom?

# Solution:
# The potential energy of the skier is converted into kinetic energy as it slides down the slope. The formula for potential energy is mgh, where m is mass, g is acceleration due to gravity (9.8 m/s^2), and h is height. Since there's no friction, all the potential energy is converted into kinetic energy at the bottom of the slope. The formula for kinetic energy is 1/2mv^2, where v is velocity. We can equate these two formulas:
# mgh = 1/2mv^2
# Solving for v, we get:
# v = sqrt(2gh)
# Substituting the given values, we get:
# v = sqrt(2*9.8*40) = 28 m/s
# Therefore, the skier speed at the bottom of the slope is 28 m/s.
# 188 tokens generated (1.82 token/s)

cargo run --release -- \
--cpu --quantized --model 2 \
--prompt "A skier slides down a frictionless slope of height 40m and length 80m. What's the skier speed at the bottom?"

# avx: false, neon: true, simd128: false, f16c: false
# temp: 0.00 repeat-penalty: 1.10 repeat-last-n: 64
# retrieved the files in 818µs
# loaded the model in 531.378583ms
# starting the inference loop
# A skier slides down a frictionless slope of height 40m and length 80m. What's the skier speed at the bottom?

# Solution:
# We can use the conservation of energy principle to solve this problem. The potential energy at the top is equal to the kinetic energy at the bottom, since there is no friction or air resistance. Therefore, we have:

# Potential energy = Kinetic energy
# mgh = (1/2)mv^2
# where m is the mass of the skier, g is the acceleration due to gravity, h is the height of the slope, and v is the speed at the bottom. Solving for v, we get:

# v = sqrt(2gh)
# Plugging in the values, we get:

# v = sqrt(2*9.8*40) = 24.25 m/s

# Therefore, the skier speed at the bottom is 24.25 m/s.


# 177 tokens generated (13.29 token/s)
```
