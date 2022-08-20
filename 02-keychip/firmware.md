# Firmware

The PCB includes a Winbond flash 512KB, the assumption is the flash will include program data or bootrom.

## Flash Dump

The Flash contains multiple regions:

```
address | size      | description
--------+-----------+------------
0x00000 | 16 blocks | logs (?)
0x10000 | 16 blocks | logs (?)
0x20000 | 16 blocks | logs (?)
0x30000 | 16 blocks | logs (?)
0x40000 | 16 blocks | logs (?)
0x50000 | 16 blocks | logs (?)
0x60000 | 16 blocks | logs (?)
0x70000 |  1 block  |
0x71000 |  1 block  |
0x72000 |  1 block  |
0x73000 |  1 block  |
0x74000 |  1 block  |
0x75000 |  1 block  |
0x76000 |  1 block  |
0x77000 |  1 block  |
0x78000 |  1 block  |
0x79000 |  1 block  |
0x7A000 |  1 block  | signatures (backup)
0x7B000 |  1 block  | signatures
0x7C000 |  1 block  | cryptographic material
0x7D000 |  1 block  |
0x7E000 |  1 block  |
0x7F000 |  1 block  |
```

### Logs

The first 7 regions are used for what appears to be logs.

Each log entry has a size of 0x40 bytes.

```
struct log_entry {
	uint8_t  unk[0x3C];
	uint32_t crc32; // in little endian
};
```

The first two log entries are bit maps to keep track of what blocks are used,
and what blocks are available.

```
00000000: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
00000010: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
00000020: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
00000030: 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00 00  ................
00000040: 00 00 00 00 00 00 00 00 00 00 00 00 03 ff ff ff  ................
00000050: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff  ................
00000060: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff  ................
00000070: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff fc  ................
```

In this particular example, the first `8 * 0x4D - 2` blocks are taken.

Note that the last two bits are always 0 (hence the fc).
This is because the bitmap is off by 2 (bit 0 represents offset 0x80).

The bitmap for a new block is:
```
00010000: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff  ................
00010010: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff  ................
00010020: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff  ................
00010030: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff  ................
00010040: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff  ................
00010050: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff  ................
00010060: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff  ................
00010070: ff ff ff ff ff ff ff ff ff ff ff ff ff ff ff fc  ................
```


### Cryptographic material

This block contains two encrypted structures of 0x800 bytes. These structures
are encrypted with `AES-128-CBC`.

```
struct crypted {
	uint32_t size;
	uint8_t  data[0x800 - sizeof(uint32_t)];
};
```

The first half contains a certificate authority (in ASN.1 format).

```
Issuer: C = JP, ST = Tokyo, L = Ohta, O = SEGA, OU = ALL.Net R&D
Validity
    Not Before: Apr  9 01:51:41 2009 GMT
    Not After : Jan 18 01:51:41 2038 GMT
Subject: C = JP, ST = Tokyo, L = Ohta, O = SEGA, OU = ALL.Net R&D
```

The second half contains a public RSA key (in ASN.1 format).
This key is probably different for each keychip. It is used to check the
validity of the signatures in the flash.

### Signatures

There are two blocks that contain signatures. Both block share the same
content for redundancy.

```
#define SIGNATURE_SIZE  0x80
#define SIGNATURE_COUNT 2

struct signature {
	uint32_t salt;
	uint8_t  data[SIGNATURE_SIZE];
};
_Static_assert(0x84 == sizeof(struct signature));

struct block_clear {
	uint32_t crc32;
	struct signature signatures[SIGNATURE_COUNT];
};
_Static_assert(0x10C == sizeof(struct block_clear));
```

The software will check if the hash of the *second* (7B) block is valid.

If it is not, it will check if the hash of the first (7A) block is valid.

If it is, the second page is restored. This hints that these pages are meant to
be modified to contain some kind of information.

The CRC-32 is calculated from byte 4 to byte 4096 (including NULL bytes)

```
$ tail -c +5 ./block7b.bin \
	| php -r 'printf("%08X\n", crc32(file_get_contents("php://stdin")));'
DBE92EF4

$ head -c 4 ./block7b.bin | xxd -g 4 -e
00000000: dbe92ef4
```

The signatures verify the SHA-1 hash of a 4-byte salt and the keychip's serial
number (without the -). The public key to verify this signature can be found in
block 7c. Both signature are the same, but with a different salt.

```
$ tail -c +$((1 + 8)) ./block7b.bin \
	| head -c $((0x80)) \
	| openssl rsautl -verify -pubin -inkey ./pubkey.pem -pkcs \
	| openssl asn1parse -i -inform der
    0:d=0  hl=2 l=  33 cons: SEQUENCE
    2:d=1  hl=2 l=   9 cons:  SEQUENCE
    4:d=2  hl=2 l=   5 prim:   OBJECT            :sha1
   11:d=2  hl=2 l=   0 prim:   NULL
   13:d=1  hl=2 l=  20 prim:  OCTET STRING      [HEX DUMP]:8C40F1...


$ printf '\x00\x04\x00\x00''***********' \
	| openssl dgst -sha1 -verify ./pubkey.pem -signature ./sig1.bin
Verified OK

$ printf '\x00\x02\x00\x00''***********' \
	| openssl dgst -sha1 -verify ./pubkey.pem -signature ./sig2.bin
Verified OK
```
