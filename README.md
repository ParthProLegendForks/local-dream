<div align="center">

# Local Dream <img src="./assets/icon.png" width="32" alt="Local Dream">

**Android Stable Diffusion with Snapdragon NPU acceleration**  
_Also supports CPU/GPU inference_

<img src="./assets/demo1.jpg" alt="App Demo" width="800">

</div>

## About this Repo

This project is **now open sourced and completely free**. Hope you enjoy it!

If you like it, please consider [sponsor](https://github.com/xororz/local-dream?tab=readme-ov-file#-support-this-project) this project.

> [!NOTE]
> Currently focus on SD1.5 and SDXL models. SD2.1 models are no longer maintained due to poor quality and not popular. SDXL models are only available for Snapdragon 8 Gen 3, Snapdragon 8 Elite and Snapdragon 8 Elite Gen 5/ 8 Gen 5.
>
> Most users don't get how to properly use highres mode for SD1.5. Please check [here](#npu-high-resolution-support).
>
> Now you can import your own NPU models converted using our easy-to-follow [NPU Model Conversion Guide](./convert). For SD1.5 models, choose `_min` if you are using non-flagship chips, `_8gen1` for 8gen1, or `_8gen2` for 8gen2/3/4/5. We recommend checking the instructions on the original model page to set up prompts and parameters.
>
> You can join our [telegram group](https://t.me/local_dream) for discussion or help with testing.

## 🚀 Quick Start

1. **Install**: Install the app on your Android device
2. **Select Models**: Open the app and download the model(s) you want to use

## ✨ Features

- 🎨 **txt2img** - Generate images from text descriptions
- 🖼️ **img2img** - Transform existing images
- 🎭 **inpaint** - Redraw selected areas of images
- **custom models** - Import your own SD1.5 models for CPU (in app), SD1.5 and SDXL for NPU (follow the [conversion guide](./convert))
- **lora support** - Support adding LoRA weights to custom CPU models when importing.
- **prompt weights** - Emphasize certain words in prompts. E.g., `(masterpiece:1.5)`. Same format as [Automatic1111](https://github.com/AUTOMATIC1111/stable-diffusion-webui)
- **embeddings** - Support for custom embeddings in SafeTensor format. Please convert `pt` to `safetensors` first.
- **upscalers** - 4x upscaling with realesrgan_x4plus_anime_6b and 4x-UltraSharpV2_Lite

## 🔧 Build Instructions

> **Note**: Building on Linux/WSL is recommended. Other platforms are not verified.

### Prerequisites

The following tools are required for building:

- **Rust** - Install [rustup](https://rustup.rs/), then run:
  ```bash
  # rustup default stable
  rustup default 1.84.0 # Please use 1.84.0 for compatibility. Newer versions may cause build failures.
  rustup target add aarch64-linux-android
  ```
- **Ninja** - Build system
- **CMake** - Build configuration

### 1. Clone Repository

```bash
git clone --recursive https://github.com/xororz/local-dream.git
```

### 2. Prepare SDKs

1. **Prepare QNN SDK**: Obtain QNN SDK 2.39 from Qualcomm and extract it
2. **Prepare Android NDK**: Obtain Android NDK from Google and extract it
3. **Configure paths**:
   - Update `QNN_SDK_ROOT` in `app/src/main/cpp/CMakeLists.txt`
   - Update `ANDROID_NDK_ROOT` in `app/src/main/cpp/CMakePresets.json`

### 3. Build Libraries

**Linux**

```bash
cd app/src/main/cpp/
bash ./build.sh
```

<details>
<summary><strong>Windows</strong></summary>

```powershell
# Install dependencies if needed:
# winget install Kitware.CMake
# winget install Ninja-build.Ninja
# winget install Rustlang.Rustup

cd app\src\main\cpp\

# Convert patch file (install dos2unix if needed: winget install -e --id waterlan.dos2unix)
dos2unix SampleApp.patch
.\build.bat
```

</details>

<details>
<summary><strong>macOS</strong></summary>

```bash
# Install dependencies with Homebrew:
# brew install cmake rust ninja

# Fix CMake version compatibility
sed -i '' '2s/$/ -DCMAKE_POLICY_VERSION_MINIMUM=3.5/' build.sh
bash ./build.sh
```

</details>

### 4. Build APK

Open this project in Android Studio and navigate to:
**Build → Generate App Bundles or APKs → Generate APKs**

## Technical Implementation

### NPU Acceleration

- **SDK**: Qualcomm QNN SDK leveraging Hexagon NPU
- **Quantization**: W8A16 static quantization for optimal performance
- **Resolution**: For SD1.5, fixed 512×512 base model shape, more shapes available using patches. For SDXL, fixed 1024x1024 model shape
- **Performance**: Extremely fast inference speed

### CPU/GPU Inference

- **Framework**: Powered by MNN framework
- **Quantization**: W8 dynamic quantization
- **Resolution**: Flexible sizes (128×128, 256×256, 384×384, 512×512)
- **Performance**: Moderate speed with high compatibility

## NPU High Resolution Support

> [!IMPORTANT]
> Please note that quantized high-resolution(>768x768) SD1.5 models may produce images with poor layout. We recommend first generating at 512 resolution (optionally you can upscale it), then using the high-resolution model for img2img (which is essentially Highres.fix). The suggested img2img denoise_strength is around 0.8. After that, you can get images with better layout and details.

## Device Compatibility

### NPU Acceleration Support

SD1.5 compatible with devices featuring:

- **Snapdragon 8 Gen 1/8+ Gen 1**
- **Snapdragon 8 Gen 2**
- **Snapdragon 8 Gen 3**
- **Snapdragon 8 Elite**
- **Snapdragon 8 Elite Gen 5/8 Gen 5**
- Non-flagship chips with Hexagon V68 or above (e.g., Snapdragon 7 Gen 1, 8s Gen 3)

SDXL compatible with devices featuring:

- **Snapdragon 8 Gen 3**
- **Snapdragon 8 Elite**
- **Snapdragon 8 Elite Gen 5/8 Gen 5**

> **Note**: Other devices cannot download NPU models

### CPU/GPU Support

- **RAM Requirement**: ~2GB available memory
- **Compatibility**: Most Android devices from recent years

## Available Models

The following models are built-in and can be downloaded directly in the app:

<div align="center">

| Model                | Type  | CPU/GPU | NPU | Clip Skip |
| -------------------- | ----- | :-----: | :-: | :-------: |
| **SDXL Base 1.0**    | SDXL  |   ❌    | ✅  |     -     |
| **Illustrious v16**  | SDXL  |   ❌    | ✅  |     -     |
| **AnythingV5**       | SD1.5 |   ✅    | ✅  |     2     |
| **ChilloutMix**      | SD1.5 |   ✅    | ✅  |     1     |
| **Absolute Reality** | SD1.5 |   ✅    | ✅  |     2     |
| **QteaMix**          | SD1.5 |   ✅    | ✅  |     2     |
| **CuteYukiMix**      | SD1.5 |   ✅    | ✅  |     2     |

</div>

## 🎲 Seed Settings

Custom seed support for reproducible image generation:

- **CPU Mode**: Seeds guarantee identical results across different devices with same parameters
- **GPU Mode**: Results may differ from CPU mode and can vary between different devices
- **NPU Mode**: Seeds ensure consistent results only on devices with identical chipsets

## Credits & Acknowledgments

### C++ Libraries

- **Qualcomm QNN SDK** - NPU model execution
- **[alibaba/MNN](https://github.com/alibaba/MNN/)** - CPU model execution
- **[xtensor-stack](https://github.com/xtensor-stack)** - Tensor operations & scheduling
- **[mlc-ai/tokenizers-cpp](https://github.com/mlc-ai/tokenizers-cpp)** - Text tokenization
- **[yhirose/cpp-httplib](https://github.com/yhirose/cpp-httplib)** - HTTP server
- **[nothings/stb](https://github.com/nothings/stb)** - Image processing
- **[facebook/zstd](https://github.com/facebook/zstd)** - Model compression
- **[nlohmann/json](https://github.com/nlohmann/json)** - JSON processing

### Android Libraries

- **[square/okhttp](https://github.com/square/okhttp)** - HTTP client
- **[coil-kt/coil](https://github.com/coil-kt/coil)** - Image loading & processing
- **[MoyuruAizawa/Cropify](https://github.com/MoyuruAizawa/Cropify)** - Image cropping
- **AOSP, Material Design, Jetpack Compose** - UI framework

### Models

- **[CompVis/stable-diffusion](https://github.com/CompVis/stable-diffusion)** and all other model creators
- **[xinntao/Real-ESRGAN](https://github.com/xinntao/Real-ESRGAN)** - Image upscaling
- **Kim2091/UltraSharpV2** - Image upscaling
- **[bhky/opennsfw2](https://github.com/bhky/opennsfw2)** - NSFW content filtering

---

## 💖 Support This Project

If you find Local Dream useful, please consider supporting its development:

### What Your Support Helps With:

- **Additional Models** - More AI model integrations
- **New Features** - Enhanced functionality and capabilities
- **Bug Fixes** - Continuous improvement and maintenance

<a href="https://ko-fi.com/xororz">
    <img height="36" style="border:0px;height:36px;" src="https://storage.ko-fi.com/cdn/kofi2.png?v=3" border="0" alt="Buy Me a Coffee at ko-fi.com" />
</a>
<a href="https://afdian.com/a/xororz">
    <img height="36" style="border-radius:12px;height:36px;" src="https://pic1.afdiancdn.com/static/img/welcome/button-sponsorme.jpg" alt="在爱发电支持我" />
</a>

Your sponsorship helps maintain and improve Local Dream for everyone!
