# zlib-sync-rust

[![NPM Version][npm-version-image]][npm-url]  
[![NPM Downloads][npm-downloads-image]][npm-downloads-url]

> High-performance sync Zlib inflater for Node.js, built with Rust (`napi-rs`).

A **simple, fast, and synchronous zlib-stream decoder**, designed for use cases like **Discord Gateway compression**, or any application needing `zlib-stream` inflation **without async streams or event handling**.

---

## 🚀 Features

* ✅ **Synchronous interface** – No `on('data')` handling
* ✅ **Streaming decompression** – Keeps state across `push()` calls
* ✅ **Auto stream-reset on end** – Works well for multiple stream chunks (e.g. Discord)
* ✅ **Rust + napi-rs** – Fast, portable, and memory-safe
* ✅ Supports both **CommonJS** and **ESM**
* ✅ Same usage style as [`zlib-sync`](https://github.com/addaleax/zlib-sync) but written in Rust
* ✅ **Prebuilt binaries for major platforms** – No need to compile or install build tools

---

## 📦 Install

```bash
npm install zlib-sync-rust
```

---

## 🔧 Usage

### Basic Example

#### CJS

```js
const { Inflater } = require('zlib-sync-rust');

const inflater = new Inflater();

// Push compressed data chunk by chunk (zlib-stream)
const out1 = inflater.push(chunk1);
const out2 = inflater.push(chunk2);
const out3 = inflater.push(chunk3);
const result = Buffer.concat([out1, out2, out3]);
console.log(result.toString());
```

#### ESM

```js
import { Inflater } from 'zlib-sync-rust';

const inflater = new Inflater();

const out = inflater.push(zlibStreamChunk);
console.log(out.toString());
```

---

## 🔍 API

### `new Inflater()`

Creates a new zlib-stream inflater instance.

---

### `inflater.push(input: Buffer, flush?: boolean): Buffer`

| Parameter | Type      | Default  | Description                            |
| --------- | --------- | -------- | -------------------------------------- |
| `input`   | `Buffer`  | Required | The compressed zlib stream chunk       |
| `flush`   | `boolean` | `false`  | Whether to finalize the current stream |

* If `flush` is `false` (default), it keeps streaming.
* If `flush` is `true`, it signals the end of a zlib stream.
* When a stream ends (via `flush: true` or stream end marker), internal state is **auto-reset** for the next stream.

---

### `inflater.reset(): void`

Manually reset the internal inflater state.
This is **optional** in most use cases, but may be useful when:

* You detect the end of a zlib stream manually (e.g. Discord's `0x00 00 ff ff` marker)
* You want to **force discard current stream state** between data chunks

```js
inflater.reset(); // Ready for a new zlib stream
```

---

## ⚠️ Streaming Strategy Notes

* This inflater supports both:

  * **Single continuous zlib streams** (e.g. a file split into parts)
  * **Multiple independent zlib streams** (e.g. Discord's message framing)

* When used with Discord Gateway:

  * Discord sends **separate zlib streams**, each ending with `0x00 00 ff ff`
  * You should call `.reset()` **after detecting this marker**
  * Do **not** call with `flush: true` unless you know the entire stream is present

---

## ⚡ Why?

Node's native `zlib` is event-based and not friendly for some low-level protocols like Discord's zlib-stream compression.
This library gives you a **sync-friendly, stateful inflater** with **Rust-level performance**.

---

## 🚀 Prebuilt binaries

`zlib-sync-rust` provides **prebuilt `.node` binaries** for major platforms:

* Windows
* Linux

No need for `node-gyp` or build tools. Install and use directly.

This uses [`@napi-build/cli`](https://github.com/napi-rs/napi-build).

---

## 🧰 License

MIT © 2025 [Yuki](https://github.com/yukiakai212)

---

[npm-downloads-image]: https://badgen.net/npm/dm/zlib-sync-rust
[npm-downloads-url]: https://www.npmjs.com/package/zlib-sync-rust
[npm-url]: https://www.npmjs.com/package/zlib-sync-rust
[npm-version-image]: https://badgen.net/npm/v/zlib-sync-rust
