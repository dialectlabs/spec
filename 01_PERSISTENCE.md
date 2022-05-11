# Message Persistence

Dialect's current, `v0` protocol stores all messages on the Solana blockchain, and is implemented [here](https://github.com/dialectlabs/protocol).

The purpose of this Message Persistence document is to lay out near- and long-term plans for how web3 mmessages should be stored.

## The problem

Storing messages on the Solana blockchain has the benefit of being decentralized and, for a Solana-native project such as Dialect, keeping the engineering footprint relatively small. However, it poses several technical and UX limitations.

1. *Storage costs* — Storing data on the Solana blockchain requires depositing rent, at a current rate of about `$1/kb` (Dialect defaults to `10 kb` allocations for message threads). While rent is recoverable, this poses a significant psychological barrier, and undoubtedly contributes to users' and dapps' perception of what kind of messaging does and does not make sense. A notifications thread to be alerted when you're at risk of a `$50k` liquidation makes sense, a throw-away chat to message an artist about an NFT may not.
2. *Transaction fees* — Similar to storage costs, sending messages requires paying transaction fees. Nonzero transaction costs, no matter how small (currently less than $0.0005`) pose a significant barrier to use. While relayer solutions exist that can pass the transaction cost off to third parties, this adds engineering complexity to.
3. *Transaction signing - Security* — Storing messages on chain requires users to sign transactions to send messages. While some wallets support transaction auto-approval, this is generally frowned on, given the security risk it poses. In addition, since messaging may be embedded in all kinds of web3 dapps, not just those specific to messaging, it poses an additional security risk by adding another dimension signing dimension to the product. N.b. transaction signing should be distinguished from _message signing_, in which no transaction is involved, and the user simply proves they own a wallet address.
4. *Transaction signing - UX* —
5. *Cross-chain support* — Cross-chain web3 messaging is inevitable. Even if it messages were to be stored on Solana for Solana wallets, the question still remains whether
6. *Engineering iteration cycle* — 
