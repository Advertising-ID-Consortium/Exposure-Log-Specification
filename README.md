---
date: '2018-08-17 14:37'
version: 0.3
---

# Context

## About the Advertising ID Consortium Exposure Log Specification
[Suggest adding a brief intro to the Ad ID consortium? Maybe something like "The Advertising ID Consortium (the Consortium) is an inter-company initiative (including DSPs and SSPs) that is focused on making the real-time bidding (RTB) process better and more valuable for everyone in the advertising ecosystem. The main means to achieve this are agreeing on a small number of cookie spaces (AppNexus, The Trade Desk, Digitrust) and a new means to communicate multi-device identity in a bid request. A key component of these initiatives is a standard exposure log specification that can be used by Consortium members [or just anyone?]."] The objective of the specification is to standardize digital advertising exposure data from the Open Internet so that advertisers can properly and transparently measure the effectiveness of campaigns and ensure engagement with consumers is relevant and valuable. The specification can be adopted by any media company that adheres to its standards and its commitment to enabling transparent and open measurement. [Can you also add a bit of info to explain OpenAdIDs, what they are, the advantages, etc.? Otherwise, this assumes they already know about them when they encounter them in the file contents table below. And do they need to use the Sidecar to get the IDLs that they'd report in these exposure logs? If so, we should probably tell them something about IDLs and Sidecar.]

## General Principles
- **Human Readable** - Prefer semantic names and identifiers that can be interpreted without system-specific knowledge. For example, `Sacramento-Stockton-Modesto` versus `65`.
- **Commonality** - The type of information contained within standardized measurement logs needs to be shared across participants. Therefore, maintaining only useful fields and being purposeful about additions is crucial to the success of a unified format.
- **Privacy** - User privacy is core to a successful and sustainable specification. Care must be taken to preserve user privacy and comply with applicable regulations.
- **Openness** - As much as possible, decisions about the exposure specification should be made in the open, with feedback from the Advertising ID Consortium, advertisers, and the broader ecosystem.

# File Format
## File Naming Convention and Packaging

Provide measurement log files in a tab-separated, gzipped format. If a file is over 500MB, break the file into separate parts that are 500MB or smaller. If the file is split, number the parts sequentially with an integer in the file name.

Values of the variables in the file name can contain letters, integers, and ASCII special characters (except for underscores, periods, and whitespace).

`SourcePlatform_Consumer_Version_CreationTimestamp_Part.tsv.gz`
Example: 
`The-Trade-Desk_Recreation-Equipment-Inc_0-1_2018-07-05T20:43:52Z_2`

Variable Name  |Definition   |Example  
--|---|--
SourcePlatform|The DSP or SSP that produced the log.|`The-Trade-Desk`  
Consumer|A description of the consumer to which the measurement data relates.   |`Recreation-Equipment-Inc`  
Version  |The Coalition measurement format version[what the heck is this? Is the "Coalition" the Consortium?]   | 0-1  
CreationTimestamp|The [RFC 3339](https://tools.ietf.org/html/rfc3339#section-5) date-time, in UTC time.   |`2018-07-05T20:43:52Z`  
Part|When the file is over 500MB and is split into smaller file parts, the part number of the particular file. If the file has not been split, do not include a value and end the file name with CreationTimestamp.  |`2`  

## File Contents for Advertiser Logs
All events types should be written to a single feed, if possible. Both OpenAdIDs and IDLs are required in the feed if either existed at the time of the bid request. [It's a bit confusing that we talk about IDLs, but the category we list in the log file is "cross-device ID" - the fact that the two are the same thing isn't immediately apparent.][If they don't have a particular category of info, should they leave that field/column in the file with blank entries, or should they not include that column?]

Name  |Description   |Example   |Valid Values   |Required for a DSP? | Required for an SSP?  
--|---|---|---|---|--
Cross_Device_ID|The cross-device identifier.|`Xi1377...`||No*|No*
Cross_Device_ID_Type|The type of cross-device ID.|`IDL`|`IDL`|No*|No*  
Open_AdID|A supported OpenAdID device identifier, including IDFA or AAID if relevant.|`098d35af-fa02-4677-ba50-eb6a62e565e5`|| No*| No*
Open_AdID_Type|The type [this is a little confusing, since "types" is used above as the type of identifier, so here I'd expect "types" to be cookie or mobile device ID - can you use a different word?] of OpenAdId.|`The Trade Desk`|`The Trade Desk`, `AppNexus`, `Digitrust`|No*| No*
Timestamp|	A timestamp given in [RFC 3339](https://tools.ietf.org/html/rfc3339#section-5) date-time, in UTC.|`2018-07-05T20:43:52Z`||Yes|Yes
Country|The country the event originated in, in [ISO 3166 ALPHA2](https://en.wikipedia.org/wiki/ISO_3166-2) format.|`US`||Yes*[Is this really the same asterisk info as the ones above, where "we need at least an IDL or OpenAdID" applies? Seems like all of these could be replaced with "Required when available"]|Yes*
Region|	The subdivision of the country, also found in ISO 3166.|`US-CA`||Yes*|Yes*
DMA|	The DMA name where the event originated. If outside of the US, the city name.|`Sacramento-Stockton-Modesto`||Yes*|Yes*
Device_Type  |The device type as interpreted by parsing the User Agent with a [ua-parser](https://github.com/ua-parser)-compatible library.| `iPad`  | Any value of `device.family`.  |Yes|Yes
OS_Type  |The operating system as interpreted by parsing the User Agent with a [ua-parser](https://github.com/ua-parser)-compatible library.   |`iOS 5.1`   | Value of `os.family + ' ' + os.major+'.'os.minor+'.'os.patch`. "None" values should be excluded.  |Yes|Yes
Browser_Type  |The browser name as interpreted by parsing the User Agent with a [ua-parser](https://github.com/ua-parser)-compatible library.  |`UC Browser`   | Value of `user_agent.family`.   |Yes|Yes
Browser_Version  |The specific release of the browser, as interpreted by parsing the User Agent. Major, minor and patch versions should be concatenated. |`3.4.3 `   |Value of `user_agent.major+'.'+user_agent.minor+'.'+user_agent.patch` "None" values should be excluded. |Yes|Yes
Browser Language [some of these headings are linked with underscores, and some have spaces]  |The browser language defined in the Accept-Language header.   |`    en-US,en;q=0.5`   | Any string passed in the HTTP header.  |  Yes|Yes
Advertiser|	The advertiser buying the media.|`Gap Inc.`|Platform-Defined|Yes|No*
Agency|	The agency (potentially) acting on behalf of the advertiser.|	`Publicis`|Platform-Defined|No|No
Campaign|	The specific campaign in play.|`FALL_MARKET_PROMO`|Platform-Defined|Yes|No*
Domain|	The publisher domain on which the inventory existed.| `eventbrite.com`| Any publisher hostname, without protocol or path.|Yes|Yes
Event Type|	The action or event that took place. |`View`|`View`,`Click`,`Bid`,`Conversion`|Yes|Yes
Inventory Type|	The ad unit.|`Banner`|Any valid [RTB2.0](https://github.com/openrtb/OpenRTB/blob/master/OpenRTB-API-Specification-Version-2-3-1-FINAL.pdf) impression object type.|Yes|Yes
Inventory Specs|	The attributes of the inventory.|`{"w":300,"h":200}`|Attributes specified in [RTB2.0](https://github.com/openrtb/OpenRTB/blob/master/OpenRTB-API-Specification-Version-2-3-1-FINAL.pdf), described in JSON format|Yes|Yes
Creative|	The name of the creative asset that was served	|`OrganicNoGMO_FY19`|Platform-Defined|Yes|No
Exchange|	The exchange that provided the inventory.|`AppNexus`|Platform-Defined|No|No
ID|	An ID to refer to the specific impression or click record.| `5465465468463`|Platform-Defined|Yes|Yes
DealID|The PMP identified used in the auction.|`abc001`|Platform-Defined|No|No
Winning Bid  | The bid that won the auction, passed to the DSP via a macro.  | $0.17  | Must contain currency, zero-padded dollars and cents. |  No|No
Extension|An optional field to add additional information.|`{"viewability_score":80}`|Any valid JSON object.|No|No

* required when available. A cross-device ID **or** device ID[do you mean OpenAdID?] must be present in each record.

## File Contents for Publisher Logs
TBD.

## File Delivery
Files will [or "should"?] be delivered hourly to an Amazon S3 bucket. While the bucket may be owned by a platform or directly by a consumer [what do we mean by "consumer" here?], the consumer is responsible for all storage costs incurred from usage of the bucket. Expiration policies must be set to not sooner than seven days from data delivery.

# Example

See a [sample]() file.
