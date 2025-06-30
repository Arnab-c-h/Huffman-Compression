
# C++ Huffman Compression Tool

A command-line tool written in C++ that implements the Huffman coding algorithm for lossless data compression and decompression. The tool can compress any file into a custom `.abiz` format and decompress it back to its original state.

---

## Features

-   **Compress & Decompress**: Supports both file compression and decompression.
-   **Self-Contained Files**: The compressed `.abiz` file includes a header with all the necessary information to decompress it, requiring no external context.
-   **Performance Metrics**: Displays original/compressed file sizes, execution time, and a progress bar.
-   **Binary Safe**: Correctly handles all 256 possible byte values, making it suitable for any file type (text, images, executables, etc.).
-   **Clean Code Structure**: Organized into logical namespaces for clarity and maintainability.

---

## How It Works

The tool is built around the Huffman coding algorithm, a method for lossless data compression that assigns variable-length codes to input characters based on their frequencies.

### Compression Process (`-c`)

1.  **Frequency Analysis**: The program reads the input file to count the frequency of each byte (0-255).
2.  **Build Huffman Tree**: A priority queue (emulated with a sorted vector) is used to build the Huffman tree. Nodes with the lowest frequencies are repeatedly combined until only the root node remains.
3.  **Generate Huffman Codes**: The tree is traversed to generate a unique, prefix-free binary code for each character.
4.  **Write `.abiz` File**:
    -   A custom header containing the Huffman tree structure and padding info is generated.
    -   The input file is read again, and each byte is replaced with its corresponding Huffman code.
    -   These bits are packed into new bytes and written to the output file (`.abiz`).

### Decompression Process (`-dc`)

1.  **Read Header**: The decompressor first reads the header from the `.abiz` file.
2.  **Reconstruct Huffman Tree**: The original Huffman tree is rebuilt in memory using the character-to-code mapping from the header.
3.  **Decode Data**:
    -   The compressed data section is read bit by bit.
    -   The program traverses the reconstructed Huffman tree according to the bitstream ('0' for left, '1' for right).
    -   When a leaf node is reached, its character is written to the output file, and the traversal resets to the root.
    -   This continues until all compressed data is read, using the padding info for the final byte.

---

## The `.abiz` File Format

The custom compressed file format is designed to be self-sufficient.

| Part                   | Size (in bytes)                                  | Description                                                                                                                                              |
| ---------------------- | ------------------------------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| **Header**             |                                                  |                                                                                                                                                          |
| Unique Character Count | 1                                                | The number of unique characters in the original file, stored as `N-1`.                                                                                   |
| Huffman Table          | Variable                                         | A repeating block for each unique character: <br/> 1. **Character** (1 byte) <br/> 2. **Code Length** (1 byte) <br/> 3. **Huffman Code** (variable string) |
| Padding                | 1                                                | The number of unused bits in the *last byte* of the data section (from 0 to 7).                                                                          |
| **Data**               |                                                  |                                                                                                                                                          |
| Compressed Data        | Variable                                         | The continuous bitstream of Huffman codes representing the original file's content.                                                                      |

---

## Building the Code

The code is written in standard C++ and can be compiled with a modern C++ compiler like g++ or Clang.

To compile the program, run the following command in your terminal:

```bash
g++ -std=c++11 -O2 -o huffman main.cpp

