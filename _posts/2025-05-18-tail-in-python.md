---
title: Implementing a Tail-like Function
author: ankitz007
categories: [Programming, Machine Coding]
tags: [Programming, Python, Machine Coding]
description: Learn how to implement a tail-like function in Python with various memory-efficient approaches.
image:
    path: https://res.cloudinary.com/ankitz007/image/upload/v1747594060/machine-coding/tail_wuh1qe.webp
    lqip: https://res.cloudinary.com/ankitz007/image/upload/e_blackwhite/v1747594060/machine-coding/tail_wuh1qe.webp
    alt: Source - Shapeshed.com
---

Here’s a concise roadmap of three approaches to implement a `tail`‑like function in Python.

* **Full‑file read**: Read the entire file into memory and slice the last `n` lines; simple but uses `O(file_size)` memory.
* **Fixed‑size queue**: Stream lines one by one, retaining only the last `n` in a `collections.deque(maxlen=n)`, so memory is bounded by `O(n)`.
* **Backward block buffer**: Seek from the file’s end in binary mode, read fixed‑size blocks backward, count newlines until enough are found, then decode and print—uses minimal memory even for very large files.

## 1. Naïve Full‑File Read (No Memory Consideration)

### Code Example

```python
def tail_simple(num_lines: int, file_path: str = "./file.log") -> None:
    with open(file_path, "r", encoding="utf-8", errors="replace") as f:
        lines = f.readlines()                   # reads entire file into memory
        for line in lines[-num_lines:]:          # slices last n lines
            print(line.rstrip("\n"))
```

### Nuances

* **Memory usage**: `f.readlines()` loads every line into a list, so memory scales with file size.
* **Performance**: Fast for small to medium files, but will crash or swap if the file is huge.

## 2. Bounded Memory via a Fixed‑Size Queue

### Code Example

```python
from collections import deque

def tail_queue(num_lines: int, file_path: str = "./file.log") -> None:
    buffer = deque(maxlen=num_lines)           # only holds last n lines
    with open(file_path, "r", encoding="utf-8", errors="replace") as f:
        for line in f:
            buffer.append(line.rstrip("\n"))   # discard oldest when full
    for line in buffer:
        print(line)
```

### Nuances

* **Memory bound**: Uses at most *n* lines of memory, regardless of file size.
* **Streaming**: Reads line by line; useful for very large files or when you don’t know file size in advance.
* **Overhead**: Python’s generator overhead per line; slightly slower than block reads but generally fine.

## 3. Block‑Buffered Reverse Read (Constant Small Memory)

When `num_lines` is small relative to file size, reading backwards in chunks can be far more efficient.

### Incremental Development

- **Open in binary and seek to end**

   

```python
   import os

   with open(file_path, "rb") as f:
       f.seek(0, os.SEEK_END)                # go to file end :contentReference[oaicite:5]{index=5}
   ```

- **Read fixed‑size blocks backwards**

   

```python
   block_size = 1024
   file_size = f.tell()
   buffer = bytearray()
   while file_size > 0 and buffer.count(b"\n") <= num_lines:
       read_size = min(block_size, file_size)
       f.seek(file_size - read_size)
       buffer[:0] = f.read(read_size)        # prepend new data
       file_size -= read_size
   ```

- **Extract and decode the last lines**

   

```python
   lines = buffer.splitlines()[-num_lines:]
   for line in lines:
       print(line.decode("utf-8"))
   ```

### Full Function

```python
import os

def tail(num_lines: int, file_path: str = "./file.log") -> None:
    with open(file_path, "rb") as f:
        f.seek(0, os.SEEK_END)
        file_size = f.tell()

        buffer = bytearray()
        lines_found = 0
        block_size = 1024

        # Read backwards until we find enough newlines
        while file_size > 0 and lines_found <= num_lines:
            read_size = min(block_size, file_size)
            f.seek(file_size - read_size)
            data = f.read(read_size)
            buffer[:0] = data
            lines_found = buffer.count(b"\n")
            file_size -= read_size

        # Decode and print the desired lines
        lines = buffer.splitlines()[-num_lines:]
        for line in lines:
            print(line.decode("utf-8"))
```

### Nuances

* **Binary mode** avoids issues with variable‑length encodings when seeking.
* **Block size**: Choose based on typical line length; too small and you incur many seeks, too large and you waste memory.
* **Byte vs. text splitting**: Counts `b"\n"` and splits on raw bytes, then decodes.
* **Edge cases**: Handles files smaller than `block_size`, and files with fewer than `num_lines` lines gracefully.

## Conclusion

* **Full‑file read** is easiest to implement but not scalable.
* **Deque‑based** offers `O(n)` memory bound and simple streaming.
* **Block‑buffered reverse read** provides near‑constant memory overhead and is ideal for very large files.

Choose the approach that best fits your file sizes and memory constraints.
