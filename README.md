# MCU_UART_RingBuffer

# STM32 UART DMA (Circular) + Idle Line Detection + Ring Buffer
A reliable UART receive pipeline for STM32 using **DMA (circular mode)**,  
**Idle Line interrupt**, and a **lock-free ring buffer**.  
Developed by **Byeongjun Min**.

This project demonstrates how to build a high-throughput, low-latency UART receiver suitable for
embedded communication, binary protocols, sensor streaming, and real-time firmware systems.

---

## ✅ Hardware & Environment
**Tested Board**
- STM32F446RE (Nucleo-F446RE)  
  *(Works on any STM32 with DMA + Idle Line support)*

**Tools**
- STM32CubeIDE  
- HAL/LL drivers  
- ARM-GCC toolchain  

---

## ✅ Features Implemented
- **UART RX via DMA (circular mode)** – continuous reception with zero CPU load  
- **Idle Line Detection** – identifies end-of-frame for variable-length packets  
- **Ring Buffer (Head/Tail)** – lock-free, overrun detection, safe byte extraction  
- **Binary Frame Parser Skeleton** – header, length, payload, CRC (optional)  
- **Low-ISR Firmware Architecture** – ISR only tracks DMA indices; parsing happens in main loop  

---

## ✅ Project Structure

Core/
├── Inc/
│ ├── uart_dma.h
│ ├── ringbuffer.h
│ └── protocol.h
└── Src/
├── uart_dma.c
├── ringbuffer.c
├── protocol.c
└── main.c

Drivers/
└── STM32 HAL + CMSIS

yaml
코드 복사

---

## ✅ How to Build & Run

### 1. CubeMX Settings
**UART**
- Asynchronous mode  
- DMA RX → Circular mode  
- Idle Line interrupt enabled (USARTx IDLE)  

**DMA**
- Direction: Peripheral → Memory  
- Mode: Circular  
- Memory increment: ON  
- Peripheral increment: OFF  

**NVIC**
- Enable USARTx global interrupt  

---

##  Initialization Example

```c
UART_DMA_Init(&huart2, dma_rx_buffer, DMA_BUF_SIZE);
RingBuffer_Init(&uart_rb, rb_data, RB_SIZE);
Main Loop

while (1) {
    UART_DMA_Process();     // Move DMA data → ring buffer
    ParseFrames(&uart_rb);  // Binary frame parsing (optional)
}
