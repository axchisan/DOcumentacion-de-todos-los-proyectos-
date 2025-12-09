# Image Metadata and Licensing

> **Relevant source files**
> * [img/Cheesecake.jpg](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg)
> * [img/alitas2.jpg](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg)
> * [img/hamburguesa_bbq.jpg](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/hamburguesa_bbq.jpg)
> * [img/hotdog2.jpg](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/hotdog2.jpg)

This document details the embedded metadata within menu item images, including EXIF/XMP standards compliance, Getty Images asset tracking, licensing requirements, creator attribution, and data mining restrictions. For information about the image file organization and database integration, see [Database Integration](/axchisan/AmericanFood/6.3-database-integration). For the catalog of menu item images by category, see [Menu Item Images](/axchisan/AmericanFood/6.1-menu-item-images).

## Overview

All menu item images in the `img/` directory contain standardized embedded metadata conforming to EXIF and Adobe XMP specifications. The metadata includes Getty Images proprietary tracking (GIFT - Getty Images Framework for Tracking), licensing information, creator attribution, and data mining policy declarations.

**Sources:** [img/Cheesecake.jpg L1-L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L1-L11)

 [img/alitas2.jpg L1-L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L1-L11)

## Metadata Architecture

### Metadata Layer Structure

```

```

**Sources:** [img/Cheesecake.jpg L1-L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L1-L11)

 [img/alitas2.jpg L1-L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L1-L11)

### XMP Packet Structure

The XMP metadata is encoded as an XML packet within the JPEG file structure:

```

```

The `rdf:Description` element contains all namespace-specific metadata attributes and child elements.

**Sources:** [img/Cheesecake.jpg L2-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L2-L10)

 [img/alitas2.jpg L2-L10](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L2-L10)

## Getty Images Tracking System

### Asset Identification

Each image contains a unique Getty Images asset identifier:

| Image File | Asset ID | Creator |
| --- | --- | --- |
| Cheesecake.jpg | 1146488934 | emrah_oztas |
| alitas2.jpg | 1285462667 | semenovp |

The AssetID is embedded in the `GettyImagesGIFT:AssetID` attribute:

```

```

**Sources:** [img/Cheesecake.jpg L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L4)

 [img/alitas2.jpg L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L4-L4)

### GIFT Namespace

```

```

The Getty Images Framework for Tracking (GIFT) enables:

* Asset verification against Getty's database
* License compliance monitoring
* Unauthorized usage detection

**Sources:** [img/Cheesecake.jpg L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L4)

 [img/alitas2.jpg L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L4-L4)

## Licensing Information

### License Agreement Reference

All images reference the iStockphoto license agreement via `xmpRights:WebStatement`:

```yaml
xmpRights:WebStatement="https://www.istockphoto.com/legal/license-agreement?utm_medium=organic&utm_source=google&utm_campaign=iptcurl"
```

**Sources:** [img/Cheesecake.jpg L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L4)

 [img/alitas2.jpg L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L4-L4)

### Licensor Information

The `plus:Licensor` element contains licensing URLs:

```

```

The URL pattern includes:

* `/photo/license-gm{AssetID}-` format
* UTM parameters for tracking

**Sources:** [img/Cheesecake.jpg L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L6-L6)

 [img/alitas2.jpg L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L6-L6)

### Licensing Workflow

```

```

**Sources:** [img/Cheesecake.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L6)

 [img/alitas2.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L4-L6)

## Creator Attribution

### Dublin Core Metadata

Creator information uses Dublin Core namespace (`dc`):

| Field | Namespace | Purpose |
| --- | --- | --- |
| `dc:creator` | [http://purl.org/dc/elements/1.1/](http://purl.org/dc/elements/1.1/) | Photographer/creator name |
| `dc:description` | [http://purl.org/dc/elements/1.1/](http://purl.org/dc/elements/1.1/) | Image description |

#### Example Structure

```

```

**Sources:** [img/Cheesecake.jpg L5](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L5-L5)

 [img/alitas2.jpg L5](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L5-L5)

### Photoshop Credit Field

The `photoshop:Credit` attribute consistently indicates the source:

```yaml
photoshop:Credit="Getty Images"
```

This field appears in both the XMP packet and the Photoshop 3.0 segment.

**Sources:** [img/Cheesecake.jpg L4-L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L11)

 [img/alitas2.jpg L4-L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L4-L11)

### Creator Mapping

```

```

**Sources:** [img/Cheesecake.jpg L5-L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L5-L11)

 [img/alitas2.jpg L5-L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L5-L11)

## Data Mining Restrictions

### DMI Policy Declaration

All images contain a data mining restriction using the PLUS Coalition vocabulary:

```yaml
plus:DataMining="http://ns.useplus.org/ldf/vocab/DMI-PROHIBITED-EXCEPTSEARCHENGINEINDEXING"
```

This URI indicates:

* **DMI-PROHIBITED**: Automated data mining is prohibited
* **EXCEPTSEARCHENGINEINDEXING**: Exception for search engine indexing

**Sources:** [img/Cheesecake.jpg L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L4)

 [img/alitas2.jpg L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L4-L4)

### PLUS Coalition Namespace

```

```

**Sources:** [img/Cheesecake.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L6)

 [img/alitas2.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L4-L6)

### Implications for Web Usage

The data mining restrictions have the following implications:

| Activity | Permitted | Notes |
| --- | --- | --- |
| Display on website | Yes | Within license terms |
| Search engine indexing | Yes | Explicitly allowed exception |
| AI model training | No | Prohibited by DMI policy |
| Automated scraping | No | Prohibited by DMI policy |
| Content aggregation | No | Prohibited by DMI policy |

**Sources:** [img/Cheesecake.jpg L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L4)

 [img/alitas2.jpg L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L4-L4)

## Metadata Extraction and Validation

### Metadata Fields by Namespace

```

```

**Sources:** [img/Cheesecake.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L6)

 [img/alitas2.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L4-L6)

### Required Metadata Fields

All images should contain the following minimum metadata:

1. **Getty Images Tracking** * `GettyImagesGIFT:AssetID` * `photoshop:Credit="Getty Images"`
2. **Licensing** * `xmpRights:WebStatement` (license agreement URL) * `plus:Licensor/plus:LicensorURL` (asset-specific license)
3. **Attribution** * `dc:creator` (photographer/creator) * `dc:description` (image description)
4. **Usage Restrictions** * `plus:DataMining` (data mining policy)

**Sources:** [img/Cheesecake.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L6)

 [img/alitas2.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L4-L6)

## Commercial Deployment Considerations

### Pre-Deployment Checklist

Before deploying to production, verify:

```

```

**Sources:** [img/Cheesecake.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L6)

 [img/alitas2.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L4-L6)

### License Verification Process

1. **Extract AssetID** from `GettyImagesGIFT:AssetID` attribute
2. **Access license URL** from `plus:LicensorURL` element
3. **Verify license terms** at `xmpRights:WebStatement`
4. **Confirm usage scope** (commercial, editorial, web, print)
5. **Check attribution requirements** from `dc:creator`
6. **Validate DMI compliance** with `plus:DataMining` policy

**Sources:** [img/Cheesecake.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L6)

 [img/alitas2.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L4-L6)

### Compliance Risks

| Risk | Impact | Mitigation |
| --- | --- | --- |
| Unlicensed usage | Legal liability | Verify all AssetIDs before deployment |
| Missing attribution | License violation | Ensure `dc:creator` displayed where required |
| DMI policy violation | Legal exposure | Implement scraping prevention measures |
| License expiration | Service disruption | Monitor license renewal dates |
| Unauthorized modification | License breach | Preserve embedded metadata |

**Sources:** [img/Cheesecake.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L6)

 [img/alitas2.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L4-L6)

## Integration with Database

While the images contain embedded metadata, the database `menu` table references images via file paths stored in the `imagen` field. The embedded metadata serves as a secondary verification layer and ensures license compliance tracking is preserved with the image files themselves.

For details on how the database references these images, see [Database Integration](/axchisan/AmericanFood/6.3-database-integration).

**Sources:** [img/Cheesecake.jpg L1-L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L1-L11)

 [img/alitas2.jpg L1-L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L1-L11)