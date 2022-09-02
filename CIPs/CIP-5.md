---
CIP: 5
title:  URI and Metadata Specification
status: Living
type: Standards Track CIP(Protocol)
created: 2022-7-26
---
# Instance URI

As we mentioned before, it’s possible to link to any object using a URI as long as by which it can be identified. Straightforwardly, we can use a `comment` type link to comment on some article, an image, or a video by their URLs. But what if we want to comment on an NFT on Ethereum, or on Polygon? Easy! Design a universal URI for all network assets! 

Such URI is designed as:

```bash
csb://asset:<contract_address>-<token_id>@<network>
```

Take an example,  `csb://asset:0x5452c7fb99d99fab3cc1875e9da9829cb50f7a13-753@ethereum` identifies an Ethereum NFT on contract address `0x5452c7fb99d99fab3cc1875e9da9829cb50f7a13` with tokenId `753` . And if we set `toUri` as `csb://asset:0x5452c7fb99d99fab3cc1875e9da9829cb50f7a13-753@ethereum` and `linkType` as `comment` when calling `postNote4AnyLink()`, we are exactly commenting on that NFT!

Okay, now you might think, that just commenting on an asset is not so cool. You also wanna directly post some comments on an Ethereum address, or a Twitter account. Why not? Then let’s design a universal URI for all platform accounts:

```bash
csb://account:<identity>@<account_platform>
```

With this design, we can pass 

`csb://account:Atlasoin@twitter` identifies twitter account with id `Atlasoin` and `csb://account:0xc560eb6fd0c2eb80Df50E5e06715295AE1205049@ethereum` identifies the Ethereum account `0xc560eb6fd0c2eb80Df50E5e06715295AE1205049`. Similarly, now if we set `toUri` as `csb://account:Atlasoin@twitter` and `linkType` as `comment` when calling `postNote4AnyLink()`, we are exactly commenting on that Twitter account.


# Metadata JSON Schema

Interactions between different instances have been strictly defined and processed by a set of smart contracts. Whereas in most cases information itself are not on-chain and only URIs pointed to them are on-chain. 

Though it suggests pretty much data flexibility and extensibility, it’s still suggested for third-party applications to share the same JSON schema standard for better interoperability. Here we propose our suggested JSON schemas to set for character metadata and note metadata, which is defined to serve as the corresponding NFT metadata as well.

Originally [ERC721](https://eips.ethereum.org/EIPS/eip-721) token metadata is proposed to be simple and has an image attribute, but we all see that today’s NFT entities are no longer limited to images anymore and contain more complicated information. To help third parties better interpret the NFT information directly from the token URI, there’s a `type` attribute in each definition.

## Character Metadata

The specification defined in TypeScript is as follows:

```tsx
type BaseURI = string; // as URI defined in RFC 3986.
type URIx = {
		uri: BaseURI;
		extensions?: [];
}; 
type MediaURIx extends URIx = {
		mime_type?: string; 
		size_in_bytes?: number;
}
type MediaURI = MediaURIx | BaseURI;

type AccountURIx extends URIx = {
		extra?: any; // probably used for proof 
}
type AccountURI = AccountURIx | BaseURI;

type AssetURIx extends URIx = {
		cached_resource?: string;
}
type AssetURI = AssetURIx | BaseURI;

type URI = URIx | BaseURI;

type CharacterMetadata = {
	type: "csb-character"; //TODO
    name?: string;
    avatars?: MediaURI[]; 
    bio?: string;
    banners?: MediaURI[];
    websites？: URI[];
    connected_accounts?: AccountURI[];
    connected_avatars?: AssetURI[];
}
```

Considering the character on Crossbell could be edited by not only one application, each URI could be extended with `extensions` attribute to include more information for different apps to interpret. E.g. different applications could ask user to set different rendering preference for each avatar URI.

Typically avatars and banners uses media resources. For better rendering experience in the front end, `mime_type` and `size_in_bytes` could be useful for a media resource. Websites are typically supposed to be resources in `text/html` type.

`connected_accounts` are account resources, supposed to be in URI specification we defined above, e.g. `csb://acount:Atlasoin@twitter`

`connected_avatars` is useful when user wants to pick some ones from his own NFTs to be his avatars. So typically `connected_avatars` are asset resources, supposed to be in URI specification we defined above, e.g. `csb://asset:0x5452c7fb99d99fab3cc1875e9da9829cb50f7a13-753@ethereum`. Considering it might be inconvenient to interpret the image URL behind the NFT at each time, we introduce the `cached_resource` to cache the URL of the real media resource used for the avatar.

Here’s an example:

```json
{
	"type": "csb-character",
    "name": "Atlas",
    "avatars": ["ipfs://QmNks3cJWDXzG5WSBzTVv1regkhXhzChK6LhJYu1HiBLS2"],
    "bio": "We shall meet in the place with no darkness.",
    "websites": ["https://rss3.io", "https://atlasoin.xyz"],
    "banners": ["https://images.pexels.com/photos/933054/pexels-photo-933054.jpeg"],
    "connected_accounts": ["csb://account:Atlasoin@twitter", "csb://account:0xc560eb6fd0c2eb80Df50E5e06715295AE1205049@ethereum"],
    "connected_avatars": [
				{
            "uri": "csb://asset:0x5452c7fb99d99fab3cc1875e9da9829cb50f7a13-753@ethereum",
						"cached_resource": "ipfs://QmSX9QiwjTGBk5m22UscTg3vrbMwUfFsmxVzMH57hkPD5U/753.png"
            "extensions": [
								"Piro": {
										"syncAt": "2022-05-17T00:00:00.000Z"
								}
						]
				}
    ]
}
```

Another example with proof of connected accounts is:

```jsx
{
    "name": "Atlas",
    "avatars": ["ipfs://QmNks3cJWDXzG5WSBzTVv1regkhXhzChK6LhJYu1HiBLS2"],
    "bio": "We shall meet in the place with no darkness.",
    "websites": ["https://rss3.io", "https://atlasoin.xyz"],
    "banner": ["https://images.pexels.com/photos/933054/pexels-photo-933054.jpeg"],
    "connected_accounts": [
        {
            "uri": "csb://account:Atlasoin@twitter",
            "extra": {
                "signature_paylaod": "character_owner + 'csb://account:Atlasoin@twitter' + timestamp",
                "signature": "Sig(signature_paylaod)",
                "witness": "Pubkey_Of_NSL",
                "algorithm": "TBD"
            }
        },
        {
            "uri": "csb://account:atlas@nya.one@misskey",
            "extra": {
                "signature_paylaod": "character_owner + 'csb://account:atlas@nya.one@misskey' + timestamp",
                "signature": "Sig(signature_paylaod)",
                "witness": "Pubkey_Of_NSL",
                "algorithm": "TBD"
            }
        },
        {
           "uri": "csb://account:0xc560eb6fd0c2eb80Df50E5e06715295AE1205049@ethereum",
           "extra": {
               "signature_paylaod": "character_owner + 'csb://account:0xc560eb6fd0c2eb80Df50E5e06715295AE1205049@ethereum' + timestamp",
               "signature": "Sig(signature_paylaod)",
               "witness": "Pubkey_Of_NSL",
               "algorithm": "TBD"
           }
        }
    ],
    "connected_avatars": [
        {
            "uri": "rss3://asset:0x5452c7fb99d99fab3cc1875e9da9829cb50f7a13-753@ethereum",
            "cached_resource": "ipfs://QmSX9QiwjTGBk5m22UscTg3vrbMwUfFsmxVzMH57hkPD5U/753.png"
        }
    ]
}
```

## Note Metadata

```tsx
type NoteMetadata = {
    tags?: string[];
    title?: string;
    type?: "note" | "linklist" | "character"
    content?: string; // In markdown format 
    content_warning?: "nsfw" | "sensitive" | "spoiler"
    attachments?: ([NoteMetadataAttachmentBase]<"address"> | [NoteMetadataAttachmentBase]<"content">)[]
    attributes?: { 
        display_type?: "string" | "number" | "boolean" | "date"
        trait_type?: string; value: null | string | number | boolean 
    }[]
    external_urls?: string[]
    sources?: string[]
    date_published?: string
};
```
For long content, we suggest packing all content with markdown format in `title` and `content` attribute. For short content, like Twitter or Instagram style content, it’s suggested to use `content` and save attached images/videos in `attachments`.

To make it clear, the `external_urls` here could be the third party urls where the notes are synced from, like when users sync posts on xLog, the `external_urls` should be urls of the original xLog posts, but when users post comments on other urls, for example a comment on someone's tweets, the urls of the tweets should be put as `targetUri`.

Here’s an example:

```tsx
{
    "tags": ['article', 'dairy'],
    "title": "My Thoughts Start From Here",
    "content": "So boring the story is.\n\nSo boring that I don’t want to know the next.",
    "external_urls": ['https://xlog.app/xxxxxx'],
    "sources": ['xlog']
}
```

## Achievement Metadata

```tsx
type AchievementMeatadata = {
    name?: string;
    description?: string;
    image?: string;
    attributes?: { 
        tier?: "string"
    }[]
};
```

Achievements are released by crossbell.io as Character-Bound-Token NFTs to celebrate and showcase your journey on Crossbell, giving you a chance to show off your experiences and accomplishments. Detailed rules and specifications will be released on crossbell.io soon.

`name` here is the title of your achievement, e.g. `talking point` or `influencer`. `tier` indicates version of your achievement, which could be `base`, `bronze`, `silver`, `gold` or `special`.

Here’s an example:


Here’s an example:

```tsx
{
    "name": "talking point",
    "description": "I got 10 comments/likes on Crossbell!",
    "image": "ipfs://xxx",
    "attributes": [{
        "tier": "bronze"
    }]
}
```