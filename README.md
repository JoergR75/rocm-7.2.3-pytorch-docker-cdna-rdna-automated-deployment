# ⚙️ ROCm 7.2.3 Stack + OpenCL 2.x + PyTorch (Stable) + Transformers + Docker Setup

[![ROCm](https://img.shields.io/badge/ROCm-7.2.3-ff6b6b?logo=amd)](https://rocm.docs.amd.com/en/docs-7.2.3/about/release-notes.html)
[![PyTorch](https://img.shields.io/badge/PyTorch-2.11.0%20%28Stable%29-ee4c2c?logo=pytorch)](https://pytorch.org/get-started/locally/)
[![Docker](https://img.shields.io/badge/Docker-29.4.x-blue?logo=docker)](https://www.docker.com/)
[![Ubuntu](https://img.shields.io/badge/Ubuntu-22.04%20%7C%2024.04-e95420?logo=ubuntu)](https://ubuntu.com/download/server)
[![AMD Radeon AI PRO R9700](https://img.shields.io/badge/AMD-RDNA4%20Radeon(TM)%20AI%20PRO%20R9700-8B0000?logo=amd)](https://www.amd.com/en/products/graphics/workstations/radeon-ai-pro/ai-9000-series/amd-radeon-ai-pro-r9700.html)
[![AMD Ryzen AI](https://img.shields.io/badge/AMD-Ryzen%20AI%20-8B0000?logo=amd)](https://ryzen-ai.com/en/)
[![AMD CDNA MI300 Series](https://img.shields.io/badge/AMD-CDNA%20Instint(TM)%20Architecture-8B0000?logo=amd)](https://www.amd.com/en/technologies/cdna.html)

## 📌 Overview
The script provisions a fully automated, non-interactive AMD GPU software development environment for AI and HPC software engineering on **Ubuntu 22.04** and **24.04**, centered on **ROCm 7.2.3** and **PyTorch** Stable.

At the platform layer, it installs the AMD GPU kernel driver (**amdgpu-dkms**) and the ROCm 7.2.3 runtime, including **HIP** and **OpenCL 2.x**, ensuring compatibility across **CDNA1**, **CDNA2**, **CDNA3** **CDNA4**, **RDNA3**, **RDNA4** GPUs and **Strix APUs**. The script configures **OpenCL ICD** paths, user group permissions (video, render, sudo), and kernel headers required for compiling GPU-accelerated native extensions.

For the AI framework layer, the script installs **PyTorch 2.11 Stable** (**ROCm 7.2.3 wheels**) directly from the official PyTorch ROCm nightly repository, enabling access to the latest HIP backends, kernel fusion paths, and compiler features. It complements PyTorch with Transformers, Accelerate, Diffusers, Datasets, SentencePiece, and supporting Python build tooling, allowing immediate development, testing, and profiling of modern LLM, diffusion, and data-parallel workloads.

The developer toolchain is rounded out with C/C++ build and system utilities required for low-level GPU software engineering and extension development, including **cmake**, **libstdc++ dev headers**, **git** / **git-lfs**, **libmsgpack**, and **rocm-bandwidth-test** for validating PCIe and HBM bandwidth. Runtime observability and system inspection are supported via htop, ncdu, and ROCm diagnostics (rocminfo, rocm-smi, amd-smi).

A validation script is generated to verify end-to-end GPU availability, confirming ROCm detection, PyTorch HIP enablement, GPU enumeration, and successful on-device tensor execution.

The setup is fully **non-interactive** and optimized for both **desktop** and **server** deployments. In addition it checks whether ROCm or PyTorch (installed via pip) is already present on the system.
If an existing ROCm installation is detected, it removes ROCm and related packages to ensure a clean environment. It also **detects** and **uninstalls** any PyTorch packages (including ROCm-specific builds) to prevent version conflicts before proceeding with a fresh installation.

---

## 🖥️ Supported Platforms

| **Component**      | **Supported Versions**                                |
|---------------------|------------------------------------------------------|
| **OS**            | Ubuntu 22.04.x (Jammy Jellyfish), Ubuntu 24.04.x (Noble Numbat) |
| **Kernels** tested       | 5.15.0-171 (22.04.5) • 6.8.0-110 (24.04.4)                       |
| **GPUs**          | AMD **CDNA1** • **CDNA2** • **CDNA3** • **CDNA4** • **RDNA3** • **RDNA4**              |
| **APUs**        | AMD **Strix** • **Strix Halo**                                       |
| **ROCm**          | 7.2.3                                                |
| **PyTorch**       | torch 2.11.0+rocm7.2, torchvision 0.26.0+rocm7.2       |       |

**⚠️ Note**: **Ubuntu 20.04.x (Focal Fossa)** is **not supported**. The last compatible ROCm version for 20.04 is **6.4.0**.

---

## ⚡ Features
- Automated **ROCm GPU drivers + HIP + OpenCL SDK** installation
- **PyTorch ROCm Stable** with GPU acceleration
- Preinstalled **Transformers**, **Accelerate**, **Diffusers**, and **Datasets**
- Integrated **Docker environment** with ROCm GPU passthrough
- **vLLM Docker images** for **RDNA4** & **CDNA**
- Optimized for **AI workloads**, **LLM inference**, and **model fine-tuning**

---

## 🚀 Installation

### 1️⃣ **System preperation**
Install **Ubuntu 22.04.5 LTS** or **Ubuntu 24.04.4 LTS** (Server or Desktop version).

**Recommendations:**
- Use a fresh Ubuntu installation if possible
- Assign the full storage capacity during installation
- Install **OpenSSH** for remote SSH management
- The script automatically checks the system for installed versions of ROCm, PyTorch, and Docker, and removes them if found
  - On a fresh Ubuntu installation, the script automatically skips the deinstallation routine, as illustrated below
    <img width="543" height="160" alt="image" src="https://github.com/user-attachments/assets/3492a5e6-86f8-4b01-88fa-509f54db0f0e" />
  - If an existing version is detected, it will be deleted, regardless of whether it is the same or an older release.
    <img width="590" height="298" alt="image" src="https://github.com/user-attachments/assets/6323bb70-5f3e-46d3-bf40-e8949d05c5a8" />

- SBIOS settings:
  - When using Linux, you should disable Secure Boot
  - On WRX80 and WRX90 motherboard solutions, make sure SR-IOV is enabled — there are known issues with Ubuntu Linux detecting the network otherwise

### 2️⃣ **Download the Script from the Repository**
```bash
wget https://raw.githubusercontent.com/JoergR75/rocm-7.2.3-pytorch-docker-cdna-rdna-automated-deployment/refs/heads/main/script_module_ROCm_723_Ubuntu_22.04-24.04_pytorch_server.sh
```

<img width="2510" height="479" alt="image" src="https://github.com/user-attachments/assets/880a50ff-9f81-47b4-80d2-2d8e35156a4f" />

### 3️⃣ **Run the Installer**
```bash
bash script_module_ROCm_723_Ubuntu_22.04-24.04_pytorch_server.sh
```
**⚠️ Note**: Entering the user password may be required.

<img width="1584" height="594" alt="image" src="https://github.com/user-attachments/assets/4c8080d1-fde5-4f0f-9d08-848f724d7a7f" />

The installation takes ~15 minutes depending on internet speed and hardware performance.

### 4️⃣ **Reboot the System**
After the successful installation, press "y" to reboot the system and activate all installed components.

<img width="2512" height="897" alt="image" src="https://github.com/user-attachments/assets/24345b0c-79ae-40bd-8e89-62e28a1dc43e" />

## 🧪 Testing ROCm + PyTorch

After rebooting, verify your setup:

This script creates a simple diagnostic python file (test.py) to verify that PyTorch with ROCm support is correctly installed and working.

What it does:

- Shows the CPU and installed memory
- Prints the ROCm, PyTorch and Transformers version.
- Checks if ROCm is available and how many GPUs are detected.
- Displays the name of the first GPU (if available).
- Creates two random 3×3 tensors directly on the GPU (if available).
- Performs a simple tensor addition operation on the GPU.
- Prints confirmation that the operation was successful and shows the result.

Example usage:
```bash
python3 test.py
```
Expected Output Example:

<img width="1235" height="719" alt="image" src="https://github.com/user-attachments/assets/683618e4-cf06-44a0-928c-2b9f2ef4241f" />

More details about the ROCm driver version can be reviewed:
```bash
apt show rocm-libs -a
```

<img width="2508" height="717" alt="image" src="https://github.com/user-attachments/assets/6ade4a74-deea-4c4e-932d-b3e82bdc8d10" />

## 📶 ROCm Bandwidth Test

**AMD’s ROCm Bandwidth Test utility** with the **`tb p2p` (Peer-to-peer device memory bandwidth test)** flag runs a complete set of bandwidth diagnostics.

### What it does

`rocm-bandwidth-test` is a diagnostic tool included in ROCm that measures **memory bandwidth performance** between:

- Host (CPU) ↔ GPU(s)  
- GPU ↔ GPU (if multiple GPUs are installed)  
- GPU internal memory  

### `tb p2p` option

Using the `--run tb p2p` flag runs **Peer-to-peer device memory bandwidth test**, including:

- **Host-to-Device (H2D)** bandwidth  
- **Device-to-Host (D2H)** bandwidth  
- **Device-to-Device (D2D)** bandwidth (for multi-GPU)  
- **Bidirectional / concurrent** bandwidth tests  

Run the P2P test
```bash
cd /opt/rocm/bin && ./rocm_bandwidth_test plugin --run tb p2p
```

### Output

The tool prints results in a **matrix format** showing bandwidth (GB/s) between every device pair.

<img width="983" height="1179" alt="{6EAC522F-550D-4881-9C78-11B3A90A555D}" src="https://github.com/user-attachments/assets/039f0f87-79b8-4dd0-856b-d959025b27a4" />

More details about the setup can be verified by
```bash
cd /opt/rocm/bin && ./rocm_bandwidth_test plugin --run tb
```

<img width="861" height="275" alt="{4103D9C7-2ECE-42CF-A231-DC1D7004C7BF}" src="https://github.com/user-attachments/assets/e0a1efaf-9c5c-4c6c-b2d9-8ff14cf1b623" />

⚠️ **Caution:**  
Make sure **"Re-Size BAR"** is enabled in the **SBIOS**.  
If it is disabled, **P2P** will be deactivated, as shown below:

<img width="977" height="777" alt="{FD9B95A3-BEFA-4857-8BBB-8D06A90108F2}" src="https://github.com/user-attachments/assets/cc148322-45b3-4164-b215-521276749f9d" />

More details about the setup can be verified by
```bash
cd /opt/rocm/bin && ./rocm_bandwidth_test plugin --run tb
```

<img width="904" height="274" alt="{3F58A790-E952-4BD9-9F0A-B99FD8F0B081}" src="https://github.com/user-attachments/assets/28b1808a-8216-4d7c-b1ea-db599f140056" />

### ⚙️ How to Enable **Re-Size BAR** in SBIOS (example ASRock WRX90 evo)

1. Enter **SBIOS**

<img width="1007" height="760" alt="{F9649127-0F1F-4E14-8008-1F3782FBBDEF}" src="https://github.com/user-attachments/assets/9685c1a4-ecab-4fea-8e91-dd21b9869c7e" />

3. Navigate to **Advanced**

<img width="1018" height="761" alt="{135D3B4C-0732-4652-A3C0-1224D275A515}" src="https://github.com/user-attachments/assets/b1cdc3ce-b526-4cdc-b44f-71d1119cf6d7" />

5. Go to **PCI Subsystem Settings** and change **Re-Size BAR Support** to **Enable** 

<img width="1016" height="761" alt="{3C54C3DA-8B82-483C-AEA5-D0A511508780}" src="https://github.com/user-attachments/assets/60536e2b-e59f-4486-a1fc-ab3ff33a3cd8" />

## 🐋 Docker Integration

The script sets up a Docker environment with GPU passthrough support via ROCm.

Check Docker installation and version
```bash
docker -v
```

<img width="467" height="55" alt="image" src="https://github.com/user-attachments/assets/61400598-6549-4422-8b4a-f752c3079f70" />

### 🤖 vLLM Docker Images

To use vLLM optimized for RDNA4 and CDNA:

Use the container image you need.

**RDNA4** architecture running on Ubuntu 24.04
```bash
docker pull vllm/vllm-openai-rocm:nightly-0c620d2e083a49ba40c2a5df318fa246d7e7a59b
```

<img width="763" height="590" alt="image" src="https://github.com/user-attachments/assets/7e13abbb-73c4-4d95-9204-2f240837c625" />

Further vLLM Docker versions for RDNA4 can be verified on Docker Hub:  
https://hub.docker.com/r/rocm/vllm-dev/tags?name=navi

or for **CDNA** architecture
```bash
sudo docker pull rocm/vllm:latest
```

Run vLLM with all available AMD GPU access (example for RDNA4 on Ubuntu 24.04)
```bash
sudo docker run -it \
    --device=/dev/kfd \
    --device=/dev/dri \
    --security-opt seccomp=unconfined \
    --group-add video \
    --entrypoint /bin/bash \
    vllm/vllm-openai-rocm:nightly-0c620d2e083a49ba40c2a5df318fa246d7e7a59b
```

<img width="618" height="124" alt="image" src="https://github.com/user-attachments/assets/fa7e620f-d082-4c0e-802b-a3be7f9fbab7" />

With `rocm-smi`, you can verify all available GPUs (in this case, 2× Radeon AI PRO R9700 GPUs).

<img width="812" height="198" alt="image" src="https://github.com/user-attachments/assets/56102532-45d9-4d4a-a400-17f86aaefed7" />

or `amd-smi`

<img width="625" height="334" alt="image" src="https://github.com/user-attachments/assets/23fdac90-720b-4ab8-9d50-b2e67bd90cf5" />

If you need to add a specific GPU, you can use the **passthrough** option.  
First, verify the available GPUs in the `/dev/dri` directory (host).
```bash
cd /dev/dri && ls
```

<img width="460" height="49" alt="image" src="https://github.com/user-attachments/assets/84b7c3b5-5a54-4132-8d87-9a16ad9ef337" />

Let's choose **GPU2**, also referred to as **"card2"** or **"renderD129"**.
```bash
sudo docker run -it \
    --device=/dev/kfd \
    --device=/dev/dri/card2 \
    --device=/dev/dri/renderD129 \
    --security-opt seccomp=unconfined \
    --group-add video \
    rocm/vllm-dev:rocm7.2.1_navi_ubuntu24.04_py3.12_pytorch_2.9_vllm_0.16.0
```
GPU2 has been added to the container

<img width="807" height="289" alt="image" src="https://github.com/user-attachments/assets/ef877968-4995-431a-a23d-49bc822053ba" />

## How to Save a Modified Docker Container

1️⃣ Open your container and modify it as needed (e.g., install packages, change configurations).

**⚠️ Note: Do not stop or close the container!**

2️⃣ Open another terminal (CLI) window.

3️⃣ Verify the running and stopped containers:
```bash
sudo docker ps -a
```

<img width="844" height="126" alt="image" src="https://github.com/user-attachments/assets/b879c0a2-a071-4307-adba-0da66534fd15" />

4️⃣ In this example, we want to save the running container `loving_wescoff` as a new image named `rocm/vllm-dev:rocm7.2.1_navi_ubuntu24.04_py3.12_pytorch_2.9_vllm_0.16.0_2`:
```bash
docker commit loving_wescoff rocm/vllm-dev:rocm7.2.1_navi_ubuntu24.04_py3.12_pytorch_2.9_vllm_0.16.0_2
```

<img width="842" height="46" alt="image" src="https://github.com/user-attachments/assets/968c0c38-20c9-4cac-8928-c4a7797e15a7" />

5️⃣ Verify that the new image was created successfully:
```bash
sudo docker images
```

<img width="855" height="138" alt="image" src="https://github.com/user-attachments/assets/86a03be1-e4e2-4e88-8a28-6d362fb14d7b" />

6️⃣ Start the new container with one GPU (renderD129):
```bash
sudo docker run -it \
    --device=/dev/kfd \
    --device=/dev/dri/card2 \
    --device=/dev/dri/renderD129 \
    --security-opt seccomp=unconfined \
    --group-add video \
    rocm/vllm-dev:rocm7.2.1_navi_ubuntu24.04_py3.12_pytorch_2.9_vllm_0.16.0_2
```

<img width="828" height="395" alt="image" src="https://github.com/user-attachments/assets/e7349f84-b08b-4500-988d-19aff77025be" />
