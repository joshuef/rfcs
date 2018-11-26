# RDF for the `_publicNames` Container

- Status: proposed
- Type: enhancement
- Related components: Safe Browser. Safe App Nodejs / Client FFI libs.
- Start Date: 26/11/2018
- Discussion: -
- Supersedes: -
- Superseded by: -

## Summary

This proposal looks to describe how we could structure the `_publicName` container using a resource description framework, and `sha3` hashing to derive XOR name locations.

## Conventions
- The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHALL NOT", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](http://tools.ietf.org/html/rfc2119).
- XOR-URL refers to a url generated as part of the content addressable system for accessing `xorname` urls in the safe_browser. As described here: https://forum.safedev.org/t/xor-address-urls-xor-urls/1952
- I'm using MutableData ( `MD` ) and ImmutableData ( `ID` ) as shorthands.


## Motivation

The aim here is to describe the `_publicNames` container, and how that would look when using [RDF data](https://en.wikipedia.org/wiki/Resource_Description_Framework) for our application data and the SAFE Public Name System.

<!-- It hinges upon some of the Public Name System RFC, namely the use of `Resolvable Map` structures being the target data. -->


## Detailed design


### Sha 3

The `Public Name System` enables us to target a specific string in XOR space, via using the `sha3` hash of the desired `Public Name`. If no MD is stored at this address with the type tag `1500`, then the `Public Name` is available. It is the saving of data at this address with this type tag that enables ownership of a given `Public Name`.


### Container

- The `_publicNames` container is a default container which must created by the SAFE Network upon account creation.
- It is a Mutable Data.
- The Public Name Map is an RDF MD w/specific type tag (`1500`) stored at the sha3 hash of the `Public Name` string `shahash3('Public Name')`.
- A `Public Name` must point to a `Resolvable Map` RDF schema. With the target MD location XOR-URL as the value to the `sha3` of the `key` (which is the desired `Public Name`).
- A user's `_publicNames` container must be encrypted.

- The `_publicNames` container RDF document contains many graphs, which, a given entry will `resolveTo` a XOR-URL for the `Resolveable Map`.





#### Public Name Map Structure

The idea for this is an RDF Data Set stored on the safe network. This will follow a newly defined schema, that represents a list of `keys`, which map to XOR-URLs derived from the desired `publicName`. Each entry can contain more information to aid in resolving data, depending on context / application.

All keys / entries will be encrypted using the MD methods, to ensure that no `Public Name` can be discovered for a user account.

Sample RDF schema can be found: https://github.com/joshuef/sschema/tree/master/src

The RDF document will have a version relating to the version of the `Public Name Map` data structure in use. (starting at `v1`.)

Provides data to be shown at the public name.
 - It must be an RDF data object `<safe/PublicNameMap>`
 - Extra data can be added to the graph for each entry to aid in service discovery for the key.


 ```js
 [
	{
		"@context": {
		    "@vocab": "https://raw.githubusercontent.com/joshuef/sschema/master/src/"
		  },
	     "@type": "PublicNameMap",
		 "@id": "safe://<thexorurl of our `_publicNames` container>",
		 "default" : "safe://<thexorurl of our `_publicNames` container>#somewhere"
	},
	{
		"@context": {
		    "@vocab": "https://raw.githubusercontent.com/joshuef/sschema/master/src/"
		  },
		"@type": "ResolvableItem",
		"@id": "safe://thisxorurl#SomePublicName",
		"target": "<target graph xor url>",
		"targetType": "ResolveableMap"
	},
	{
		"@context": {
			"@vocab": "https://raw.githubusercontent.com/joshuef/sschema/master/src/"
		  },
		"@type": "ResolvableItem",
		"@id": "safe://thisxorurl#AnotherPublicName",
		"target": "<target graph xor url>",
		"targetType": "ResolveableMap"
	},

 ]
 ```


## Drawbacks

Adding RDF requires some limited parsing/ knowledge of triples / graphs.

## Alternatives

One alternative is keeping things as is.

## Unresolved questions
