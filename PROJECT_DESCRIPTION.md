Project Description
Deployed Frontend URL: https://sol-journal-two.vercel.app/
Solana Program ID: EJTGjYQmVnedbzSTHGoqx67n5Pe4w9hnYa72C8DkBx3t

Project Overview
Description
"Solana Journal" is a decentralized content application built on the Solana blockchain. It allows users to create persistent, on-chain journal entries where they are the sole owners of the data. Unlike Web2 journaling apps, data here is censorship-resistant and stored permanently on the blockchain. This dApp demonstrates core Solana concepts including Program Derived Addresses (PDAs), space allocation, and React-to-Anchor integration.

Key Features

Feature 1: Create Entry- Users can mint a new Journal Entry account by providing a unique title and message.
Feature 2: Update Entry- Users can modify the message content of their existing entries.
Feature 3: Delete Entry- Users can close the entry account and refund the rent (SOL) back to their wallet.
Feature 4: View Entries- Automatic fetching and display of all on-chain entries owned by the connected wallet.
...
How to Use the dApp
1.Connect Wallet: Click "Select Wallet" in the top right to connect Phantom or Solflare (Devnet).
2.Write Entry: Enter a unique "Title" and your "Message" in the creation form.
3.Mint: Click "Mint Entry". This will prompt your wallet to sign a transaction.
4.View: The list refreshes automatically to show your new on-chain data.
5.Refresh: Use the "Refresh List" button to re-fetch data directly from the blockchain.
...

Program Architecture
The Journal dApp uses a PDA-based architecture to manage data. Instead of a single large account holding all entries, every specific entry is its own independent account on the blockchain.

PDA Usage
The program uses Program Derived Addresses (PDAs) to deterministically locate entries without needing a centralized database index.

PDAs Used:

PDA 1: Journal Entry PDA, Derived from seeds [title_as_bytes, owner_public_key].
PDA 2: It's purpose is to ensure that every "Title" is unique per user. If a user tries to create an entry with a title they already used, the PDA collision detection will fail the transaction, enforcing data integrity.

Program Instructions

Instructions Implemented:
Instruction 1: create_entry Initializes a new PDA account, sets the owner, and saves the title/message data.
Instruction 2: update_entry Allows the owner to overwrite the message of an existing entry.
Instruction 3: delete_entry Closes the entry account and refunds the rent (SOL) back to the user.
...
Account Structure

#[account]
#[derive(InitSpace)]
pub struct JournalEntry {
    pub owner: Pubkey,      // The wallet that owns this entry
    #[max_len(50)]
    pub title: String,      // Unique identifier for this user
    #[max_len(1000)]
    pub message: String,    // The content of the journal
}

Testing
Test Coverage
I implemented a comprehensive TypeScript test suite (tests/anchor.test.ts) to verify the smart contract logic before frontend integration.

Happy Path Tests:
Test 1: Create Entry successfully mints a new PDA with the correct title and message.
Test 2: Update Entry verifies that the message can be changed on an existing account.
Test 3: Delete Entry verifies that the account is closed and data is removed.

Unhappy Path Tests:
Test 1: Prevent Duplicate, intentionally tries to create an entry with a title that already exists. The test confirms that the program correctly throws an error and rejects the transaction.

Running Tests
# Commands to run your tests
anchor test      # run tests in Solana Playground (https://beta.solpg.io/) or local environment

Additional Notes for Evaluators
This project was built using Solana Playground (https://beta.solpg.io) for the Smart Contract build, deployment and testing, and React/Vite for the Frontend.

I encountered significant challenges with the Anchor Provider and Wallet Adapter causing race conditions (specifically the _bn error) during page load. I solved this by implementing a "Raw Transaction" strategy in the frontend. Instead of relying on the Anchor Provider to sign, I manually constructed the transaction instructions using the IDL and sent them via the standard wallet.sendTransaction method. This ensured reliability across different wallet states and prevented common RPC timeouts.
