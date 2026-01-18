# Quickstart: Your First Sui Smart Contract in 5 Minutes

**Time to complete:** ~5 minutes  
**Difficulty:** Beginner  
**Prerequisites:** Basic command line familiarity

## What You'll Build

In this quickstart, you'll:
1. Install the Sui CLI
2. Create your first Move package
3. Write a simple smart contract
4. Deploy it to the Sui testnet

By the end, you'll have a working smart contract that creates digital items on the Sui blockchain.

## Before You Begin

Make sure you have:
- A computer running macOS, Linux, or Windows (WSL)
- An internet connection
- 5 minutes of uninterrupted time

## Step 1: Install Sui CLI

The Sui CLI is your command-line interface for interacting with the Sui blockchain.

### macOS / Linux

```bash
cargo install --locked --git https://github.com/MystenLabs/sui.git --branch main sui
```

### Verify Installation

```bash
sui --version
```

You should see output like: `sui 1.x.x`

> **Tip:** If the command isn't found, you may need to add Cargo's bin directory to your PATH: `export PATH="$HOME/.cargo/bin:$PATH"`

## Step 2: Create a New Move Package

Move organizes code into packages. Let's create one:

```bash
sui move new my_first_package
cd my_first_package
```

This creates a new directory with this structure:

```
my_first_package/
├── Move.toml          # Package configuration
└── sources/           # Your Move source code goes here
```

## Step 3: Write Your First Smart Contract

Create a new file `sources/collectible.move` with this code:

```move
module my_first_package::collectible {
    use sui::object::{Self, UID};
    use sui::transfer;
    use sui::tx_context::{Self, TxContext};

    /// A digital collectible that anyone can create
    public struct Collectible has key {
        id: UID,
        name: vector<u8>,
        description: vector<u8>,
    }

    /// Create a new collectible
    public entry fun create(
        name: vector<u8>,
        description: vector<u8>,
        ctx: &mut TxContext
    ) {
        let collectible = Collectible {
            id: object::new(ctx),
            name,
            description,
        };
        transfer::transfer(collectible, tx_context::sender(ctx));
    }
}
```

### What This Code Does

- **Module declaration** - Defines a module named `collectible` in your package
- **Struct definition** - Creates a `Collectible` type with an ID, name, and description
- **Function** - `create()` lets anyone make a new collectible and transfer it to themselves

> **Key Concept:** In Sui, objects must have the `key` ability to be stored on-chain. The `UID` gives each object a unique identifier.

## Step 4: Build Your Package

Compile your Move code to check for errors:

```bash
sui move build
```

If successful, you'll see:

```
BUILDING my_first_package
```

**Common errors:** If you get compilation errors, check that your code exactly matches the example above.

## Step 5: Set Up Your Sui Account

Create a new Sui address to deploy from:

```bash
sui client new-address ed25519
```

Switch to the testnet:

```bash
sui client switch --env testnet
```

Get some test SUI tokens (you'll need these to pay gas fees):

```bash
sui client faucet
```

Check your balance:

```bash
sui client gas
```

## Step 6: Deploy to Testnet

Publish your package to the Sui testnet:

```bash
sui client publish --gas-budget 50000000
```

You'll see output including:

```
----- Transaction Digest ----
<transaction-hash>

----- Published Package ----
PackageID: 0x<your-package-id>
```

**🎉 Congratulations!** Your smart contract is now live on the Sui testnet.

## Step 7: Interact with Your Contract

Create your first collectible:

```bash
sui client call \
  --package 0x<your-package-id> \
  --module collectible \
  --function create \
  --args "My First NFT" "This is my first digital collectible on Sui" \
  --gas-budget 10000000
```

Replace `<your-package-id>` with the PackageID from Step 6.

View your collectibles:

```bash
sui client objects
```

You should see your newly created collectible!

## What You've Learned

✅ How to install the Sui CLI  
✅ How to create a Move package  
✅ Basic Move syntax for smart contracts  
✅ How to deploy to the Sui testnet  
✅ How to interact with deployed contracts

## Next Steps

Now that you've deployed your first smart contract, you can:

- **Learn core concepts** - Understand [Sui's object model](../concepts/sui-objects.md)
- **Build something real** - Try the [Digital Collectibles Tutorial](../tutorials/digital-collectibles.md)
- **Explore the CLI** - Check out the [Sui CLI Reference](../api-reference/sui-cli.md)
- **Troubleshoot issues** - See [Common Errors](../troubleshooting/common-errors.md)

## Need Help?

- **Official Docs:** [docs.sui.io](https://docs.sui.io)
- **Discord:** [Sui Developer Community](https://discord.gg/sui)
- **GitHub:** [github.com/MystenLabs/sui](https://github.com/MystenLabs/sui)

---

**Next:** [Understanding Sui Objects](../concepts/sui-objects.md) →
