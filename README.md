# Nucleo-F401RE Blinky (PlatformIO + STM32Cube)

This project demonstrates a basic GPIO blink using an STM32F401RE Nucleo board, with a custom PlatformIO + STM32Cube workflow on Linux (headless-friendly!).

---

## 🧰 Prerequisites

- STM32 Nucleo-F401RE board
- Python (for PlatformIO)
- PlatformIO CLI
- STM32CubeProgrammer (CLI version)

---

## 🛠️ Setup Steps

### 1. Python Environment for PlatformIO

```bash
python3 -m venv ~/pio-env/venv
source ~/pio-env/venv/bin/activate
pip install -U platformio
```

Add this alias to your `.bashrc` or `.zshrc`:

```bash
alias pio="~/pio-env/venv/bin/pio"
```

---

### 2. Install STM32CubeProgrammer (CLI)

- Download from: https://www.st.com/en/development-tools/stm32cubeprog.html
- Transfer `.zip` file to your Linux machine via `scp`
- Extract and install:

```bash
unzip en.stm32cubeprg-lin-vX-XX-X.zip
cd en.stm32cubeprg-lin-vX-XX-X/
chmod +x SetupSTM32CubeProgrammer-*.linux
./SetupSTM32CubeProgrammer-*.linux
```

It installs by default to:
```
~/STMicroelectronics/STM32Cube/STM32CubeProgrammer
```

Create a CLI alias:

```bash
alias stm32cli='~/STMicroelectronics/STM32Cube/STM32CubeProgrammer/bin/STM32_Programmer_CLI'
```

Or make it globally accessible:

```bash
sudo ln -s ~/STMicroelectronics/STM32Cube/STM32CubeProgrammer/bin/STM32_Programmer_CLI /usr/local/bin/stm32cli
```

---

### 3. Configure PlatformIO

#### `platformio.ini` Example:
```ini
[env:nucleo_f401re]
platform = ststm32
board = nucleo_f401re
framework = stm32cube

upload_protocol = custom
upload_command = stm32cli -c port=SWD mode=UR -e all -d $BUILD_DIR/firmware.elf -v -rst

build_flags += -Ilib/STM32F4xx_Nucleo
```

---

### 4. Add STM32Cube Example Code

We used GPIO toggle code from STM’s official repo:

📁 https://github.com/STMicroelectronics/STM32CubeF4

- Copy relevant files from:
  ```
  Projects/NUCLEO-F401RE/Examples/GPIO/GPIO_IOToggle/
  ```

- Especially:
  - `main.c`, `stm32f4xx_it.c`, `stm32f4xx_it.h`, `main.h`
  - `stm32f4xx_nucleo.h` from the BSP directory → place in `lib/STM32F4xx_Nucleo`

---

## 🚀 Upload Code

```bash
source ~/pio-env/venv/bin/activate
pio run -t upload
```

The `stm32cli` tool will perform a full chip erase and upload using SWD.

---

## 💡 Notes

- Ensure `JP6` is connected to power the target MCU via ST-LINK
- This setup avoids OpenOCD in favor of STM32CubeProgrammer for more reliable uploads
- Your blinky should toggle `LD2` on `PA5` at 500ms intervals

---

## ✅ Success!

You now have a fully functional STM32 development setup using PlatformIO and STM32Cube on a headless Linux system 🎉

