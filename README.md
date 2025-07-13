# 🔐 py-multilayer-obfuscator

A highly configurable **multi-layer Python obfuscator** that combines compression, AES + XOR encryption, marshal wrapping, and CodeType injection.

This tool helps you pack and protect your Python code using recursive encryption and obfuscation logic that unwraps itself at runtime.

---

## 📆 Features

* ✅ **Multi-layer compression**: `marshal` → `zlib` → `bz2` → `lzma`
* 🔐 **Double encryption**: AES‑CBC + XOR with auto‑generated keys
* 📜 **Base64-wrapped loader**: using `marshal`, `zlib`, and `base64`
* 🧠 **CodeType injection**: replaces the main function's code object
* 🔁 **Recursive obfuscation**: repeat the entire pipeline multiple times
* 🗂 **Single file output**: despite multiple layers, output stays as one `.py` file
* 🎲 **Auto key generation**: prints the XOR and AES keys at the end

---

## 💪 Requirements

Install dependencies via `pip`:

```bash
pip install pycryptodome
```

> Required to support AES encryption (`Crypto.Cipher.AES` and `Crypto.Util.Padding`).

---

## 🚀 Usage

```bash
git clone https://github.com/n1k4xryougaaa/py-multilayer-obfuscator.git
cd py-multilayer-obfuscator
git pull
python obfuscator.py
```

You will be prompted to enter:

* ✅ Input file path (your `.py` source)
* ✅ Output file path (the obfuscated `.py` result)
* ✅ Number of obfuscation loops (e.g., 3 = 3x deeper obfuscation)

---

## 🔁 How It Works (Per Layer)

Each layer of obfuscation applies the following pipeline:

1. **Compression Layer**

   * Your code is compiled to a Python code object using `compile(...)`
   * It's then serialized via `marshal`, compressed using `zlib`, `bz2`, and finally `lzma`

2. **Encryption Layer**

   * The compressed blob is AES-encrypted using AES‑CBC with a random IV
   * Then XOR-encrypted with a random key
   * The encrypted result is base64-encoded for safe embedding

3. **Loader Layer**

   * A small loader script is created to:

     * Decrypt the XOR layer
     * Decrypt AES and unpad
     * Decompress the original blob
     * Load the original code object and execute it

4. **Wrapper Layer**

   * The loader is compiled and marshaled
   * It's wrapped in a base64 + zlib exec call

5. **CodeType Injection**

   * The final `exec` wrapper is embedded inside a lambda function via `types.CodeType` injection:

     ```python
     _=(lambda x:x);code=type(_.__code__);_.__code__=code(...)
     ```

This entire process is **repeated recursively**, as many times as the user specifies.

---

## 🔓 Deobfuscation Notes

To deobfuscate a file that has been obfuscated `N` times, you would need to:

* Run it `N` times inside a controlled environment (or write an automated unpacker)
* Capture and log the intermediate layers
* Use the AES and XOR keys (printed after obfuscation) in the reverse order

This design is not intended for cryptographic security but rather **layered reverse engineering resistance**.

---

## ✅ Advantages

* 🔁 **Recursive obfuscation** for exponential complexity
* 🧬 **CodeType injection** hides code in unexpected places
* 🛡 **AES + XOR** double encryption for stronger protection
* 📆 **Single file output** keeps deployment simple
* 🎲 **Random keys** prevent consistent pattern matching
* 💡 **Readable and modifiable** for advanced users

---

## ⚠️ Limitations

* 🧠 **Security through obscurity**: this is **not** unbreakable; experienced reverse engineers can decompile it with time
* ⚡ **Performance impact**: many layers = slower load time
* 🔑 **Key management**: losing AES or XOR keys means you cannot recover the original script
* 🔁 **Deobfuscation** must follow exact reverse sequence of layers

---

## 📛 Example Output

After 3 layers of obfuscation, you’ll get a file like this:

```python
# AUTO-GENERATED OBFUSCATED FILE
_=(lambda x:x);code=type(_.__code__);_.__code__=code(...)
```

To run, just execute the file like normal:

```bash
python obfuscated_script.py
```

---

## 🧐 Credits

* Developed by [Nyctophile Ryouga](https://github.com/n1k4xryougaaa)
* Co-authored with OpenAI's ChatGPT (model GPT-4)
* Inspired by deep-layer Python packing and runtime obfuscation techniques

---

## 📜 License

MIT License — feel free to use, modify, and improve the project.
If you fork or extend it, a credit link would be appreciated 🙏
