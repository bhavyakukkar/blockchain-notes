
- Solana is an active blockchain and smart-contract platform that boasts much faster block speeds than Ethereum
- Solana uses the SOL (1 million "Lamports") token as its cryptocurrency
- Unlike Ethereum, the site of modification of a smart-contract (called **"program"** in Solana) is limited, to **"accounts"** (variable units of storage) that a program **owns**

- Account structure:
  ```rs
  pub struct Account {
      /// lamports in the account
      pub lamports: u64,
      /// data held in this account
      pub data: Vec<u8>,
      /// the program that owns this account. If executable, the program that loads this account.
      pub owner: Pubkey,
      /// this account's data contains a loaded program (and is now read-only)
      pub executable: bool,
      /// the epoch at which this account will next owe rent
      pub rent_epoch: Epoch,
  }
  ```

- There are two types of accounts, determined by the value of the `executable` flag on the account data:
  + Accounts with `executable: false` simply store data that is readable and writable
  + Accounts with `executable: true` are known as programs (i.e. smart contracts) and contain executable code  
    The executable program in these accounts can read and write from the data of accounts owned by them


# Tokens

A few commonly used instructions include:

+ **InitializeMint**: Create a new mint account to represent a new type of token.
+ **InitializeAccount**: Create a new token account to hold units of a specific type of token (mint).
+ **MintTo**: Create new units of a specific type of token and add them to a token account. This increases the supply of the token and can only be done by the mint authority of the mint account.
+ **Transfer**: Transfer units of a specific type of token from one token account to another.

