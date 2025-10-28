# Base64UUID: A Compact and Sortable UUID Encoding

## Abstract

This specification defines the Base64UUID encoding, a compact, sortable, and URL-safe text representation of UUIDs using a 64-character ASCII alphabet. The encoding is designed specifically for UUID representation rather than general-purpose data encoding.

## 1. Introduction

Base64UUID provides a text encoding for UUIDs that preserves their natural sort order while maintaining compatibility with URLs, file systems, and text selection interfaces.

## 2. The Base64UUID Encoding

### 2.1. Alphabet

The 64-character alphabet SHALL be:
`$0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ_abcdefghijklmnopqrstuvwxyz`

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
| 00000000-0000-0000-0000-000000000000     | $$$$$$$$$$$$$$$$$$$$$$    |
| ffffffff-ffff-ffff-ffff-ffffffffffff      | zzzzzzzzzzzzzzzzzzzzzk    |

## 4. Properties

### 4.1. Sort Preservation

The encoding preserves the numerical order of UUIDs when compared lexicographically as strings due to the monotonic mapping between bit values and character codes.

### 4.2. URL Safety

All characters in the alphabet are permitted in URL paths and query parameters without percent-encoding, satisfying URL-safe requirements.

### 4.3. File System Compatibility

The encoding is compatible with major file systems including Windows, Linux, Android, and iOS, as it excludes prohibited characters (`/`, `\`, `:`, `*`, `?`, `"`, `<`, `>`, `|`).

### 4.4. Text Selection

The alphabet supports complete text selection in popular SQL development environments:
- Full support: DBeaver (24.1+), DataGrip (2024.1+), Azure Data Studio (1.48+), Google BigQuery Console, Snowflake Web Interface.
- Partial support: Notepad++, VS Code, SQL Server Management Studio — $ character breaks word selection.

## 5. Security Considerations

This encoding does not introduce additional security considerations beyond those of UUIDs themselves. Implementations should ensure proper validation of encoded strings before decoding.

## 6. References

- [RFC9562](https://datatracker.ietf.org/doc/html/rfc9562) Universally Unique IDentifiers (UUIDs)
- [RFC4648](https://datatracker.ietf.org/doc/rfc4648/) The Base16, Base32, and Base64 Data Encodings

## 7. Acknowledgements

The idea for this encoding belongs to Jelte Fennema-Nio, who proposed it for UUID under the name "base64urlhex".

The alphabet was chosen and the original text was composed by Sergey Prokhorenko with the assistance of DeepSeek.
