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


### Mint brc-721

``` json
{
    "p": "brc-721",
    "op": "mint",
    "tick": "ordinals"
}
```

| Key | Required? | Description |
|---|---|---|
| p | Yes | Protocol: Helps other systems identify and process brc-721 events |
| op | Yes | Operation: Type of event (deploy, mint, update) |
| tick | Yes | Ticker: identifier of the brc-721, 4 to 8 letters, case insensive |

* token ID is generated from 1 to `max` according to the order of inscription IDs.

### Transfer brc-721

It's simple to transfer an brc-721 token, just send the inscription minted above to the receiver. There is no need to mint a transfer inscription before sending like brc-20.

### Update brc-721

``` json
{
    "p": "brc-721",
    "op": "update",
    "tick": "ordinals",
    "upd": false,
    "buri": "https://ipfs.io/abc/",
    "meta": { ... }
}
```

| Key | Required? | Description |
|---|---|---|
| p | Yes | Protocol: Helps other systems identify and process brc-721 events |
| op | Yes | Operation: Type of event (deploy, mint, update) |
| tick | Yes | Ticker: identifier of the brc-721, 4 to 8 letters, case insensive |
| upd | No | Whether the metadata is updatable, default is `true` |
| buri | No | BaseURI URI for the brc-721, access `{buri}{token_id}` for the metadata of a token |
| meta | No | The metadata of the collection |

* This operation should be only allowed for the owner of the deploy inscription
* The `update` inscription must be minted to the same address as the deploy inscription.
* Only `upd`, `buri` and `meta` can be updated.
* if `upd` is `true`, the deployer can update the metadata of the collection by sending an update inscription; otherwise, the metadata is immutable, any `update` op will be invalid after the inscription setting `upd` to `false`.

## State Changes

* NFT Deployer

  * The address holding the `deploy` inscription is the deployer
  * The receiving address of the first deploy inscription minting becomes the deployer
  * If the deploy inscription is transferred to a new address, the new address becomes the deployer

* Token ID

  * Similar to ERC721, each token in a BRC-721 collection has a unique ID
  * Each `mint` operation's inscription generates a token with a token ID ranging from 1 to `max` (the total supply defined in the deploy inscription) in the order of inscription ID
  * Minting inscriptions beyond the total supply are invalid
  * `mint` inscription ID should be larger than the deploy inscription ID

* Token Owner

  * The address holding the mint inscription is the owner of the token
  * When the mint inscription is transferred to a new address, the owner changes to the new address

* Transfer

  * Transfer the NFT token using `ord wallet send <ADDRESS> <INSCRIPTION ID>`

* Metadata

  * The metadata of the collection is immutable if `upd` is `false`
  * The metadata of the collection can be updated by the deployer if `upd` is `true`
  * The metadata of the collection is initialized by the `deploy` inscription
  * The metadata of the collection can be updated by the `update` inscription
  * The `update` inscription must be minted by the owner of `deploy` inscription and sent to the same address as the `deploy` inscription
  * `update` inscription ID should be larger than the deploy inscription ID
