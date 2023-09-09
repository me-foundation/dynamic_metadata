# Dynamic Metadata Standard

Dynamic metadata has emerged as a transformative tool in the digital art space. It empowers creators to integrate real-time off-chain data, thereby enriching the on-chain metadata of their creations. What's even more compelling is its full compatibility with the established metadata standards on ETH, SOL, and BTC platforms.

In the rapidly evolving NFT landscape, features like dynamic metadata are becoming indispensable. We're excited to offer robust support for this, enhancing both the depth and richness of the information available to users. This not only elevates the value of the NFT but also provides a more immersive experience.

To ensure a seamless integration, creators can also append custom "attributes" metadata to their individual assets. For the dynamic metadata to be accurately displayed for each NFT, it's essential that your API retrieves the relevant information using the digital asset's `asset_id`.

Here's a spec of the DynamicMetadata standard we proposed. It's compatible with the existing onchain metadata JSON schema.

```typescript
interface Attribute {
  trait_type: string;
  value: string;
}

interface DynamicMetadata {
  name: string;              // required field, we use name as the display value of the dynamic metadata of the asset
  attributes?: Attribute[];  // optional field for detailed attributes
  asset_id?: string;         // optional echo back the requested asset_id
  description?: string;      // optional description field
  image?: string;            // optional url to the image.
  expiration_epoch?: number; // optional expiration epoch timestamp unit in seconds
}
```

## Properties

| Property         | Type                          | Description                                                                                                                                                                          |
| :--------------- | :---------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| name             | string (required)             | `name` indicates the value you'd like to show on each NFT                                                                                                                                   |
| asset_id         | string (optional)             | `asset_id` echos back the requested `:asset_id` from the URL path. It indicates the asset_id associated with the metadata. <ul><li>For SOL: `${mintAddress}`,</li> <li>For ETH: `${smart_contract}/${token_id}`,</li> <li>For BTC: `${inscription_id}`</li> |
| attributes       | Attribute[] (optional)        | The dynamic attributes for the metadata.                                                                                                                                   |
| description      | string (optional)             | Rich information for users to understand the attributes                                                                                                                     |
| image            | string (optional)             | URL for custom attribute icons. Recommended size: 20x20 pixels in SVG or PNG format                                                                                                  |
| expiration_epoch | number (optional)             | Expiration date for your dynamic attributes (Unix timestamp in seconds)                                                                                                |

## Server Implementation

Highly recommend the server implement the REST api that can respond to the following curl. 

```
curl https://HOST:PORT/dynamic-metadata/:asset_id
```

### Node Server Example

```
const express = require('express');
const app = express();
const PORT = 3000;

app.use(express.json());

app.get('/dynamic-metadata/:asset_id', (req, res) => {
    const asset_id = req.params.asset_id;
    // Fetch or compute the dynamic metadata for the given asset_id
    const dynamicMetadata = {
        name: "Points 123/200",
        attributes: [
            { trait_type: "color", value: "red" }
        ],
        asset_id: asset_id,
        description: "Sample Description",
        image: "https://example.com/image.jpg",
        expiration_epoch: Date.now() + 3600
    };
    res.json(dynamicMetadata);
});

app.listen(PORT, () => {
    console.log(`Server started on http://localhost:${PORT}`);
});
```

### Golang Server Example

```
package main

import (
	"encoding/json"
	"net/http"
)

type Attribute struct {
	TraitType string `json:"trait_type"`
	Value     string `json:"value"`
}

type DynamicMetadata struct {
	Name            string     `json:"name"`
	Attributes      []Attribute `json:"attributes,omitempty"`
	AssetID         string     `json:"asset_id,omitempty"`
	Description     string     `json:"description,omitempty"`
	Image           string     `json:"image,omitempty"`
	ExpirationEpoch int64      `json:"expiration_epoch,omitempty"`
}

func handler(w http.ResponseWriter, r *http.Request) {
	assetID := r.URL.Path[len("/dynamic-metadata/"):]
	dynamicMetadata := DynamicMetadata{
        Name: "Points 123/200",
		Attributes: []Attribute{
			{TraitType: "color", Value: "red"},
		},
		AssetID:         assetID,
		Description:     "Sample Description",
		Image:           "https://example.com/image.jpg",
		ExpirationEpoch: 1234567890,
	}
	w.Header().Set("Content-Type", "application/json")
	json.NewEncoder(w).Encode(dynamicMetadata)
}

func main() {
	http.HandleFunc("/dynamic-metadata/", handler)
	http.ListenAndServe(":8080", nil)
}
```

### Python Server Example

```
from flask import Flask, jsonify, request

app = Flask(__name__)

@app.route('/dynamic-metadata/<asset_id>', methods=['GET'])
def get_dynamic_metadata(asset_id):
    dynamic_metadata = {
        "name": "Points 123/200",
        "attributes": [
            {"trait_type": "color", "value": "red"}
        ],
        "asset_id": asset_id,
        "description": "Sample Description",
        "image": "https://example.com/image.jpg",
        "expiration_epoch": 1234567890
    }
    return jsonify(dynamic_metadata)

if __name__ == '__main__':
    app.run(port=5000)
```

## Detailed Walkthrough with Example

- name

  - Type: string; required.
  - Description: Represents the value you'd like us to display for each NFT. For customization, we currently ONLY support the string type.

    <img src="https://bafkreidzb67l2csqvu2pglbft4kvhwo7owg64m25w5xbtbjkd263cokdnm.ipfs.nftstorage.link/" width="400" alt="How name will be rendered">

- asset_id

  - Type: string; optional.
  - Description: Indicates the asset_id associated with the metadata.
    - For SOL: `${mintAddress}`
    - For ETH: `${smart_contract}/${token_id}`
    - For BTC: `${inscription_id}`

- attributes
  - Type: Attribute[]; optional.
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

- description

  - Type: string; optional.
  - Description: Used to provide an additional description for the name. Auto-generated format if left null: {trait_type} is ${value} from the attributes array.

    <img src="https://bafybeieeeu46xfwd6ttsxrftxm5rw7fgg6caqqucq36rii7naem74w24pm.ipfs.nftstorage.link/" width="400" alt="How description will be rendered">

- image

  - Type: string; optional.
  - Description: Adds custom icons for your metadata. We recommend icons of size 20x20 pixels in SVG or PNG format.

    <img src="https://bafkreie4f53go5oqkd33cwrfccyjitjxp2osw6e5m6dikhqlqptrglg6ye.ipfs.nftstorage.link/" width="400" alt="How image will be rendered">

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

<img src="https://bafkreibno3oixn3anoioppvrcwk2sl42kqibho6evdomhzlkhfs7fmtvha.ipfs.nftstorage.link/" width="400" alt="How everything works together">
