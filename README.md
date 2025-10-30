# Base64UUID: A Compact and Sortable UUID Encoding

## Abstract

This specification defines the Base64UUID encoding, a compact, sortable, and URL-safe case-sensitive text representation of UUIDs using a 64-character ASCII alphabet. The encoding is designed specifically for UUID representation rather than general-purpose data encoding.

Base64UUID encoding is recommended for general use, while the standard 36-character hexadecimal format should be used when compatibility is required, and base32hex encoding is recommended for dictation of UUIDs.

## 1. Introduction

Base64UUID provides a text encoding for UUIDs that preserves their natural sort order while maintaining compatibility with URLs, file systems, and text selection interfaces.

## 2. Requirements Notation

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC2119](https://datatracker.ietf.org/doc/html/rfc2119) when, and only when, they appear in all capitals, as shown here.

## 3. The Base64UUID Encoding

### 3.1. Alphabet

The 64-character alphabet MUST be:
`$0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ_abcdefghijklmnopqrstuvwxyz`

Implementations MUST use the following mapping between 6-bit values and characters for Base64UUID encoding:
```
Value Encoding  Value Encoding  Value Encoding  Value Encoding
     0 $           16 F           32 V           48 k
     1 0           17 G           33 W           49 l
     2 1           18 H           34 X           50 m
     3 2           19 I           35 Y           51 n
     4 3           20 J           36 Z           52 o
     5 4           21 K           37 _           53 p
     6 5           22 L           38 a           54 q
     7 6           23 M           39 b           55 r
     8 7           24 N           40 c           56 s
     9 8           25 O           41 d           57 t
    10 9           26 P           42 e           58 u
    11 A           27 Q           43 f           59 v
    12 B           28 R           44 g           60 w
    13 C           29 S           45 h           61 x
    14 D           30 T           46 i           62 y
    15 E           31 U           47 j           63 z
```

Base64UUID strings MAY be enclosed in double quotes (U+0022) when necessary. Decoders MUST accept both quoted and unquoted forms of Base64UUID strings and remove the quotes before processing the Base64UUID value.

### 3.2. Encoding Process

To encode a UUID from its canonical format into a Base64UUID string:
1. Convert the UUID from standard 36-character hexadecimal format to 128-bit binary representation. UUID validation is RECOMMENDED. In a SQL query if convertion is not possible or validation is unsuccessful, the encoder MUST return NULL
2. Right-shift the entire 128-bit value by 4 bit positions
3. Set the 4 most significant bits to `0100` (binary) to ensure the encoded string starts with a letter
4. Encode the resulting 132-bit value as a 22-character Base64UUID string

### 3.3. Decoding Process

To decode a Base64UUID string back to its canonical UUID format:
1. Decode the 22-character Base64UUID string to obtain a 132-bit value. Base64UUID string validation is RECOMMENDED. In a SQL query if decoding is not possible or validation is unsuccessful, the decoder MUST return NULL
2. Convert the rightmost 128-bit value back into the standard 36-character hexadecimal format of UUID

## 4. Encoding Examples

| UUID                                      | Base64UUID Encoding       |
| ----------------------------------------- | ------------------------- |
| 00000000-0000-0000-0000-000000000000     | A$$$$$$$$$$$$$$$$$$$$$    |
| 019535d9-3df7-79fb-b466-fa907fa17f9e     | F$5KCSZxxrbvh5QuZ6yWUt  |
| ffffffff-ffff-ffff-ffff-ffffffffffff      | Izzzzzzzzzzzzzzzzzzzzz    |

## 5. Properties

### 5.1. Sort Preservation

The encoding preserves the numerical order of UUIDs when compared lexicographically as strings due to the monotonic mapping between bit values and character codes.

### 5.2. URL Safety

All characters in the alphabet are permitted in URL paths and query parameters without percent-encoding, satisfying URL-safe requirements.

### 5.3. File System Compatibility

The encoding is compatible with major file systems including Windows, Linux, Android, and iOS, as it excludes prohibited characters (`/`, `\`, `:`, `*`, `?`, `"`, `<`, `>`, `|`).

### 5.4. Text Selection

The alphabet supports complete text selection in popular SQL development environments:
- Full support: DBeaver (24.1+), DataGrip (2024.1+), Azure Data Studio (1.48+), Google BigQuery Console, Snowflake Web Interface.
- Partial support (selection breaks at `$` character): Notepad++, VS Code, SQL Server Management Studio.

### 5.5. Letter-Starting Guarantee

All encoded strings start with letters. This is achieved by right-shifting the 128-bit UUID by 4 bits and prefixing `0100` bits before Base64 encoding. The transformation is reversible and preserves lexical sort order of the original UUIDs.

## 6. Security Considerations

This encoding does not introduce additional security considerations beyond those of UUIDs themselves. Implementations should ensure proper validation of encoded strings before decoding.

## 7. References

- [RFC9562](https://datatracker.ietf.org/doc/html/rfc9562) Universally Unique IDentifiers (UUIDs)
- [RFC4648](https://datatracker.ietf.org/doc/rfc4648/) The Base16, Base32, and Base64 Data Encodings


