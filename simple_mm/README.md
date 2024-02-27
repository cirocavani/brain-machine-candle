# Simple Matrix Multiplication

<https://github.com/huggingface/candle/blob/main/README.md#get-started>

```sh
mkdir simple_mm
cd simple_mm
cargo init

echo '
[profile.release]
strip = true
lto = true
codegen-units = 1
opt-level = 3'>> \
Cargo.toml

sed -i '' 's/Hello, world!/Robotics Research Candle (ML framework)/' src/main.rs

cargo add candle-core --features "metal"

echo 'use candle_core::{Device, Tensor};

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let device = Device::Cpu;

    let a = Tensor::randn(0f32, 1., (2, 3), &device)?;
    let b = Tensor::randn(0f32, 1., (3, 4), &device)?;

    let c = a.matmul(&b)?;
    println!("{c}");
    Ok(())
}' \
> src/simple_mm_cpu.rs

echo '
[[bin]]
name = "simple_mm_cpu"
path = "src/simple_mm_cpu.rs"' \
>> Cargo.toml

# Running

cargo run --bin simple_mm_cpu --release

# [[-1.1883,  0.9027,  1.2739, -0.2812],
#  [ 0.3509,  0.2079, -0.9653, -0.1465]]
# Tensor[[2, 4], f32]

# Metal (Apple Silicon)

echo 'use candle_core::{Device, Tensor};

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let device = Device::new_metal(0)?;

    let a = Tensor::randn(0f32, 1., (2, 3), &device)?;
    let b = Tensor::randn(0f32, 1., (3, 4), &device)?;

    let c = a.matmul(&b)?;
    println!("{c}");
    Ok(())
}' \
> src/simple_mm_metal.rs

echo '
[[bin]]
name = "simple_mm_metal"
path = "src/simple_mm_metal.rs"' \
>> Cargo.toml

# Running

cargo run --bin simple_mm_metal --release

# [[ 1.7867,  0.2673,  0.0918, -1.8655],
#  [-0.5083,  1.2201,  0.4101,  0.5661]]
# Tensor[[2, 4], f32, metal:4294969067]
```
