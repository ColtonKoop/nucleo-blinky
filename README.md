# Nucleo-F401RE Blinky (PlatformIO + STM32Cube)

This project demonstrates a basic GPIO blinky using the STM32F401RE Nucleo board, using a custom **PlatformIO + STM32Cube** workflow that's compatible with headless Linux environments and minimal IDEs.

---

## 🧰 Prerequisites

- STM32 Nucleo-F401RE board
- Python 3
- PlatformIO (CLI)
- STM32CubeProgrammer (CLI)
- Git (with submodule support)

---

## 🛠️ Setup

### 1. Setup Python & PlatformIO

```bash
python3 -m venv ~/pio-env/venv
source ~/pio-env/venv/bin/activate
pip install -U platformio
```

Optional alias (add to `~/.bashrc`, `~/.zshrc`, etc):

```bash
alias pio="~/pio-env/venv/bin/pio"
```

---

### 2. Install STM32CubeProgrammer (CLI)

- Download from ST: https://www.st.com/en/development-tools/stm32cubeprog.html
- Transfer the installer to your Linux machine (e.g. via `scp`)
- Install it:

```bash
unzip en.stm32cubeprg-lin-*.zip
cd en.stm32cubeprg-lin-*/
chmod +x SetupSTM32CubeProgrammer-*.linux
./SetupSTM32CubeProgrammer-*.linux
```

Default install path:  
`~/STMicroelectronics/STM32Cube/STM32CubeProgrammer`

Alias for CLI usage:

```bash
alias stm32cli='~/STMicroelectronics/STM32Cube/STM32CubeProgrammer/bin/STM32_Programmer_CLI'
```

Or make it globally accessible:

```bash
sudo ln -s ~/STMicroelectronics/STM32Cube/STM32CubeProgrammer/bin/STM32_Programmer_CLI /usr/local/bin/stm32cli
```

---

### 3. Clone This Repo with Submodules

```bash
git clone --recurse-submodules https://github.com/ColtonKoop/nucleo-blinky.git
cd nucleo-blinky
```

Or, if already cloned:

```bash
git submodule update --init --recursive
```

---

### 4. Submodules Used

We use a **minimal driver set** as git submodules for maintainability and reuse:

```text
lib/Drivers/
├── CMSIS/Device/ST/STM32F4xx/         (cmsis_device_f4.git)
├── CMSIS/Include/                     (from framework)
├── BSP/STM32F4xx-Nucleo/             (stm32f4xx-nucleo-bsp.git)
├── BSP/Components/Common/            (stm32-bsp-common.git)
```

These are added as submodules like this:

```bash
git submodule add https://github.com/STMicroelectronics/cmsis_device_f4.git lib/Drivers/CMSIS/Device/ST/STM32F4xx
git submodule add https://github.com/STMicroelectronics/stm32f4xx-nucleo-bsp.git lib/Drivers/BSP/STM32F4xx-Nucleo
git submodule add https://github.com/STMicroelectronics/stm32-bsp-common.git lib/Drivers/BSP/Components/Common
```

---

### 5. PlatformIO Configuration

`platformio.ini`:

```ini
[env:nucleo_f401re]
platform = ststm32
board = nucleo_f401re
framework = stm32cube

upload_protocol = custom
upload_command = stm32cli -c port=SWD mode=UR -e all -d $BUILD_DIR/firmware.elf -v -rst

build_flags =
    -Iinclude
    -Ilib/Drivers/CMSIS/Include
    -Ilib/Drivers/CMSIS/Device/ST/STM32F4xx/Include
    -Ilib/Drivers/BSP/STM32F4xx-Nucleo
    -Ilib/Drivers/BSP/Components/Common
```

> ❗ No `build_src_filter` is used — PlatformIO handles source file inclusion automatically.

---

### 6. Example Code Setup

We use STM's GPIO example as the base:

- Copied from STM32CubeF4 → `Projects/NUCLEO-F401RE/Examples/GPIO/GPIO_IOToggle/`
- Key files placed in project structure:
  - `src/main.c`
  - `src/stm32f4xx_it.c`
  - `include/stm32f4xx_it.h`, `include/main.h`
- CMSIS template `system_stm32f4xx.c` included in `src/`

---

## 🚀 Upload Firmware

Activate your environment and upload:

```bash
source ~/pio-env/venv/bin/activate
pio run -t upload
```

This will use `STM32_Programmer_CLI` to flash the firmware via SWD.

---

## 🧼 Optional .gitignore

Here’s a good `.gitignore`:

```
.pio/
.vscode/
.idea/
*.pyc
*.swp
.DS_Store
.env
venv/
__pycache__/
```

---

## ✅ Success!

If all went well, you should see LD2 (PA5) blinking at 500ms intervals 🎉

This setup is now:
- **Modular** (with git submodules for drivers)
- **Headless-friendly** (no GUI dependencies)
- **Ready for expansion** to more STM32Cube projects

---

## 📌 Future Plans

- Add more BSP component submodules as needed
- Setup CMSIS-DAP or JLink support
- Integrate debugging workflow via GDB

---

Happy blinking! 💡
