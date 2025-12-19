# mano

An assembler and emulator library for the Mano Machine RISC CPU, as described in "Computer System Architecture" by M. Morris Mano. Available on [crates.io](https://crates.io/crates/mano)

See [github.com/husmus00/mano-rs](https://github.com/husmus00/mano-rs) for examples of CLI, TUI, and web-based applications built using this library.

## Features

- **Assembler**: Compiles Mano assembly language to machine code
- **CPU Emulator**: Executes assembled programs instruction by instruction
- **State Inspection**: Access CPU registers and memory at any point
- **Message System**: Collect informational, debug, and error messages during execution

## Installation

Add this to your `Cargo.toml`:

```toml
[dependencies]
mano = "0.1"
```

## Quick Start

Building a basic emulator requires only three methods: `prime()`, `tick()`, and `get_state()`.

```rust
use mano::{Machine, Messages};

fn main() {
    let program = vec![
        "ORG 0".to_string(),
        "LDA A".to_string(),
        "ADD B".to_string(),
        "STA C".to_string(),
        "HLT".to_string(),
        "A, DEC 5".to_string(),
        "B, DEC 10".to_string(),
        "C, DEC 0".to_string(),
        "END".to_string(),
    ];

    let mut machine = Machine::new();

    // prime() - Assemble and load the program
    let messages = machine.prime(program);
    if messages.has_errors() {
        eprintln!("Assembly failed");
        return;
    }

    // tick() - Execute one instruction at a time
    let mut run_messages = Messages::new();
    while !machine.is_halted() {
        machine.tick(&mut run_messages);
    }

    // get_state() - Inspect CPU registers and memory
    let state = machine.get_state();
    println!("Program Counter: {:04X}", state.program_counter);
    println!("Accumulator: {:04X}", state.accumulator);
    println!("First 8 memory words: {:04X?}", &state.memory_snapshot[..8]);
}
```

## API Overview

### Machine

- `Machine::new()` - Create a new machine
- `prime(program: Vec<String>) -> Messages` - Assemble and load program
- `tick(messages: &mut Messages)` - Execute one instruction cycle
- `is_halted() -> bool` - Check if CPU is halted
- `get_state() -> MachineState` - Get current CPU and memory state
- `get_assembled_program() -> &[String]` - Get assembled hex code

### Messages

- `Messages::new()` - Create message container
- `has_errors() -> bool` - Check for error messages
- `info(message: &str)` - Add info message
- `debug(message: &str)` - Add debug message
- `error(message: &str)` - Add error message

### MachineState

Contains CPU registers and memory snapshot:
- `program_counter`, `accumulator`, `instruction_register`
- `address_register`, `data_register`, `extend_register`
- `sequence_counter`, `is_halted`, `is_running`
- `memory_snapshot` - First 32 words of memory

## Supported Instructions

**Memory-Reference**: `AND`, `ADD`, `LDA`, `STA`, `BUN`, `BSA`, `ISZ`

**Register-Reference**: `CLA`, `CLE`, `CMA`, `CME`, `CIR`, `CIL`, `INC`, `SPA`, `SNA`, `SZA`, `SZE`, `HLT`

**Input-Output**: `INP`, `OUT`, `SKI`, `SKO`, `ION`, `IOF`

**Pseudo-Instructions**: `ORG`, `DEC`, `HEX`, `END`

## Notes

Input functionality is planned but not yet provided.

## License

Licensed under the [MIT License](LICENSE).

## References

"Computer System Architecture" by M. Morris Mano (3rd Edition).
