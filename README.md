# pymst - Merkle-Sum-Tree in Python

## Usage:

Here is an example of creating a Merkle-Sum-Tree of size 2^64, getting the proof
for 3rd leaf and verifying it.

```py
from pymst import MerkleSumTree, Leaf

TREE_SIZE = 2 ** 64

leaves = [Leaf((0, 4), None), # None means the leaf is empty.
            Leaf((4, 10), b"tx1"),
            Leaf((10, 15), None),
            Leaf((15, 20), b"tx2"),
            Leaf((20, 90), b"tx4"),
            Leaf((90, TREE_SIZE), None)]

tree = MerkleSumTree(leaves)

root = tree.get_root()
proof = tree.get_proof(3)

if MerkleSumTree.verify_proof(root, leaves[3], proof):
  print("Proof is valid!")
else:
  print("Proof is not valid!")
```

## Integration with Solidity

Binary representation of proofs:

| Bytes  | Data                   |
|:------:|:----------------------:|
| 0-8    | Number of steps        |
| 8-9    | Step #1: Right or Left |
| 9-17   | Step #1: Bucket size   |
| 17-49  | Step #1: Bucket hash   |
| 49-50  | Step #2: Right or Left |
| 50-58  | Step #2: Bucket size   |
| 58-90  | Step #2: Bucket hash   |
| ...    | ...                    |

Notes:
- Number of steps is represented as a 8-byte big-endian unsigned integer.
- Each step is 41 bytes long.
- Bucket size is also a 8-byte big-endian unsigned integer.
- Bucket hash is a 32-byte hash.
- Left = `0x00` & Right = `0x01`.
