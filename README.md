# NPF (Nasir Picture Format) - C++ Implementation

A high-performance C++ implementation of the NPF image encryption system that creates encrypted `.npf` files which show noise in regular image viewers but can be decrypted with the correct password.

## Features

- **🔒 Strong Encryption**: AES-256-GCM with PBKDF2 key derivation
- **⚡ High Performance**: Native C++ implementation
- **🔐 Secure**: 100,000 PBKDF2 iterations, random salt per file
- **🎭 Noise Display**: Encrypted files show random noise in regular viewers
- **💻 Cross-platform**: Works on Windows (MSYS2), Linux, and macOS
- **📁 File Format**: Custom .npf format with metadata preservation
- **🖼️ Qt GUI Viewer**: Modern graphical interface with fullscreen support
- **⌨️ Keyboard Navigation**: Arrow keys for image navigation
- **🔑 Smart Password Management**: Remembers last used password
- **🔍 Auto-Detection**: Seamlessly handles encrypted and regular images

## Prerequisites

### MSYS2 (Windows)
```bash
# Install required packages
pacman -S mingw-w64-x86_64-gcc
pacman -S mingw-w64-x86_64-openssl
pacman -S mingw-w64-x86_64-qt6-base  # For Qt GUI viewer
pacman -S make
```

### Ubuntu/Debian
```bash
sudo apt-get install build-essential libssl-dev
sudo apt-get install qt6-base-dev  # For Qt GUI viewer
```

### CentOS/RHEL
```bash
sudo yum install gcc-c++ openssl-devel make
```

## Building

### Option 1: Qt GUI Viewer (Recommended)
Build the modern Qt-based graphical viewer:

```bash
# Copy the Qt Makefile
cp Makefile.qt Makefile

# Check dependencies
make check-deps

# Build Qt viewer
make all

# Or use the Windows batch file
build_qt.bat
```

### Option 2: Console Tools Only
Uses the simplified Makefile without Qt dependencies:

```bash
# Copy the simple Makefile
cp Makefile.simple Makefile

# Check dependencies
make check-deps

# Build all executables
make all

# Or build quickly (encrypt only)
make quick
```

### Option 3: CMake Build
If you have CMake and nlohmann/json installed:

```bash
mkdir build && cd build
cmake ..
make
```

### Option 4: Manual Compilation
```bash
# Create directories
mkdir -p obj bin

# Compile library objects
g++ -std=c++17 -Wall -O2 -c npf_utils.cpp -o obj/npf_utils.o
g++ -std=c++17 -Wall -O2 -c npf_encryptor_simple.cpp -o obj/npf_encryptor_simple.o
g++ -std=c++17 -Wall -O2 -c npf_decryptor.cpp -o obj/npf_decryptor.o

# Build executables
g++ -std=c++17 -Wall -O2 npf_encrypt.cpp obj/*.o -o bin/npf_encrypt.exe -lssl -lcrypto
g++ -std=c++17 -Wall -O2 npf_decrypt.cpp obj/*.o -o bin/npf_decrypt.exe -lssl -lcrypto
g++ -std=c++17 -Wall -O2 npf_demo.cpp obj/*.o -o bin/npf_demo.exe -lssl -lcrypto
```

## Usage

### Qt GUI Viewer (Interactive Mode)
The modern Qt-based viewer with arrow key navigation and smart password management:

```bash
./bin/npf_viewer [image_file.npf]
```

Features:
- **Arrow Key Navigation**: Use left/right arrows to navigate through .npf images in the current directory
- **Smart Password Management**: Remembers the last successful password per session
- **Fullscreen Mode**: Press F11 or double-click the image for fullscreen view
- **Menu Options**: File menu for encrypt/decrypt operations
- **Drag & Drop**: Drop image files directly onto the viewer
- **Zoom Controls**: Mouse wheel or +/- keys to zoom
- **Image Info**: View image dimensions and encryption status

### Console Tools

#### Encrypt an Image
```bash
# Basic usage
./bin/npf_encrypt image.jpg mypassword123

# Specify output file
./bin/npf_encrypt photo.png "my secret password" encrypted_photo.npf

# Encrypt with custom path
./bin/npf_encrypt /path/to/image.jpg password /path/to/output.npf
```

#### Decrypt an Image
```bash
# Basic usage
./bin/npf_decrypt encrypted.npf mypassword123

# Specify output file
./bin/npf_decrypt secret.npf "my secret password" decrypted_image.png

# Decrypt with custom path
./bin/npf_decrypt /path/to/file.npf password /path/to/output.jpg
```

#### Run Demo
```bash
# Run the demonstration
make demo

# Or run directly
./bin/npf_demo
```

### Test with Sample Images
```bash
# Test with sample images from parent directory
make test
```

## File Structure

```
cpp/
├── npf_encryptor.h          # Encryptor header
├── npf_encryptor.cpp        # Full encryptor implementation
├── npf_encryptor_simple.cpp # Simplified encryptor (no external deps)
├── npf_decryptor.h          # Decryptor header
├── npf_decryptor.cpp        # Decryptor implementation
├── npf_utils.h              # Utility functions header
├── npf_utils.cpp            # Utility functions implementation
├── npf_encrypt.cpp          # Encryption command-line tool
├── npf_decrypt.cpp          # Decryption command-line tool
├── npf_demo.cpp             # Demonstration program
├── CMakeLists.txt           # CMake build file
├── Makefile                 # Standard Makefile
├── Makefile.simple          # Simplified Makefile (recommended)
└── README.md                # This file
```

## API Usage

### Encrypt an Image (C++ Code)
```cpp
#include "npf_encryptor.h"

try {
    NPFEncryptor encryptor;
    std::string encryptedFile = encryptor.encryptImage(
        "input.jpg",           // Input image path
        "mypassword123",       // Password
        "output.npf"           // Output path (optional)
    );
    std::cout << "Encrypted: " << encryptedFile << std::endl;
} catch (const std::exception& e) {
    std::cerr << "Error: " << e.what() << std::endl;
}
```

### Decrypt an Image (C++ Code)
```cpp
#include "npf_decryptor.h"

try {
    NPFDecryptor decryptor;
    
    // Check if file is valid NPF
    if (decryptor.isNPFFile("encrypted.npf")) {
        std::string decryptedFile = decryptor.decryptImage(
            "encrypted.npf",       // NPF file path
            "mypassword123",       // Password
            "decrypted.jpg"        // Output path (optional)
        );
        std::cout << "Decrypted: " << decryptedFile << std::endl;
    }
} catch (const std::exception& e) {
    std::cerr << "Error: " << e.what() << std::endl;
}
```

### Get NPF Metadata
```cpp
#include "npf_decryptor.h"

NPFDecryptor decryptor;
auto metadata = decryptor.getNPFMetadata("file.npf");

for (const auto& [key, value] : metadata) {
    std::cout << key << ": " << value << std::endl;
}
```

## Security Features

- **AES-256-GCM**: Industry-standard encryption with authentication
- **PBKDF2**: Key derivation with SHA-256 and 100,000 iterations
- **Random Salt**: Unique salt for each encrypted file
- **Authentication**: Built-in integrity verification
- **Memory Safety**: Secure memory handling for cryptographic operations

## NPF File Format

```
[Header: "NPF_ENCRYPTED_IMAGE"]
[Salt Length: 4 bytes]
[Salt: 16 bytes]
[Metadata Length: 4 bytes]
[Metadata: JSON-formatted metadata]
[Encrypted Data: AES-256-GCM encrypted image]
```

## Troubleshooting

### Build Issues

1. **OpenSSL not found:**
   ```bash
   # MSYS2
   pacman -S mingw-w64-x86_64-openssl
   
   # Ubuntu
   sudo apt-get install libssl-dev
   ```

2. **Compiler not found:**
   ```bash
   # MSYS2
   pacman -S mingw-w64-x86_64-gcc
   
   # Ubuntu
   sudo apt-get install build-essential
   ```

3. **Linking errors:**
   - Make sure OpenSSL development libraries are installed
   - Check that you're using the correct Makefile for your system

### Runtime Issues

1. **"Incorrect password" error:**
   - Verify the password is exactly the same as used for encryption
   - Passwords are case-sensitive

2. **"Not a valid NPF file" error:**
   - Make sure the file was created by this NPF system
   - Check if the file is corrupted

3. **File permission errors:**
   - Ensure read access to input files
   - Ensure write access to output directory

## Performance

- **Encryption Speed**: ~50-100 MB/s (depends on system)
- **Memory Usage**: ~2x image size during processing
- **File Size Overhead**: ~1-5% increase due to encryption metadata

## Comparison with Python Version

| Feature | C++ Version | Python Version |
|---------|-------------|----------------|
| Speed | ⚡ Very Fast | 🐌 Slower |
| Dependencies | Minimal | Many packages |
| Binary Size | Small | Large (interpreter) |
| Platform | Native | Requires Python |
| Development | More complex | Easier |

## Contributing

Feel free to submit issues and enhancement requests!

## License

Created by Nasir for educational and personal use.

---

**© 2025 NPF Image Encryption System - C++ Implementation by Nasir**
