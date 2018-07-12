---
layout: post
title: Measurement Consortium
date: '2018-07-11 13:30'
version: 0.1
---

# Context

## About the Measurement Coalition
The Coalition’s charter is to make digital advertising exposure data from the Open Internet available so that advertisers can properly and transparently measure the effectiveness of campaigns and ensure engagement with consumers is relevant and valuable. The Coalition is open to any media company that adheres to its standards and its commitment to enabling transparent and open measurement.

## General Principles
- **Human Readable** - Prefer semantic names and identifiers that can be interpreted without system-specific knowledge. For example, `Sacramento-Stockton-Modesto` versus `65`.
- **Commonality** - The type information contained within standardized measurement logs needs to be shared across participants. Therefore, maintaining only useful fields and being purposeful about additions is crucial to the success of a unified format.
- **Privacy** - User privacy is core to a successful and sustainable coalition. Care must be taken to preserve user privacy and comply with applicable regulation.
- **Openness** - As much as possible, decisions about the measurement coalition should be made in the open, with feedback from participants, advertisers and the broader ecosystem.

# File Format
## File Naming Convention and Packaging

Measurement log files are provided in a tab-separated, gzipped format. The files are broken up into 500 megabyte chunks, if necessary. If the file is chunked, the parts will be numbered with an integer.

Values of the variables in the filename may contain letters, integers and ASCII special characters (except for underscores, periods and whitespace).

`SourceDSP_Advertiser_Version_CreationTimestamp_Part.tsv.gz`

Name  |Definition   |Example  
--|---|--
SourceDSP|The DSP that produced the log.|`The-Trade-Desk`  
Advertiser|A description of the advertisers to which the measurement data relates.   |`Recreation-Equipment-Inc`  
Version  |The Coalition measurement format version   | 0-1  
CreationTimestamp|The [RFC 3339](https://tools.ietf.org/html/rfc3339#section-5) date-time, in UTC time.   |`2018-07-05T20:43:52Z`  
Part|The "chunk" of a split, 500MB+ file.   |`2`  


## File Contents
Both impressions and click events should be written to a single feed, if possible. Both OpenAdIds and IDLs are required in the feed if either existed at the time of the bid request.

Name  |Description   |Example   |Valid Values   |Required  
--|---|---|---|--
IDL|The platform-encoded LiveRamp IdentityLink|`Xi1377...`||No*
Open_AdID|A supported Open_AdID device identifier, including IDFA or AAID if relevant.|`098d35af-fa02-4677-ba50-eb6a62e565e5`|| No*
Open_AdID|The type of OpenAdId.|`The Trade Desk`|`The Trade Desk`, `AppNexus`, `Digitrust`|No*
Timestamp|	A timestamp given in [RFC 3339](https://tools.ietf.org/html/rfc3339#section-5) date-time, in UTC.|`2018-07-05T20:43:52Z`||Yes
Country|The country the event originated in, in [ISO 3166 ALPHA2](https://en.wikipedia.org/wiki/ISO_3166-2) format.|`US`||Yes*
Region|	The subdivision of the country, also found in ISO 3166.|`US-CA`||Yes*
DMA|	The DMA name where the event originated.|`Sacramento-Stockton-Modesto`||Yes*
User Agent|	The user agent presented by the device in the request header.|`Mozilla/5.0 (Linux; Android 7.1.1; SM-T550 Build/NMF26X) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/63.0.3239.111 Safari/537.36`||Yes
Browser Language  |The browser language defined in the Accept-Language header.   |`    en-US,en;q=0.5`   |   |  Yes
Advertiser|	The advertiser buying media.|`Gap Inc.`|DSP-Defined|Yes
Agency|	The agency (potentially) acting on behalf of the advertiser.| 	`Publicis`|DSP-defined|No
Campaign|	The specific campaign in play.|`FALL_MARKET_PROMO`|DSP-Defined|Yes
Domain|	The publisher domain on which the inventory existed.| `eventbrite.com`| Any publisher hostname, without protocol or path.|Yes
Event Type|	The action / event that took place. |`View`|`View`,`Click`|Yes
Inventory Type|	The ad unit.|`Banner`|Any valid [RTB2.0](https://github.com/openrtb/OpenRTB/blob/master/OpenRTB-API-Specification-Version-2-3-1-FINAL.pdf) impression object type.|Yes
Inventory Specs|	The attributes of the inventory.|`{"w":300,"h":200}`|Attributes specified in [RTB2.0](https://github.com/openrtb/OpenRTB/blob/master/OpenRTB-API-Specification-Version-2-3-1-FINAL.pdf), described in JSON format|Yes
Creative|	The name of the creative asset that was served	|`OrganicNoGMO_FY19`|DSP-Defined|Yes
Exchange|	The exchange that provided the inventory.|`AppNexus`|DSP-Defined|No
Event Type|	The type of data record.| `Impression`|`Impression`,`Click`,`Conversion`|Yes
ID|	An ID to refer to the specific impression or click record.| `5465465468463`|DSP-Defined|Yes
DealID|The PMP identified used in the auction.|`abc001`|DSP-Defined|No
Winning Bid  | The bid that won the auction, passed to the DSP via a macro.  | $0.17  | Must contain currency, zero-padded dollars and cents. |  No
Extension|An optional field to add additional information.|`{"viewability_score":80}`|Any valid JSON object.|No

* required when available.

## File Delivery
Files will be delivered hourly to an Amazon S3 bucket. While the bucket may be owned by a DSP or directly by an advertiser, the advertiser is responsible for all storage costs incurred from usage of the bucket. Expiration policies must be set to not sooner than seven days from data delivery.

# Example

See a [sample]() file.

# Future Enhancements
- Firehose API to pull logs in realtime.
- SSP logs?
