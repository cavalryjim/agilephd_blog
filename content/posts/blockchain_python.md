+++
title = 'Blockchain: A Python Example'
tags = ["blockchain"]
date = 2024-06-01
+++

Here is a simple Blockchain example written in Python.

### The Code

As a note, this code could be utilized in any coding environment but I would recommend a Jupyter Notebook.  A version of the code mentioned in this post can be viewed on [GitHub](https://github.com/cavalryjim/blockchain_example).

Import a few packages that will be useful.

```python
import hashlib
import json
from datetime import datetime
```

Start by defining the Block and Blockchain classes. 

#### Create the Block Class

The `Block` class will represent a single block in the blockchain.  Each block will contain the block's index, previous block's hash, timestamp, data (transactions), nonce, and it's own hash.  The `compute_hash` method calculates the SHA-256 hash of the block's contents.

```python
class Block:
    def __init__(self, index, previous_hash, timestamp, data, nonce=None):
        self.index = index
        self.previous_hash = previous_hash
        self.timestamp = timestamp
        self.data = data
        self.nonce = nonce
        self.hash = None

    def compute_hash(self):
        block_dict = self.__dict__
        if 'hash' in block_dict:
            del block_dict['hash']
        block_string = json.dumps(self.__dict__, sort_keys=True).encode()
        return hashlib.sha256(block_string).hexdigest()
```

#### Create the Blockchain Class

The `Blockchain` class will manage the chain of blocks and handle the consensus mechanism.  The class defines the following methods:
- `create_genesis_block` method initializes the chain with the genesis block.
- `last_block` method returns the most recent block in the chain.
- `add_block` method adds a new block to the chain after verifying its integrity and proof.
- `proof_of_work` method implements the Proof of Work consensus mechanism by finding a nonce that produces a hash with a specified number of leading zeros.
- `add_new_transaction` method adds a new transaction to the list of unconfirmed transactions.
- `mine` method creates a new block from unconfirmed transactions and adds it to the chain after solving the proof of work puzzle.
- `is_valid_proof` method verifies the validity of a block's proof.
- `check_chain_validity` method ensures the integrity of the entire blockchain by verifying each block's hash and proof.

```python
class Blockchain:
    difficulty = 2  # Proof of Work difficulty

    def __init__(self):
        self.unconfirmed_transactions = []
        self.chain = []
        self.create_genesis_block()

    def create_genesis_block(self):
        genesis_block = Block(0, None, str(datetime.now()), "Genesis Block", 0)
        genesis_block.hash = genesis_block.compute_hash()
        self.chain.append(genesis_block)

    def last_block(self):
        return self.chain[-1]

    def add_block(self, block, new_hash):
        previous_hash = self.last_block().hash

        if previous_hash != block.previous_hash:
            return False

        if not self.is_valid_proof(block, new_hash):
            return False

        block.hash = new_hash
        
        self.chain.append(block)
        return True
    
    def proof_of_work(self, block):
        block.nonce = 0
        computed_hash = block.compute_hash()
        while not computed_hash.startswith('0' * Blockchain.difficulty):
            block.nonce += 1
            computed_hash = block.compute_hash()
        
        return computed_hash

    def add_new_transaction(self, transaction):
        self.unconfirmed_transactions.append(transaction)

    def mine(self):
        if not self.unconfirmed_transactions:
            return False

        last_block = self.last_block()
        new_block = Block(index=last_block.index + 1,
                          previous_hash=last_block.hash,
                          timestamp=str(datetime.now()),
                          data=self.unconfirmed_transactions)

        proof = self.proof_of_work(new_block)
        self.add_block(new_block, proof)
        self.unconfirmed_transactions = []
        return new_block.index
    
    def is_valid_proof(self, block, block_hash):
        return (block_hash.startswith('0' * Blockchain.difficulty) and
                block_hash == block.compute_hash())

    def check_chain_validity(self):
        result = True
        previous_hash = None

        for block in self.chain:
            if block.index == 0:
                previous_hash = block.hash
                continue 
                
            block_hash = block.hash

            if not self.is_valid_proof(block, block_hash) or previous_hash != block.previous_hash:
                result = False
                break
                
            previous_hash = block_hash

        return result
```

#### Running the code

When running this example, it will create a simple Blockchain, add some transactions, mine new blocks, and print the Blockchain's contents. It also checks the validity of the entire chain to ensure its integrity.

```python
blockchain = Blockchain()
blockchain.add_new_transaction("James earned 10 LSU_tokens")
blockchain.mine()
blockchain.add_new_transaction("James pays Mike_the_tiger 5 LSU_tokens")
blockchain.mine()
```

```python
for block in blockchain.chain:
    print(f"Block {block.__dict__}")
```

```
Block {'index': 0, 'previous_hash': None, 'timestamp': '2024-08-08 14:34:59.958990', 'data': 'Genesis Block', 'nonce': 0, 'hash': 'ea5ca59ee5ce305d73c929e46f87b8ff3ce6884284f0352091360bef4c617673'}
Block {'index': 1, 'previous_hash': 'ea5ca59ee5ce305d73c929e46f87b8ff3ce6884284f0352091360bef4c617673', 'timestamp': '2024-08-08 14:34:59.959075', 'data': ['James earned 10 LSU_tokens'], 'nonce': 685, 'hash': '00deffc551853b93135329f117b063045109977649556b0b3e6837fabade4d00'}
Block {'index': 2, 'previous_hash': '00deffc551853b93135329f117b063045109977649556b0b3e6837fabade4d00', 'timestamp': '2024-08-08 14:34:59.964583', 'data': ['James pays Mike_the_tiger 5 LSU_tokens'], 'nonce': 74, 'hash': '008066be505213f2523624625587d8096d7e4f1fe6ede3eb4e0976d1720c9cb6'}
```

```python
print("Blockchain valid?", blockchain.check_chain_validity())
```

```
Blockchain valid? True
```

### Conclusion

This basic implementation demonstrates how blockchain can achieve consensus and maintain a secure, tamper-evident record, effectively solving the [Byzantine Generals Problem](https://blog.agilephd.com/posts/blockchain_byzantine/) in a distributed network.