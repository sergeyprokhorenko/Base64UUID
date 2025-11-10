# The Base64UUID Encoding and Choice of Encoding for UUID

## Abstract

This specification defines the Base64UUID encoding, a compact, sortable, and URL-safe case-sensitive text representation of UUIDs using a 64-character ASCII alphabet. The encoding is designed specifically for UUID representation rather than general-purpose data encoding.

## 1. Introduction

Base64UUID provides a text encoding for UUIDs that preserves their natural sort order while maintaining compatibility with URLs, file systems, and text selection interfaces.

## 2. Requirements Notation

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "NOT RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC2119](https://datatracker.ietf.org/doc/html/rfc2119) when, and only when, they appear in all capitals, as shown here.

## 3. Choice of Encoding for UUID

The Base64UUID encoding MUST be used, if possible. Otherwise, the standard 36-character hexadecimal format is RECOMMENDED due to its wide compatibility and ease of dictation.

## 4. The Base64UUID Encoding

### 4.1. The Base64UUID Alphabet

The Base64UUID encoding has a character set that is distinct from both Base64 (RFC 4648 Section 4) and Base64url (RFC 4648 Section 5).

The 64-character alphabet MUST be:
`$0123456789ABCDEFGHIJKLMNOPQRSTUVWXYZ_abcdefghijklmnopqrstuvwxyz`

Implementations MUST use the following mapping between 6-bit values in decimal format and characters for Base64UUID encoding:
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

Base64UUID strings MAY be enclosed in double quotes (U+0022, `"`) when necessary. Decoders MUST accept both quoted and unquoted forms of Base64UUID strings and remove the quotes before processing the Base64UUID value.

### 4.2. Encoding Process

To encode the standard 36-character hexadecimal format into a Base64UUID string:
1. Convert the UUID from standard 36-character hexadecimal format to 128-bit binary value. Validation of the UUID is RECOMMENDED unless they are originally in 128-bit binary format. In a SQL query, if conversion or validation fails, the encoder MUST return NULL.
3. Prepend the 4-bit prefix `0100` to the entire 128-bit UUID, forming a 132-bit value. This prefix ensures the encoded string starts with a letter.
4. Split the 132-bit value into 22 groups of 6 bits.
5. Map each 6-bit group to a character from the Base64UUID alphabet.
6. Concatenate these characters to produce the final 22-character Base64UUID string.

### 4.3. Decoding Process

To decode a Base64UUID string back to its standard 36-character hexadecimal format:
1. Decode the 22-character Base64UUID string to retrieve a 132-bit value. Base64UUID string validation is RECOMMENDED. In a SQL query, if decoding or validation fails, the decoder MUST return NULL.
2. Convert the 128 least significant bits back into the standard 36-character hexadecimal format of UUID.

## 5. Base64UUID Encoding Examples

| UUID                                      | Base64UUID Encoding       |
| ----------------------------------------- | ------------------------- |
| 00000000-0000-0000-0000-000000000000     | F$$$$$$$$$$$$$$$$$$$$$    |
| ffffffff-ffff-ffff-ffff-ffffffffffff      | Izzzzzzzzzzzzzzzzzzzzz    |

## 6. Base64UUID Encoding Properties

### 6.1. Compact String Length

The Base64UUID encoding produces a compact string of only 22 characters, significantly shorter than other common UUID representations. This compactness is achieved by using a 64-character alphabet.

| Alphabet Size | String Length for 128-bit UUID |
| :--- | :--- |
| 16 | 32 hex digits + 4 hyphens = 36 total |
| 32 | 26 |
| 36 | 25 |
| 64 | 22 |

### 6.2. Sorts the Same as Binary

The encoding preserves the numerical order of UUIDs when compared lexicographically as strings due to the monotonic mapping between bit values and character codes.

### 6.3. URL Safety

If system configuration permits, Base64UUID strings in URLs MAY retain the `$` character without percent-encoding. Otherwise, percent-encoding (`%24`) is REQUIRED. Implementations MUST explicitly declare which approach they support.

### 6.4. XML Safety

The `$` character in Base64UUID strings MAY be used unescaped in XML element content and attribute values.

The `$` character in Base64UUID strings is prohibited in XML element names, attribute names, and NCName contexts (e.g., namespace prefixes), where it MUST be replaced with a hyphen-minus (U+002D, `-`).

### 6.5. File System Compatibility

The encoding is compatible with major file systems including Windows, Linux, Android, macOS and iOS, as it excludes prohibited characters (`/`, `\`, `:`, `*`, `?`, `"`, `<`, `>`, `|`).

### 6.6. Double Click to Copy

The alphabet supports complete text selection in modern development environments and database tools.

*   **Full Support:** Available in most current versions of dedicated SQL clients and cloud query consoles.
*   **Partial Support:** In some text editors and environments text selection may break at the `$` character.

### 6.7. Starts with a Letter

All encoded strings start with letters. This is achieved by right-shifting the 128-bit UUID by 4 bits and prefixing `0100` bits before Base64 encoding. The transformation is reversible and preserves lexical sort order of the original UUIDs.

### 6.8. Computational Efficiency

Base64UUID is significantly more performant for encoding and decoding than encodings with non-power-of-two alphabet sizes (36, 48, 52, 58, or 62). It operates on the UUID's 128-bit raw binary data using simple bitwise transformations to a base64 representation. In contrast, non-power-of-two encodings interpret the entire UUID as a large integer and require computationally expensive iterative big-integer division and modulus operations.

## 7. Security Considerations

Base64UUID encoding does not introduce additional security considerations beyond those of UUIDs themselves.

## 8. References

- [RFC9562](https://datatracker.ietf.org/doc/html/rfc9562) Universally Unique IDentifiers (UUIDs)
- [RFC4648](https://datatracker.ietf.org/doc/rfc4648/) The Base16, Base32, and Base64 Data Encodings
- [RFC2119](https://datatracker.ietf.org/doc/html/rfc2119) Key words for use in RFCs to Indicate Requirement Levels
