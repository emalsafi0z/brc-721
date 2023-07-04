# BRC-721: Non-Fungible Tokens on the Bitcoin Network

BRC-721 is inspired by the BRC-20, an experimental standard for creating fungible tokens on the Bitcoin network. BRC-20, which can be found at [brc-20-experiment](https://domo-2.gitbook.io/brc-20-experiment), aims to bring the functionality of issuing tokens to the Bitcoin ecosystem.

By building upon the ideas and principles of BRC-20, BRC-721 extends the capabilities of tokenization on the Bitcoin network to include non-fungible tokens, thus enabling a broader range of digital asset management and value representation.

BRC-721 is designed for non-fungible tokens (NFTs) on the Bitcoin network. It allows for the creation, ownership, and transfer of unique digital assets on the Bitcoin blockchain. Each token created under BRC-721 has a unique identifier, making them distinct and non-interchangeable.  


## Operations

### Deploy brc-721

Deploy NFT with an external base URI to provide metadata for each token:

``` json
{
    "p": "brc-721",
    "op": "deploy",
    "tick": "ordinals",
    "max": "10000",
    "buri": "https://ipfs.io/abc/"
}
```

* For token ID 1, the metadata is located at `https://ipfs.io/abc/1`

Deploy NFT with onchain metadata and make the collection immutable:

``` json
{
    "p": "brc-721",
    "op": "deploy",
    "tick": "ordinals",
    "max": "10000",
    "meta": {
        "name": "Ordinals",
        "description": "Bring NFT to Bitcoin", 
        "image": "https://storage.googleapis.com/opensea-prod.appspot.com/puffs/3.png",
        "attributes": [
            {
                "trait_type": "trait1", 
                "value": "value1"
            }, ... ]
    },
    "upd": false
}
```

* All tokens of the collections will share the same metadata.

| Key | Required? | Description |
|---|---|---|
| p | Yes | Protocol: Helps other systems identify and process brc-721 events |
| op | Yes | Operation: Type of event (deploy, mint, update) |
| tick | Yes | Ticker: identifier of the brc-721, more than 4 letters, case insensive |
| max | Yes | Max supply: set max supply of the brc-721 |
| upd | No | Whether the metadata is updatable, default is `true` |
| buri | No | BaseURI URI for the brc-721, access `{buri}{token_id}` for the metadata of a token |
| meta | No | The metadata of the collection |

* if `upd` is `true`, the deployer can update the metadata of the collection by sending an `update` inscription; otherwise, the metadata is immutable, any `update` op will be invalid.
