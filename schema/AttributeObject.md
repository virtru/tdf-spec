# Attribute Object

## What is this?

An Attribute Object is a JSON object containing information about a single attribute - a piece of data used to support the Trusted Data Format - to enforce access control on a particular file. The attribute holds information required by the client, Entity Attribute Service (EAS) and the KAS (Key Access Server) to answer fundamental questions, such as:

* Does an entity have this required attribute to decrypt a file? (See [Entity Object](EntityObject.md))
* What public key should be used in protecting the file's content?
* When there is a request to decrypt the file, what URL should be used for the KAS?

## How does it work?

When encrypting, the client determines which attributes an entity must have to decrypt the payload and applies those attributes to the file's [Policy Object](PolicyObject.md). The file's contents are encrypted using the entity's private key originally, then by "wrapping" (or encrypting) this private key with the `pubKey` found in the Attribute Object. This wrapped key is stored, along with the Attribute Object(s), in the file's [Manifest](manifest-json.md).

When a decrypt is requested, the KAS checks the [Policy Object](PolicyObject.md) against the [Entity Object](EntityObject.md) from the requesting client to ensure the attributes that an entity "has" satisfies those that an entity "needs". If this check succeeds, the KAS permits a decrypt operation and returns a valid key which the client can decrypt and use to expose the file contents.


## Example

```javascript
{
  "attribute": "https://example.com/attr/classification/value/topsecret",
  "isDefault": true,
  "displayName": "classification",
  "pubKey": "pem encoded public key of the attribute",
  "kasUrl": "https://kas.example.com/",
  "schemaVersion:": "x.y.z"
}
```

|Parameter|Type|Description|Required?|
|---|---|---|---|
|`attribute`|String|Also known as the "attribute url."  The unique resource name for the attribute represented as a case-insensitive URL string. This field must be both unique and immutable as it is the reference id for the attribute. The attribute URL string contains three pieces of information - the authority namespace (https://example.com), the attribute name (classification), and the attribute value (topsecret).|Yes|
|`isDefault`|Boolean|If "true" this flag identifies the attribute as the default attribute. If missing (preferred) or false then the attribute is not the default attribute.|No|
|`displayName`|String|A human-readable nickname for the attribute for convenience.|Yes|
|`pubKey`|PEM|PEM encoded public key for this attribute. Often other attributes will use the same pubKey.|Yes|
|`kasUrl`|URL|Base URL of a KAS that can make access control decisions for this attribute.|Yes|
|`schemaVersion`|String|Version number of the AttributeObject schema.|No|


## Version

The current schema version is `1.1.0`.
