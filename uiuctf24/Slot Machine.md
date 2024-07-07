## Task description
We have onboard entertainment! Try your luck on our newly installed slot machine.
`ncat --ssl slot-machine.chal.uiuc.tf 1337`

Also we're given the source code:
```
from hashlib import sha256

hex_alpha = "0123456789abcdef"

print("== Welcome to the onboard slot machine! ==")
print("If all the slots match, you win!")
print("We believe in skill over luck, so you can choose the number of slots.")
print("We'll even let you pick your lucky number (little endian)!")

lucky_number = input("What's your lucky (hex) number: ").lower().strip()
lucky_number = lucky_number.rjust(len(lucky_number) + len(lucky_number) % 2, "0")
if not all(c in hex_alpha for c in lucky_number):
    print("Hey! That's not a hex number! -999 luck!")
    exit(1)
hash = sha256(bytes.fromhex(lucky_number)[::-1]).digest()[::-1].hex()

length = min(32, int(input("How lucky are you feeling today? Enter the number of slots: ")))

print("=" * (length * 4 + 1))
print("|", end="")
for c in hash[:length]:
    print(f" {hex_alpha[hex_alpha.index(c) - 1]} |", end="")
print("\n|", end="")
for c in hash[:length]:
    print(f" {c} |", end="")
print("\n|", end="")
for c in hash[:length]:
    print(f" {hex_alpha[hex_alpha.index(c) - 15]} |", end="")
print("\n" + "=" * (length * 4 + 1))

if len(set(hash[:length])) == 1:
    print("Congratulations! You've won:")
    flag = open("flag.txt").read()
    print(flag[:min(len(flag), length)])
else:
    print("Better luck next time!")
```

## Solution
#### What does the code do?
Clearly its first asking us for a number n, then an n digit hex string and then its calculating its SHA-256 hash. If the first n characters of the hash are the same, we'll get our flag. 

#### What can **we** do?
Obviously we can't start with a hash and then find its primitive since SHA256 is cryptographic. (non reversible) Our best bet is therefore to literally just start guessing till we find a string where the same character is repeated many times. Unfortunately the probability of finding a string with the same char repeated, say 20 times is `1 in 2^(4*19)` That's literally 1 followed by 24 zeroes. 

#### The Saviour
Thankfully we're not the only ones doing this. The Bitcoin Cluster has also been obsessing over hashes with lots of zeroes for a long while now. We can search for the BTC hash with the most leading zeroes, or more precisely `smallest BTC hash` and find this:

![Smallest BTC Hashes](https://github.com/manogyasingh/ctf_writeups/blob/main/uiuctf24/MEDIA/Pasted%20image%20240707215701.png)

So BTC already has found a header which when hashed yields 24 digits of pure bliss (zeroes!)
We can now lookup `bitcoin hash 756951` on `lohkoketju.fi` to find all the info needed to make its header

![Info on Block 756951](https://github.com/manogyasingh/ctf_writeups/blob/main/uiuctf24/MEDIA/Pasted%20image%2020240707215746.png)

This guide on the structure of a BTC header helps us remake it
```
02000000 ........................... Block version: 2

b6ff0b1b1680a2862a30ca44d346d9e8
910d334beb48ca0c0000000000000000 ... Hash of previous block's header
9d10aa52ee949386ca9385695f04ede2
70dda20810decd12bc9b048aaab31471 ... Merkle root

24d95a54 ........................... [Unix time][unix epoch time]: 1415239972
30c31b18 ........................... Target: 0x1bc330 * 256**(0x18-3)
fe9f0864 ........................... Nonce
```

A quick script to make it easier:
 ```
import struct
import hashlib

# Helper function to convert a hexadecimal string to little-endian bytes
def hex_to_le_bytes(hex_str, length):
    return bytes.fromhex(hex_str)[::-1].ljust(length, b'\x00')

# Raw block data from your JSON
version = 541065216
previousblockhash = "000000000000000000050da0da9451c2e1306db4ddb5acc965fc1016678d9154"
merkleroot = "62c46f1efadf6e39b7463e5362bb552cba98f74a80a58378ff5194c7b058005a"
time = 1664846893
bits = "1708f9ae"
nonce = 3240300428

# Convert the fields to byte representations
version_bytes = struct.pack("<I", version)
previousblockhash_bytes = hex_to_le_bytes(previousblockhash, 32)
merkleroot_bytes = hex_to_le_bytes(merkleroot, 32)
time_bytes = struct.pack("<I", time)
bits_bytes = hex_to_le_bytes(bits, 4)
nonce_bytes = struct.pack("<I", nonce)

# Concatenate all the byte arrays to form the serialized block header
block_header = (
    version_bytes +
    previousblockhash_bytes +
    merkleroot_bytes +
    time_bytes +
    bits_bytes +
    nonce_bytes
)

# Compute the double SHA-256 hash
hash1 = hashlib.sha256(block_header).digest()
hash2 = hashlib.sha256(hash1).digest()

print(hash1[::-1].hex())

# Convert the final hash to hexadecimal (little-endian to big-endian)
block_hash = hash2[::-1].hex()

print(block_hash)
```

Which gives our lucky number as:
`93ccd10e30712e566e0bc0189c791e609b11fc17190b00eb50d6fa8b4909b2f5`

Putting it in the slot machine:
```
[manogya@cntr]-[~]
 > ncat --ssl slot-machine.chal.uiuc.tf 1337
== Welcome to the onboard slot machine! ==
If all the slots match, you win!
We believe in skill over luck, so you can choose the number of slots.
We'll even let you pick your lucky number (little endian)!
What's your lucky (hex) number: 93ccd10e30712e566e0bc0189c791e609b11fc17190b00eb50d6fa8b4909b2f5
How lucky are you feeling today? Enter the number of slots: 24
=================================================================================================
| f | f | f | f | f | f | f | f | f | f | f | f | f | f | f | f | f | f | f | f | f | f | f | f |
| 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
| 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
=================================================================================================
Congratulations! You've won:
uiuctf{keep_going!_3cyd}


```

And so we get out flag:
`uiuctf{keep_going!_3cyd}`
