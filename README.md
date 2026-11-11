# Forensics-CTF-Walkthroughs
bjective
We received a file containing a huge sequence of 0 and 1:
  <blr>
    fdkjhdshgf
    <blr>


111111111101100011111111111000000...
The goal was to recover the hidden image and find the flag.

Step 1 — Recognize the data
The file contained only:


0
1
That immediately suggests binary data.

Remember:


8 bits = 1 byte
For example:


01001000
represents:


0x48
which is ASCII:


H
So our first idea was:


Binary
   ↓
8-bit groups
   ↓
Bytes
Step 2 — Look for a file signature
Instead of blindly decoding everything, we should look at the beginning of the resulting bytes.

JPEG files normally begin with:


FF D8 FF
This is the JPEG magic byte/signature.

So if our binary starts converting to:


FF D8 FF ...
we know:

🎯 The binary is actually a JPEG file represented as 0 and 1.

Step 3 — Convert binary into the actual file
We convert every 8 bits into a byte.

Conceptually:


11111111 → FF
11011000 → D8
11111111 → FF
Therefore:


11111111 11011000 11111111
       ↓
FF D8 FF
       ↓
JPEG
After converting the complete binary data, we get:


decoded.jpg
Step 4 — Verify the file
Use:

Bash

file decoded.jpg
Expected result:


JPEG image data
You can also verify the header:

Bash

xxd -l 16 decoded.jpg
You should see something beginning with:


ff d8 ff
Step 5 — Open/analyze the JPEG
Now that we know it's a JPEG, we can investigate it.

Useful commands:

Bash

exiftool decoded.jpg
Bash

strings decoded.jpg
Bash

binwalk decoded.jpg
The important point is that the binary itself wasn't the final format. It was another representation of a JPEG.

Step 6 — Get the flag
The recovered JPEG contained the flag:


picoCTF{h1dd3n_1n_th3_b1n4ry_8e65b559}
🏁 Final chain

Binary 0/1
    ↓
8-bit groups
    ↓
Bytes
    ↓
FF D8 FF
    ↓
JPEG
    ↓
Decode/recover image
    ↓
FLAG
❌ Mistakes / lessons
Mistake 1: Thinking it was encrypted
It wasn't encryption.

It was simply:


binary representation of a file
Mistake 2: Trying Base64 first
We should identify the data format before trying random decoding methods.

Mistake 3: Ignoring magic bytes
The:


FF D8 FF
signature was the important clue.

🧠 Main lesson
Always identify the underlying file format before assuming something is encrypted.
