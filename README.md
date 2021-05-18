# PyBlockchain 🐍 🧱 ⛓

A simple blockchain created in Python initially created via this hackermoon [tutorial](https://hackernoon.com/learn-blockchains-by-building-one-117428612f46). With the plan to build more features into the program, to help develop learnings on blockchain technology and more in depth Python concepts.

## Step 1: Building a Blockchain

#### new_block(self, proof, previous_hash=None)

Create a new Block in the Blockchain
- `:param proof: <int>` The proof given by the Proof of Work algorithm
- `:param previous_hash: (Optional) <str>` Hash of previous Block
- `:return: <dict>` New Block

#### new_transaction(self, sender, recipient, amount)
Creates a new transaction to go into the next mined Block.
- `:param sender: <str>` Address of the Sender
- `:param recipient: <str>` Address of the Recipient
- `:param amount: <int>` Amount
- `:return: <int>` The index of the Block that will hold this transaction

#### hash(block)

Creates a SHA-256 hash of a Block
- `:param block: <dict>` Block
- `:return: <str>`

#### proof_of_work(self, last_proof)
Simple Proof of Work Algorithm:
- Find a number p' such that hash(pp') contains leading 4 zeroes, where p is the previous p'
- p is the previous proof, and p' is the new proof
- `:param last_proof: <int>`
- `:return: <int>`

#### valid_proof(last_proof, proof)
Validates the Proof: Does `hash(last_proof, proof)` contain 4 leading zeroes?

- `:param last_proof: <int>` Previous Proof
- `:param proof: <int>` Current Proof
- `:return: <bool>` True if correct, False if not.

## Step 2: Our Blockchain as an API

We’re going to use the Python Flask Framework. It’s a micro-framework and it makes it easy to map endpoints to Python functions

We’ll create three methods:
- `/transactions/new` to create a new transaction to a block
- `/mine` to tell our server to mine a new block.
- `/chain` to return the full Blockchain

## Step 3: Interacting with our Blockchain

Mine a block by making a `GET` request to `http://localhost:5000/mine`

Create a new transaction by making a `POST` request to `http://localhost:5000/transactions/new` with a body containing our transaction structure:

```json
{
    'sender': '4203357f85a2ef8e5902cf1f1d263e9c',
    'recipient': 'someones-other-address',
    'amount': 10000
}
```

To inspect the full chain by requesting with a `GET` request to `http://localhost:5000/chain`

## Step 4: Consensus

### Registering new Nodes

Before we can implement a Consensus Algorithm, we need a way to let a node know about neighbouring nodes on the network. Each node on our network should keep a registry of other nodes on the network. Thus, we’ll need some more endpoints:

- `/nodes/register` to accept a list of new nodes in the form of URLs.
- `/nodes/resolve` to implement our Consensus Algorithm, which resolves any conflicts—to ensure a node has the correct chain.


#### register_node(self, address)
Add a new node to the list of nodes
- `:param address: <str>` Address of node. Eg. `http://192.168.0.5:5000`
- `:return: None`

#### valid_chain(self, chain)

Determine if a given blockchain is valid
- `:param chain: <list>` A blockchain
- `:return: <bool>` True if valid, False if not

#### resolve_conflicts(self)
This is our Consensus Algorithm, it resolves conflicts by replacing our chain with the longest one in the network.
- `:return: <bool>` True if our chain was replaced, False if not

### TODO: 

- [ ] Store transactions in a db 

I want to be putting the following in to the db from `http://localhost:5000/chain`

- `index` AS block #
- `timestamp` AS time
- `transactions.recipient` AS miner

```json
{
            "index": 2,
            "previous_hash": "f66f2671abbaff0966c99927ea2a4f41474871f7b093b0fe35ffac08fba3285a",
            "proof": 35293,
            "timestamp": 1621352461.6053782,
            "transactions": [
                {
                    "amount": 1,
                    "recipient": "0a3b0854b1124b4b93756fdc325d912f",
                    "sender": "0"
                }
            ]
        }
```

- [ ] Convert the unix timpstamp

Doing it like this will allow me to place `date` as a column and `time` as one.

```python
import datetime

timestamp = datetime.datetime.fromtimestamp(1621352461.6053782)

ts = timestamp.strftime("%Y-%m-%d, %H:%M:%S")

print(f"Block was mined on {ts}")
```

Thinking my table could be like this:
```sql
-- id | block_number | miner | ts | date | time
CREATE TABLE mined_blocks(
    transaction_id INTEGER PRIMARY KEY AUTOINCREMENT,
    block_number   INTEGER NOT NULL,
    miner          VARCHAR(255),
    ts             VARCHAR(255),
    date           VARCHAR(255),
    time           VARCHAR(255)
)
```
- [ ] Provide a web ui to display transactions
