# System-on-Chip (SoC) Design Fundamentals and the BabySoC Learning Platform 💡

## 1. What is a System-on-Chip (SoC)?

![381888361-a3428bc5-1ddd-4bdc-b3a6-10a74205c8d8](https://github.com/user-attachments/assets/221e5a26-e52f-4468-aa68-cd93e9623cd1)


A **System-on-Chip (SoC)** is a complete, integrated electronic system built onto a single integrated circuit (IC) or "chip." It functions as a "mini-computer" on a single silicon die, incorporating all the major components required for a specific electronic device to operate.

### Why are SoCs Essential?

SoCs are the backbone of modern portable and embedded electronics (smartphones, tablets, smartwatches, IoT devices) because they offer critical advantages over traditional multi-chip designs:

* **Space Saving:** Consolidating components onto one chip makes devices smaller and more portable.
* **Energy Efficiency:** The close proximity of components reduces power consumption, which is vital for battery-operated devices.
* **High Performance:** Data travels shorter distances, enabling faster processing.
* **Cost-Effectiveness:** Manufacturing a single, complex chip is often cheaper than assembling multiple discrete components.
* **Reliability:** Fewer points of failure lead to generally more dependable devices.

---

## 2. Components of a Typical SoC

A complex SoC integrates a variety of Intellectual Property (IP) blocks to handle its various functions. The primary components generally fall into these categories:

| Component | Role | Example |
| :--- | :--- | :--- |
| **CPU (Central Processing Unit)** | The **brain**; executes instructions, handles calculations, and manages data processing. | **RVMYTH** in BabySoC |
| **Memory** | Stores data and instructions, both temporarily and permanently. | RAM (temporary data), Flash Storage (permanent data) |
| **Peripherals/I/O Ports** | Interface with the external world and other on-chip components. | **Digital-to-Analog Converter (DAC)** in BabySoC, USB, Camera interfaces, Wi-Fi modules |
| **Interconnect** | The network that allows all the components (CPU, memory, peripherals) to **communicate** and transfer data. | Buses or Network-on-Chip (NoC) |
| **Clock/Timing** | Generates and distributes a stable, synchronized clock signal to coordinate all activities. | **Phase-Locked Loop (PLL)** in BabySoC |
| **Other Accelerators** | Specialized units for specific, demanding tasks. | GPU (Graphics), DSP (Digital Signal Processing), Security Modules |

---
## 3. Typical SoC Design flow

The SoC design process is a multi-stage workflow.



<img width="867" height="1305" alt="381900611-54b5e8f9-f03d-4b53-a535-859360589119" src="https://github.com/user-attachments/assets/3cf78d86-90b5-4ce6-9035-f323a40e38d7" />




## 4. BabySoC: A Simplified Model for Learning 
<img width="2270" height="1260" alt="381908055-38253bb7-b658-496d-a043-15402219e089" src="https://github.com/user-attachments/assets/b9689112-5081-428e-abbc-f67db837e666" />


The **VSDBabySoC** is designed as a compact, educational platform based on the **RISC-V architecture**. Its primary purpose is to provide a simplified, highly documented environment for learning fundamental SoC concepts and experimenting with IP cores and mixed-signal (digital-analog) interfacing.

### BabySoC's Key Components and Simplified Interconnect

BabySoC deliberately pares down a full-scale SoC to three essential IP cores:

1.  **RVMYTH (RISC-V CPU):** The core processor, which executes instructions and generates the digital data stream (specifically using the `r17` register) to be converted.
2.  **Phase-Locked Loop (PLL):** Generates a stable and synchronized clock signal, which is crucial for coordinating the RVMYTH's execution and the DAC's conversion process. This internal timing is a fundamental requirement in any high-speed SoC.
3.  **10-bit Digital-to-Analog Converter (DAC):** The primary peripheral, converting the digital data from RVMYTH into a real-world analog signal (e.g., for audio/video output). This highlights the necessary bridge between the digital core and the analog world in many modern devices.

By focusing on this minimal, functional set of components, BabySoC allows learners to master the interactions between a CPU, a clocking system, and a crucial mixed-signal peripheral without the complexity of a commercial-grade SoC.

---

## 5. The Role of Functional Modelling

The SoC design process is a multi-stage workflow, and **Functional Modelling** (or **Pre-RTL Simulation**) is one of the earliest and most critical phases.

### Design Flow Context



The typical digital design flow proceeds as follows:

$$\text{Functional Modelling} \rightarrow \text{RTL Design} \rightarrow \text{Logic Synthesis} \rightarrow \text{Physical Design}$$

### Importance of Functional Modelling

* **Verifying Concept and Logic:** Functional modelling involves writing high-level code (like Verilog) to describe *what* the circuit does (its behavior and function) without worrying about *how* it will be physically implemented in gates or transistors.
* **Debugging Early:** It allows designers to simulate the system's behavior using tools like **Icarus Verilog** and **GTKWave** (as used in this task) to ensure the system logic is correct before investing significant time in Register Transfer Level (RTL) coding and physical design. Fixing errors at this stage is drastically faster and cheaper than later stages.
* **Establishing Testbenches:** The functional model acts as a reference against which the more detailed RTL design will be verified.

In the context of BabySoC, the functional modelling stage is where we confirm that the RVMYTH core, the PLL, and the DAC interact correctly—for instance, that the RVMYTH's output is correctly passed to and converted by the DAC, all synchronized by a stable clock. This foundational simulation ensures that the design's *intent* is met before moving toward a gate-level and ultimately a physical chip implementation.

## 📂 Project Structure

```txt
VSDBabySoC/
├── src/
│   ├── include/      # Header files (*.vh)
│   ├── module/       # Verilog + TLV modules
│   │   ├── vsdbabysoc.v   # Top-level module
│   │   ├── rvmyth.v       # CPU
│   │   ├── avsdpll.v      # PLL
│   │   ├── avsddac.v      # DAC
│   │   └── testbench.v    # Testbench
└── output/           # Simulation outputs
```

---

## 🛠️ Setup

### 📥 Cloning the Project

```bash
cd ~/VLSI
git clone https://github.com/manili/VSDBabySoC.git
cd VSDBabySoC/
```

📂 You’ll see:

* `src/` (modules)
* `images/` (visuals)
* `output/` (simulation results)

---

## 🔧 TLV → Verilog Conversion

Since **RVMYTH** is written in **TL-Verilog (.tlv)**, we need to convert it to Verilog before simulating.

```bash
# Install tools
sudo apt update
sudo apt install python3-venv python3-pip

# Create virtual env
python3 -m venv sp_env
source sp_env/bin/activate

# Install SandPiper-SaaS
pip install pyyaml click sandpiper-saas

# Convert TLV → Verilog
sandpiper-saas -i ./src/module/*.tlv -o rvmyth.v --bestsv --noline -p verilog --outdir ./src/module/
```

✅ Now you’ll have `rvmyth.v` alongside your other Verilog files.

---

## 🧪 Simulation Flow

### 🔹 Pre-Synthesis Simulation

```bash
mkdir -p output/pre_synth_sim

iverilog -o output/pre_synth_sim/pre_synth_sim.out \
  -DPRE_SYNTH_SIM \
  -I src/include -I src/module \
  src/module/testbench.v

cd output/pre_synth_sim
./pre_synth_sim.out
```

📊 View in GTKWave:

<img width="1415" height="684" alt="image" src="https://github.com/user-attachments/assets/54668b24-bb75-429c-8b7f-1520083df4a6" />


```bash
gtkwave output/pre_synth_sim/pre_synth_sim.vcd
```

### 🔍 Signals to Observe

* ⏱️ **CLK** → Input clock (from PLL)
* 🔄 **reset** → Reset signal
* 🎚 **OUT (DAC)** → Output from DAC (appears digital in sim)
* 🔢 **RV_TO_DAC[9:0]** → 10-bit RVMYTH output → DAC input

---


## VSDBabySoC Pre-Synthesis Waveform Analysis

The GTKWave screenshot confirms the fundamental digital-analog data path and timing for the simplified BabySoC design.

### Core Timing and Control

* **`CLK`**: Shows a stable, periodic high/low sequence, confirming the **Phase-Locked Loop (PLL)** is generating the necessary system clock to drive all components.
* **`reset`**: Is held **low (`0`)**, indicating the entire system (RVMYTH, DAC, PLL) is operational and out of reset.

***

### Data Flow (RVMYTH to DAC)

The waveforms illustrate the **RVMYTH CPU** successfully delivering a digital value to the **DAC peripheral**.

* **`core.OUT`**: This is the data stream output from the **RVMYTH CPU** (likely from register `r17`). The visible pattern of **`0, 1, 0, 1, ...`** confirms the CPU is actively executing a test program designed to toggle its output.
* **`dac.DAC[9:0]`**: This is the **10-bit digital input** to the DAC. The value is consistently held at **`042`** (hexadecimal, or $66$ decimal) for the time shown. This confirms the CPU has successfully written a static digital code to the DAC's input register.

***

### Analog Output

* **`OUT` (Real-Valued)**: This signal, labeled `OUT=0.06451`, is the resulting **analog voltage output** from the DAC. Its stable value demonstrates the core mixed-signal function is working: the DAC successfully converts the digital input (`042h`) into a corresponding constant analog voltage (`0.06451`).

**In summary, the simulation verifies that the CPU is clocked, active, and can correctly communicate digital data to the DAC, which in turn converts it to a stable analog output voltage.**








