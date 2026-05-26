# PHP File Obfuscator (Web UI)

[![PHP Version](https://img.shields.io/badge/php-%3E%3D%205.5-8892BF.svg)](https://www.php.net/)
[![License](https://img.shields.io/badge/license-MIT-green.svg)](LICENSE)

A premium-designed, single-file PHP web application that compiles, encrypts, and obfuscates PHP scripts using multiple packaging engines. 

Featuring a modern dark UI with drag-and-drop support, this tool helps you secure administrative scripts, file managers, or custom PHP code for production deployment.

---

## 🎨 Interface & UI Features

- **Premium Aesthetics**: Features a modern, glassmorphic dark theme with harmonious purple/pink gradients, fluid animations, and JetBrains Mono code rendering.
- **Interactive Drag & Drop**: Drag and drop your target PHP file directly into the browser to upload it.
- **Live Output Statistics**: Displays real-time details of the compilation:
  - Original file size vs. obfuscated file size.
  - Encryption ratio (%).
  - Live preview of the generated loader stub.
- **Copy & Download Actions**: Instantly download the compiled payload or copy the generated loader stub directly to your clipboard.

---

## ⚙️ Obfuscation & Encryption Engines

You can choose between three distinct compilation methods:

### 1. AES-256 Encryption (Password Gated)
- **Security**: Encrypts the source code with AES-256-CBC, using a key derived from the SHA-256 hash of your custom password.
- **Web Loader**: Embeds a clean, styled HTML login form within the output file. When the output script is requested, the user is prompted for the password.
- **Stealth Mode**: Includes an option to hide the login form completely, providing only a borderless, invisible password input field.
- **Memory-only Execution**: Decrypts the code directly in memory at runtime and includes it using a custom PHP stream wrapper (`vs://`). **No decrypted code is written to disk.**

### 2. Bitwise NOT + Memory Stream Wrapper (Diskless)
- **Security**: Obfuscates the payload using bitwise NOT (`~`) encoding.
- **Execution**: At runtime, registers an in-memory custom stream wrapper (`vs://`) to read, decode, and execute the payload on the fly.
- **AV Bypass**: Avoids temporary files on disk, making it safer against local file scanning.

### 3. Strrev + Temp-File (Legacy Compatibility)
- **Security**: Reverses the code payload (`strrev`) for basic obfuscation.
- **Execution**: Decodes the code and writes it to a temporary file in the system temp directory, includes the file, and immediately deletes (`unlink()`) it.
- **Compatibility**: Offers the highest compatibility for restricted web hosting environments where custom stream wrappers might be blocked.

---

## 📋 System Requirements

- **PHP**: Version 5.5 or higher.
- **Extensions**: `openssl` (required for the AES-256 encryption engine).
- **Web Server**: Apache, Nginx, IIS, Laragon, or any PHP-enabled web server.

---

## 🚀 Installation & Usage

1. Copy **[obfuscator.php](obfuscator.php)** to your web server (e.g. `/var/www/html/` or `C:/laragon/www/`).
2. Open the page in your web browser:
   `http://yourdomain.com/obfuscator.php`
3. Drag & drop or browse to select your PHP file.
4. Select your preferred **Compression Engine**:
   - For **AES-256**, define a secure **Decryption Password** (and optionally check *Stealth mode*).
5. Click **⚡ Secure Uploaded File**.
6. Download the resulting `[filename]_obf.php` file or copy its stub code.

---

## 🛠️ How It Works (Technical Overview)

1. **Upload & Preprocessing**: The script receives the uploaded PHP file, strips the outer `<?php` and `?>` tags, and pre-processes files (e.g. automatically updating `PASSWORD` configuration keys to match the obfuscation password).
2. **Payload Compiling**:
   - Generates a lightweight bootstrap loader stub depending on the selected engine.
   - Encrypts/obfuscates the payload code.
   - Appends the binary ciphertext payload directly after a `__halt_compiler();` marker in the stub.
3. **Execution**:
   - When the output file is requested, the PHP bootstrap stub reads its own file contents starting from the exact binary seek offset after `__halt_compiler();`.
   - It decodes/decrypts the payload in-memory (or via a temporary file for the temp-file engine) and runs it via `include` inside the parent SAPI request lifecycle.

---

## 🔒 Security Recommendations

- **Use HTTPS**: Always run the obfuscation panel and your encrypted scripts over HTTPS to secure file transfers and password entries from sniffing.
- **Restrict Access**: Place `obfuscator.php` in a password-protected directory (`.htaccess` / `.htpasswd`) or delete it from the server when not in use.
- **Choose Strong Passwords**: The strength of the AES-256 output file relies entirely on the strength of your decryption password.

---

## 📄 License

This project is licensed under the MIT License. Feel free to use, modify, and distribute it.
