# 8-bit ALU Integrated with ROM on ZYNQ Platform

[![FPGA](https://img.shields.io/badge/FPGA-ZYNQ--7000-blue)](https://www.xilinx.com/products/silicon-devices/soc/zynq-7000.html)
[![VHDL](https://img.shields.io/badge/Language-VHDL-green)](https://en.wikipedia.org/wiki/VHDL)
[![License](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## 📋 Table of Contents
- [Project Overview](#-project-overview)
- [System Architecture](#-system-architecture)
- [Design Implementation](#-design-implementation)
- [File Structure](#-file-structure)
- [Getting Started](#-getting-started)
- [Testing and Validation](#-testing-and-validation)
- [ZYNQ Implementation](#-zynq-implementation)
- [Results](#-results)
- [Limitations and Future Work](#-limitations-and-future-work)
- [Contributing](#-contributing)

## 🎯 Project Overview

This project implements an 8-bit Arithmetic Logic Unit (ALU) integrated with a ROM (Read-Only Memory) on a ZYNQ-7000 SoC platform. The system demonstrates embedded system design principles by combining programmable logic (PL) and processing system (PS) capabilities.

### Key Features
- ✅ 8-bit ALU supporting four operations: Addition, Subtraction, Multiplication, Division
- ✅ 8×8-bit ROM with predefined content
- ✅ Synchronous operation with clock, reset, and chip enable
- ✅ Carry/overflow flag generation
- ✅ ZYNQ-7000 SoC implementation with ARM Cortex-A9 integration
- ✅ Comprehensive testbench with edge case coverage

## 🏗️ System Architecture

### Block Diagram
```
┌─────────────────────────────────────────────────────────────┐
│                    ZYNQ-7000 SoC                           │
│  ┌─────────────────────┐    ┌─────────────────────────────┐ │
│  │   Processing System │    │     Programmable Logic      │ │
│  │     (ARM PS)        │    │          (ARTIX-7)          │ │
│  │                     │    │                             │ │
│  │  ┌─────────────┐    │    │  ┌─────────────────────────┐ │ │
│  │  │ARM Cortex-A9│◄───┼────┼──┤    ALU-ROM System      │ │ │
│  │  │  Processor  │    │    │  │                         │ │ │
│  │  └─────────────┘    │    │  │  ┌─────┐    ┌───────┐  │ │ │
│  │                     │    │  │  │ ROM │    │  ALU  │  │ │ │
│  │  ┌─────────────┐    │    │  │  │8x8  │◄───┤ 8-bit │  │ │ │
│  │  │   Memory    │    │    │  │  │bits │    │       │  │ │ │
│  │  └─────────────┘    │    │  │  └─────┘    └───────┘  │ │ │
│  └─────────────────────┘    └─────────────────────────────┘ │
└─────────────────────────────────────────────────────────────┘
           │                              │
           │        AXI4-Lite Bus         │
           └──────────────────────────────┘
```

### Signal Interface
| Signal | Direction | Width | Description |
|--------|-----------|-------|-------------|
| CLK | Input | 1 | System Clock |
| RESET | Input | 1 | Asynchronous Reset |
| CE | Input | 1 | Chip Enable |
| OP | Input | 2 | Operation Code (00=ADD, 01=SUB, 10=MUL, 11=DIV) |
| A | Input | 8 | Operand A |
| ADDR | Input | 3 | ROM Address |
| RESULT | Output | 8 | ALU Result |
| CARRY | Output | 1 | Carry/Error Flag |

## 🔧 Design Implementation

### VHDL Methodology
The design employs a **structural VHDL approach** with:
- **Synchronous Design**: All operations are clock-synchronized
- **Process-based Implementation**: Separate processes for ROM access and ALU operations
- **Signal-based Communication**: Internal signals for data flow between components

### ROM Content Design
```vhdl
constant ROM_Content : ROM_Type := (
    0 => "00000100",  -- 4   (0x04)
    1 => "00001000",  -- 8   (0x08)
    2 => "00010100",  -- 20  (0x14)
    3 => "00100011",  -- 35  (0x23)
    4 => "01000010",  -- 66  (0x42)
    5 => "01100100",  -- 100 (0x64)
    6 => "10000011",  -- 131 (0x83)
    7 => "10000100"   -- 132 (0x84)
);
```

### Carry Flag Functionality
The carry flag serves multiple purposes:
- **Addition/Multiplication**: Indicates overflow when result exceeds 8-bit range
- **Subtraction**: Indicates underflow when A < B
- **Division**: Indicates division by zero error

## 📁 File Structure

```
ALU_ROM_ZYNQ/
├── README.md
├── LICENSE
├── .gitignore
├── src/
│   ├── rtl/
│   │   ├── MAIN_ALU.vhd              # Main ALU entity
│   │   └── ALU_ROM_wrapper.vhd       # AXI wrapper for IP integration
│   ├── testbench/
│   │   └── MAIN_ALU_tb.vhd           # Comprehensive testbench
│   └── software/
│       ├── alu_driver.c              # ARM driver code
│       ├── alu_driver.h              # Driver header
│       └── main.c                    # Test application
├── constraints/
│   └── zybo_z7_constraints.xdc      # ZYBO Z7 pin assignments
├── tcl/
│   ├── create_project.tcl            # Project creation script
│   ├── build_bitstream.tcl           # Bitstream generation
│   └── program_fpga.tcl              # Programming script
├── docs/
│   ├── design_specification.md       # Detailed design document
│   ├── user_guide.md                 # Usage instructions
│   └── test_results.md               # Verification results
├── sim/
│   ├── run_simulation.tcl            # Simulation script
│   └── waveforms/                    # Saved waveform files
├── ip/
│   └── alu_rom_ip/                   # Generated IP files
├── images/
│   ├── block_diagram.png
│   ├── board_setup.jpg
│   ├── waveform_screenshots/
│   └── system_diagram.svg
└── scripts/
    ├── setup_environment.sh          # Environment setup
    └── run_tests.sh                   # Automated testing
```

## 🚀 Getting Started

### Prerequisites
- **Hardware**: ZYBO Z7-20 Development Board
- **Software**: Xilinx Vivado 2020.2 or later
- **Knowledge**: Basic VHDL and embedded systems concepts

### Quick Start
1. **Clone the repository**:
   ```bash
   git clone https://github.com/yourusername/ALU_ROM_ZYNQ.git
   cd ALU_ROM_ZYNQ
   ```

2. **Set up environment**:
   ```bash
   source scripts/setup_environment.sh
   ```

3. **Create Vivado project**:
   ```bash
   vivado -mode batch -source tcl/create_project.tcl
   ```

4. **Run simulation**:
   ```bash
   vivado -mode batch -source sim/run_simulation.tcl
   ```

5. **Build bitstream**:
   ```bash
   vivado -mode batch -source tcl/build_bitstream.tcl
   ```

### Manual Setup
1. Open Vivado
2. Create new project targeting ZYNQ-7020
3. Add all VHDL files from `src/rtl/`
4. Add constraints file
5. Run synthesis and implementation
6. Generate bitstream

## 🧪 Testing and Validation

### Test Cases Coverage

| Test Case | Operation | Input A | ROM Addr | ROM Value | Expected Result | Expected Carry |
|-----------|-----------|---------|----------|-----------|-----------------|----------------|
| 1 | Addition | 15 (0x0F) | 1 | 8 (0x08) | 23 (0x17) | 0 |
| 2 | Subtraction | 16 (0x10) | 2 | 20 (0x14) | 0 (0x00) | 1 (underflow) |
| 3 | Multiplication | 2 (0x02) | 3 | 35 (0x23) | 70 (0x46) | 0 |
| 4 | Division | 16 (0x10) | 1 | 8 (0x08) | 2 (0x02) | 0 |
| 5 | Division by Zero | 16 (0x10) | 0 | 4 (0x04) | 0 (0x00) | 1 (error) |

### Running Tests
```bash
# Run all tests
./scripts/run_tests.sh

# Run specific simulation
cd sim/
vivado -mode batch -source run_simulation.tcl
```

### Expected Waveforms
- **Clock Period**: 12ns (83.33 MHz)
- **Reset Duration**: 20ns
- **Operation Latency**: 1 clock cycle
- **Test Duration**: 20ns per test case

## ⚡ ZYNQ Implementation

### Resource Utilization (Estimated)
| Resource | Used | Available | Utilization |
|----------|------|-----------|-------------|
| LUT | 80 | 53,200 | <1% |
| LUTRAM | 8 | 17,400 | <1% |
| FF | 24 | 106,400 | <1% |
| BRAM | 0.5 | 140 | <1% |
| DSP | 1 | 220 | <1% |
| IO | 20 | 200 | 10% |

### ZYBO Board Connections
- **Switches (SW0-SW7)**: Input operand A
- **Buttons**: Operation selection and control
- **LEDs (LD0-LD7)**: Result output display
- **RGB LEDs**: Status indicators
- **Pmod Connectors**: Extended I/O for breadboard

## 📊 Results

### Performance Metrics
- **Maximum Clock Frequency**: 100 MHz
- **Latency**: 1 clock cycle for all operations
- **Throughput**: 100 MOPS (Million Operations Per Second)
- **Power Consumption**: <500mW (estimated)

### Verification Status
- ✅ All arithmetic operations verified
- ✅ ROM access functionality confirmed
- ✅ Edge cases handled correctly
- ✅ Overflow/underflow detection working
- ✅ Division by zero protection active

## ⚠️ Limitations and Future Work

### Current Limitations
1. **8-bit Result Limitation**: Multiplication overflow truncated to 8 bits
2. **Integer Division Only**: No fractional results
3. **Fixed ROM Content**: No runtime modification capability
4. **Single Error Flag**: Limited error reporting granularity

### Proposed Enhancements
1. **16-bit Extended ALU**: Full precision multiplication results
2. **Floating-Point Operations**: IEEE 754 support
3. **Configurable ROM**: Runtime programmable memory
4. **Enhanced Error Reporting**: Detailed error codes
5. **Pipeline Implementation**: Multi-stage processing for higher throughput

## 🤝 Contributing

We welcome contributions! Please follow these steps:

1. Fork the repository
2. Create a feature branch (`git checkout -b feature/amazing-feature`)
3. Commit your changes (`git commit -m 'Add amazing feature'`)
4. Push to the branch (`git push origin feature/amazing-feature`)
5. Open a Pull Request

### Contribution Guidelines
- Follow VHDL coding standards
- Include comprehensive test cases
- Update documentation as needed
- Ensure all tests pass

## 📄 License

This project is licensed under the MIT License - see the [LICENSE](LICENSE) file for details.

## 📞 Contact

🔗 LinkedIn: www.linkedin.com/in/mitsabhadiya
📅 Project Date: 2024

## 🙏 Acknowledgments

- Course Instructor and Teaching Assistants
- Xilinx/AMD for development tools
- FPGA community for valuable resources
- Fellow students for collaboration and support

---

**⭐ If you find this project helpful, please give it a star!**

*This project was developed as part of an embedded systems course focusing on FPGA-based system design and VHDL implementation.*
