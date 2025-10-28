# Base64UUID: A Compact and Sortable UUID Encoding

## Abstract

This specification defines the Base64UUID encoding, a compact, sortable, and URL-safe text representation of UUIDs using a 64-character ASCII alphabet. The encoding is designed specifically for UUID representation rather than general-purpose data encoding.

## 1. Introduction

Base64UUID provides a text encoding for UUIDs that preserves their natural sort order while maintaining compatibility with URLs, file systems, and text selection interfaces.

## 2. The Base64UUID Encoding

### 2.1. Alphabet

The 64-character alphabet SHALL be:
`0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz_$`

```
Value Encoding  Value Encoding  Value Encoding  Value Encoding
     0 0           16 G           32 W           48 m
     1 1           17 H           33 X           49 n
     2 2           18 I           34 Y           50 o
     3 3           19 J           35 Z           51 p
     4 4           20 K           36 a           52 q
     5 5           21 L           37 b           53 r
     6 6           22 M           38 c           54 s
     7 7           23 N           39 d           55 t
     8 8           24 O           40 e           56 u
     9 9           25 P           41 f           57 v
    10 A           26 Q           42 g           58 w
    11 B           27 R           43 h           59 x
    12 C           28 S           44 i           60 y
    13 D           29 T           45 j           61 z
    14 E           30 U           46 k           62 _
    15 F           31 V           47 l           63 $
```

### 2.2. Encoding Process

To encode a UUID:
1. Interpret the UUID as a 128-bit big-endian integer
2. Process the bits from most significant to least significant
3. Group the bits into 21 groups of 6 bits and 1 final group of 2 bits
4. Encode each 6-bit group using the alphabet above
5. For the final 2-bit group:
   - Pad with 4 zero bits to form a complete 6-bit value
   - Encode using the alphabet
   - This results in exactly 22 characters

### 2.3. Decoding Process

To decode a Base64UUID string:
1. Verify the string is exactly 22 characters long
2. Decode each character using the alphabet to 6-bit values
3. For the final character, take only the 2 most significant bits
4. Concatenate all bits to reconstruct the 128-bit UUID

## 3. Encoding Examples

| UUID                                      | Base64UUID Encoding       |
| ----------------------------------------- | ------------------------- |
| 00000000-0000-0000-0000-000000000000     | 0000000000000000000000    |
| ffffffff-ffff-ffff-ffff-ffffffffffff      | $$$$$$$$$$$$$$$$$$$$$$    |
| 019535d9-3df7-79fb-b466-fa907fa17f9e     | 0FJNXZ7d97nvG2qX6QX6F$g   |
| 123e4567-e89b-12d3-a456-426614174000     | BI5FZz6JGxKkVkQmFQXwAA    |

## 4. Properties

### 4.1. Sort Preservation

The encoding preserves the numerical order of UUIDs when compared lexicographically as strings due to the monotonic mapping between bit values and character codes.

### 4.2. URL Safety

All characters in the alphabet are permitted in URL paths and query parameters without percent-encoding, satisfying URL-safe requirements.

### 4.3. File System Compatibility

The encoding is compatible with major file systems including Windows, Linux, Android, and iOS, as it excludes prohibited characters (`/`, `\`, `:`, `*`, `?`, `"`, `<`, `>`, `|`).

### 4.4. Text Selection

The alphabet supports complete text selection on double-click in SQL development environments:

**Fully Supported SQL Environments:**
- DBeaver (version 24.1+) - full string selection
- DataGrip (2024.1+) - full string selection  
- Azure Data Studio (1.48+) - full string selection
- Google BigQuery Console - full string selection
- Snowflake Web Interface - full string selection

**Partial Support (with limitations):**
- Notepad++ (8.6+) - `$` character breaks word selection in SQL context
- Visual Studio Code - `$` character breaks word selection in SQL files
- SQL Server Management Studio - `$` character breaks word selection

In supported environments, both `_` and `$` are treated as word characters, enabling complete selection of Base64UUID strings. In environments with partial support, selection stops at the `$` character.

## 5. Security Considerations

This encoding does not introduce additional security considerations beyond those of UUIDs themselves. Implementations should ensure proper validation of encoded strings before decoding.

## 6. References

- [RFC9562](https://datatracker.ietf.org/doc/html/rfc9562) Universally Unique IDentifiers (UUIDs)
- [RFC4648](https://datatracker.ietf.org/doc/rfc4648/) The Base16, Base32, and Base64 Data Encodings

## 7. Acknowledgements

The idea for this encoding belongs to Jelte Fennema-Nio, who proposed it for UUID under the name "base64urlhex".

The alphabet was chosen and the original text was composed by Sergey Prokhorenko with the assistance of DeepSeek.
