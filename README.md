[![Run Code Checks](https://github.com/RESOStandards/reso-upi-v2/actions/workflows/codecheck.yml/badge.svg)](https://github.com/RESOStandards/reso-upi-v2/actions/workflows/codecheck.yml) &nbsp; [![CodeQL](https://github.com/RESOStandards/reso-upi-v2/actions/workflows/codeql.yml/badge.svg)](https://github.com/RESOStandards/reso-upi-v2/actions/workflows/codeql.yml)


# Universal Property Identifier (UPI) Version 2

The RESO Universal Property Identifier (UPI) is a standard for a single identifier that includes both parcel numbers and the geographies of the authorities that created them. By implementing a UPI within property information records, data providers and consumers can improve data alignment across systems and avoid parcel number collisions.

## Purpose
The business case for the UPI is matching diverse property-related information pieces to the same parcel. A common example is for-sale property listings. A property data aggregator will often receive the same listing from multiple sources but with slightly different addresses on each listing. Some of its property listings across different geographies will have identical parcel numbers, because they come from different parcel-assigning authorities.

The UPI deduplicates these listings across addresses and authorities. It can do so for many business cases, including tax and public records, broker and agent systems, insurance records, environment data, and transactional history records.

## Description
The UPI shape was designed to support different geographic methods of identifier creation in one worldwide model. It uses existing national and global standards in its components.

## Basic Components
<UPI URN Stem>:<Version>:<Country>:<Subcountry>:<ParcelNumber>

#Basic Example of a UPI
urn:reso:upi:2.0:US:48201:123

* urn:reso:upi: is the stem, including URN prefix and UPI namespace
* 2.0 is the version of the UPI (version 1.0 was previously implemented by some companies)
* US is the Country an ISO Code
* 48201 is the Subcountry (not to be confused with subcounty) for Harrison County, TX, a GEOID from the U.S Census Bureau (the method for identifying Subcountry is country-specific)
* 123 is the parcel number

## Extended Components
<UPI URN Stem>:<Version>:<Country>:<SubCountry>:sub:<SubParcelNumber>

# Extended Example With Special Characters and SubparcelNumbers
urn:reso:upi:2.0:US:48201:12:34:sub:78 - 9.aB

Because the core UPI contains a fixed number of colon-separated components, special characters that exist within components of the UPI are supported. Even colons within a parcel number will be identified as such because of the fixed number of preparcel components for the overall model.

* 12:34 is the parcel number

UPIs can also be used to identify subparcel elements of a property, such as parking spaces, outbuildings or air rights. The UPI, with its fixed number of preparcel components separated by colons, can be extended with a :sub: component label followed by the subparcel item identifier.

* :sub: is the extended component label
* 78 - 9.aB is the subparcel identifier for one of many property elements on the parcel

## Practical Issues

# UPI Usage with Listings and Other Groupings of Parcels
There are common business cases where multiple parcels, or multiple subparcel elements, are grouped together. This could be a for-sale listing with multiple parcels or a for-rent listing with multiple buildings inside a single parcel.

There is demand for standardized ways to represent multiple UPIs for these kinds of business cases, which is an opportunity for future work.

# Selecting the Right Parcel IDs
While unique parcel numbers are straightforward to identify in some geographies, they are not in others. Large data aggregators who were early implementers of the UPI have found that the data sets for IDs that they select for parcels do not always match those chosen by other implementers. Without this alignment, the UPI can’t provide its intended benefit.

RESO is conducting research and will document the correct ID data sets to be utilized by implementers as real-world issues are discovered in new geographies.

# Developing Subcountry Standards for More Countries
The UPI was created with the understanding that other countries will have different standards than the U.S. for how they identify the parcel-assigning authority. RESO will maintain continual outreach to international organizations to create consensus on how the Subcountry component for each of their geographies will be formed.

# Retaining Raw Data from Sources
Data in UPI components should match the raw data from the source as closely as is possible, including capitals, dashes, special characters, spaces, etc.

With version 1.0 of UPI there was an effort to simplify identifiers through stripping some characters, but further research has shown that accuracy is improved when the entire original data set is retained, as in the UPI v2.0 model.

# Limits on Rights to Distribute Parcel Data
There are scenarios where the organization wanting to distribute UPIs may be restricted from distributing the actual parcel numbers based on intellectual property licensing issues or local government rules.

The matching and deduplication aspects of the UPI can still add value through a process called one-way hashing. Hashing obscures the actual data from the receiver of the hash. Instead, it provides a cryptographically hashed unique identifier that can be matched across systems, offering data collision-resistance, which is important for the universality of the UPI. 

Hashing may remove some of the intended simplicity of the UPI such as human-readability, and could add complexity for data consumers matching properties from data providers that deliver differentiated UPI data, standard vs. hashed.

# Hashed UPI Example
Urn:reso:upi:2.0:sha3-256-hash:427c883322af677b76d72d43d9a00c3bedd6a1ede20e43c614f710abf85549a9


### Additional Details

## Uniform Resource Names (URNs)

[**Uniform Resource Names**](https://en.wikipedia.org/wiki/Uniform_Resource_Name) are a type of [**Uniform Resource Identifier (URI)**](https://en.wikipedia.org/wiki/Uniform_Resource_Identifier) that allow globally unique identifiers to be created using a namespace. 

They are a general Internet standard from IANA and IETF, which are in many enterprise-scale applications such as AWS and LinkedIn.

RESO has a reserved URN which includes v1 UPI identifiers. [**See section 3.4.2**](https://www.iana.org/assignments/urn-formal/reso).

## URN Encoding Scheme
In order to avoid collisions with special characters and preserve data passed in the URN, UPIv2 takes the following approach:

> _The delimiters are the components of the UPI!_

What does this mean, in practice? 

The UPI includes Country as part of constituent data. This means it has a component called `:country:` which is also a delimiter. This avoids the need for special delimiters.

## Examples

### Decode a UPI Payload from an Encoded UPI

Assume we have the following UPI:

`urn:reso:upi:2.0:country:US:stateorprovince:CA:county:06037:subcounty::propertytype:Residential:subpropertytype::parcelnumber: [abc] 1-2 ::   3:456 :subparcelnumber:`

Let's look at the individual parts:
* `urn:reso:upi:` is the stem of the UPI, which includes both the URN prefix and upi namespace
* `2.0` is the version of the UPI
* `country` is `US` in this case (ISO Country Code)
* `stateorprovince` is `CA` for California (USPS)
* `county` is a FIPS county code
* `subcounty` is an optional FIPS sub-county code, however it's empty here (`:subcounty::`)
* `propertytype` is an optional Data Dictionary type, in this case Residential
* `subpropertytype` is empty in this case
* `parcelnumber` shows a number of weird characters, including brackets, colons, and spaces, but since the components are the delimiters, the original values are preserved
* `subparcelnumber` is empty in this case (no values after the last `:`)

This UPI can be decoded into a RESO Common Format payload using the `decode` function.

```js
// Assume we're calling from the node REPL 
// and in the root of the project directory
> const { decode } = require(".");

> const upi = 'urn:reso:upi:2.0:country:US:stateorprovince:CA:county:06037:subcounty::propertytype:Residential:subpropertytype::parcelnumber: [abc] 1-2 ::   3:456 :subparcelnumber:';

> const upiData = decode({ upi });

> upiData
{
  '@reso.context': 'urn:reso:metadata:2.0:resource:property',
  Country: 'US',
  StateOrProvince: 'CA',
  County: '06037',
  SubCounty: null,
  PropertyType: 'Residential',
  SubPropertyType: null,
  ParcelNumber: ' [abc] 1-2 ::   3:456 ',
  SubParcelNumber: null
}
```

Note that any item without data in it is null, and that all special characters are preserved in the ParcelNumber. This would be true if special characters were used in any of the values, not just ParcelNumber.


### Encode a UPI from a RESO Common Format Payload
Now let's say we have some standardized RESO data and we want to create a UPI from it. 

We can do so using the `encode` function:

```js
// Assume we're calling from the node REPL 
// and in the root of the project directory
> const { encode } = require(".");

> const upiData = {
  '@reso.context': 'urn:reso:metadata:2.0:resource:property',
  Country: 'US',
  StateOrProvince: 'CA',
  County: '06037',
  SubCounty: null,
  PropertyType: 'Residential',
  SubPropertyType: null,
  ParcelNumber: ' [abc] 1-2 ::   3:456 ',
  SubParcelNumber: null
};

> const upi = encode({upiData});

> upi
'urn:reso:upi:2.0:country:US:stateorprovince:CA:county:06037:subcounty::propertytype:Residential:subpropertytype::parcelnumber: [abc] 1-2 ::   3:456 :subparcelnumber:'
```

### Other Benefits of URNs
* Since `version` is included, different formulas could be used for different versions and accommodate future changes or improvements without making backwards-breaking changes.
* v1 URNs are also supported, they're just not encoded using the component scheme shown here. They could either be prefixed with `1.0` in place of `2.0`, above, or we can assume that if the data in the DD field does not start with `urn:reso:upi`, that it's in the v1 format.
* Since the URN-based UPI essentially encodes key/value pairs, it's extensible and could even support local components.
* The URN-based UPI is self-documenting and human friendly, since each component is explicitly named. We know that the first element is `:country:` and what its value is, and that the second value is `:stateorprovince:`, etc.

# UPI Hashes

In the U.S., Parcel Numbers are a matter of public record. However, in other countries / scenarios, there may be some data that cannot be conveyed due to intellectual property concerns or for other reasons.

The matching and deduplication aspects of the UPI still work even when hashed since if the same components and data were used between two records, their hashes would be the same.

As for choice of hashes, since we're dealing with particularly sensitive data that others wouldn't want shared, one-way hashing (i.e. cryptographic hashing) is a natural choice since it sufficiently obscures the source data. They're also [**NIST**](https://csrc.nist.gov/projects/hash-functions) and global standards used in large-scale production environments like GitHub, Blockchain and Ethereum, and have support out of the box in programming languages and frameworks. 

One-way hashes also offer collision-resistance, which is important for the universality of the UPI.

## Example
Let's assume we have the UPI created in earlier examples: 

```
urn:reso:upi:2.0:country:US:stateorprovince:CA:county:06037:subcounty::propertytype:Residential:subpropertytype::parcelnumber: [abc] 1-2 ::   3:456 :subparcelnumber:
```

To create a UPI hash from this value, use the `hash` function:

```js
// Assume we're calling from the node REPL 
// and in the root of the project directory
> const { hash } = require('.'),
  upi = 'urn:reso:upi:2.0:country:US:stateorprovince:CA:county:06037:subcounty::propertytype:Residential:subpropertytype::parcelnumber: [abc] 1-2 ::   3:456 :subparcelnumber:'

> const upiHash = hash(upi);

> upiHash

'urn:reso:upi:2.0:sha3-256-hash:427c883322af677b76d72d43d9a00c3bedd6a1ede20e43c614f710abf85549a9'
```

Note that the component representing the UPI hash also includes the method that was used for hashing. This seems practical, and offers the ability to support different kinds of hashing, should the need arise. 


# Installation

## As an npm package
To install from GitHub:

```
npm i RESOStandards/reso-upi-v2
```

## Installing and Running Locally
If you would like to run the project locally, install [**Node**](https://nodejs.org/en/download) and [**git**](https://github.com/git-guides/install-git) if you don't have them already.

```
> mkdir [PROJECT_LOCATION]
> cd [PROJECT_LOCATION]
> git clone https://github.com/RESOStandards/reso-upi-v2.git
> cd reso-upi-v2
> npm install
> npm install mocha
```

To check the installation, run:
```
> npm run test
```

For examples of how to use this library, see [**./test**](./test/test.js).

# Contributing
If you would like to suggest changes, [**please open a ticket**](https://github.com/RESOStandards/reso-upi-v2/issues).

If you have changes to contribute, fork the repo, clone locally, make changes, then make a PR against this repo.
