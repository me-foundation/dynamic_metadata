# Dynamic Metadata Standard

Dynamic metadata, such as utilities, plays a crucial role in the current NFT ecosystem. We now offers support for dynamic metadata, enriching the information provided. We also enable you to add customized "attributes" metadata to each of your assets.

To accurately display dynamic metadata for each NFT, your API needs to return the information based on the `mintAddress`.

```typescript
interface Attribute {
  trait_type: string;
  value: string;
}

interface DynamicMetadata {
  asset_id: string; // required
  attributes: Attribute[]; // required
  name?: string; // optional
  description?: string; // optional
  image?: string; // url to the image.
  expiration_epoch?: number; // unit: seconds
}

function getDynamicMetadata(mintAddress: string): DynamicMetadata {
  // Implementation here
}
```

## Properties

| Property         | Type                          | Description                                                                                                                                                                          |
| :--------------- | :---------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| asset_id         | string (required)             | Indicates the asset_id associated with the metadata. <ul><li>For SOL:`${mintAddress}`,</li> <li>For ETH: `${smart_contract}/${token_id}`,</li> <li>For BTC: `${inscription_id}`</li> |
| attributes       | Array of Attribute (required) | Indicates the dynamic attributes for the metadata                                                                                                                                    |
| name             | string (optional)             | Indicates the value you'd like to show on each NFT                                                                                                                                   |
| description      | string (optional)             | Provides rich information for users to understand the attributes                                                                                                                     |
| image            | string (optional)             | URL for custom attribute icons. Recommended size: 20x20 pixels in SVG or PNG format                                                                                                  |
| expiration_epoch | number (optional)             | Indicates the expiration date for your dynamic attributes (Unix timestamp in seconds)                                                                                                |

## Detailed Walkthrough with Example

- asset_id

  - Type: string; required.
  - Description: Indicates the asset_id associated with the metadata.
    - For SOL: `${mintAddress}`
    - For ETH: `${smart_contract}/${token_id}`
    - For BTC: `${inscription_id}`

- attributes
  - Type: Attribute[]; required.
  - Description: This section is crucial in defining the appearance of your dynamic metadata. For example:

```
[
  {
    "trait_type": "level",
    "value": "1"
  },
  {
    "trait_type": "points",
    "value": "200"
  }
]
```

- name

  - Type: string; optional.
  - Description: Represents the value you'd like us to display for each NFT. For customization, we currently ONLY support the string type.
    <img src="https://bafkreidzb67l2csqvu2pglbft4kvhwo7owg64m25w5xbtbjkd263cokdnm.ipfs.nftstorage.link/" width="200" alt="How name will be rendered">

- description

  - Type: string; optional.
  - Description: Used to provide an additional description for the name. Auto-generated format if left null: {trait_type} is ${value} from the attributes array.
    <img src="https://bafybeieeeu46xfwd6ttsxrftxm5rw7fgg6caqqucq36rii7naem74w24pm.ipfs.nftstorage.link/" width="200" alt="How description will be rendered">

- image

  - Type: string; optional.
  - Description: Adds custom icons for your metadata. We recommend icons of size 20x20 pixels in SVG or PNG format.
    <img src="https://bafkreie4f53go5oqkd33cwrfccyjitjxp2osw6e5m6dikhqlqptrglg6ye.ipfs.nftstorage.link/" width="200" alt="How image will be rendered">

- expiration_epoch
  - Type: number; optional.
  - Description: Specifies an expiration date for dynamic metadata. After the date, the metadata will not be visible on the NFT.

## Summary

With the above example of metadata, your API response for each asset will be:

```json
{
  "asset_id": "AzgPbTTQE3nVtDB8SycHypEyY1MCqJGELrZSAFUaDme6",
  "attributes": [
    {
      "trait_type": "level",
      "value": "1"
    },
    {
      "trait_type": "points",
      "value": "200"
    }
  ],
  "name": "1/200",
  "description": "This NFT has level 1 and points 200",
  "image": "https://....",
  "expiration_epoch": 1696796332
}
```

The example mint will be displayed accordingly.

<img src="https://bafkreibno3oixn3anoioppvrcwk2sl42kqibho6evdomhzlkhfs7fmtvha.ipfs.nftstorage.link/" width="200" alt="How everything works together">
