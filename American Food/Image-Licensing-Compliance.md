# Image Licensing Compliance

> **Relevant source files**
> * [img/Cheesecake.jpg](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg)
> * [img/alitas2.jpg](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg)
> * [img/hamburguesa_bbq.jpg](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/hamburguesa_bbq.jpg)

## Purpose and Scope

This document covers the licensing requirements, restrictions, and compliance procedures for the image assets used in the American Food restaurant management system. All 26 menu item images are licensed from Getty Images/iStockphoto and contain embedded metadata that enforces specific usage terms. This page details the licensing structure, asset tracking mechanisms, and requirements that must be addressed before production deployment.

For information about the organizational structure and database integration of these assets, see [Asset Management](/axchisan/AmericanFood/6-asset-management). For the complete catalog of menu item images, see [Menu Item Images](/axchisan/AmericanFood/6.1-menu-item-images).

---

## Licensing Overview

All image assets in the `img/` directory are licensed from Getty Images through the iStockphoto platform. Each image contains embedded licensing metadata that defines usage rights, restrictions, and attribution requirements.

### License Source Structure

```

```

**Sources**: [img/Cheesecake.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L6)

 [img/alitas2.jpg L20-L21](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/alitas2.jpg#L20-L21)

 High-level Diagram 7

---

## Embedded Metadata Structure

Each image file contains three layers of licensing and attribution metadata embedded using industry-standard formats.

### Metadata Layer Architecture

```

```

**Sources**: [img/Cheesecake.jpg L1-L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L1-L11)

### Key Metadata Fields

The following table documents the critical metadata fields embedded in each image:

| Namespace | Field | Purpose | Example Value |
| --- | --- | --- | --- |
| `photoshop` | `Credit` | Identifies license provider | `"Getty Images"` |
| `GettyImagesGIFT` | `AssetID` | Unique asset identifier for tracking | `"1146488934"` |
| `xmpRights` | `WebStatement` | URL to license agreement | `"https://www.istockphoto.com/legal/license-agreement?utm_medium=organic&utm_source=google&utm_campaign=iptcurl"` |
| `plus` | `DataMining` | Data mining restrictions | `"http://ns.useplus.org/ldf/vocab/DMI-PROHIBITED-EXCEPTSEARCHENGINEINDEXING"` |
| `dc` | `creator` | Photographer attribution | `"emrah_oztas"` |
| `dc` | `description` | Image description | `"Stawberry Cheesecake"` |
| `plus` | `LicensorURL` | Licensor-specific URL | `"https://www.istockphoto.com/photo/license-gm1146488934-?..."` |

**Sources**: [img/Cheesecake.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L6)

---

## Asset Tracking System

Getty Images uses the GIFT (Getty Images Framework for Tracking) system to track licensed assets. Each image contains a unique `AssetID` that can be used to verify licensing status.

### AssetID Mapping

```

```

**Sources**: [img/Cheesecake.jpg L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L4)

### Verification Process

To verify compliance for any image:

1. **Extract AssetID**: Parse the XMP metadata from the image file
2. **Lookup License**: Access Getty Images account to verify the AssetID is licensed
3. **Check Usage Terms**: Review the specific license type (Standard, Extended, etc.)
4. **Verify Scope**: Ensure current usage matches licensed scope (web, print, commercial, etc.)

**Sources**: [img/Cheesecake.jpg L1-L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L1-L11)

---

## Usage Rights and Restrictions

### Data Mining Prohibition

All images contain the following data mining restriction:

```yaml
plus:DataMining="http://ns.useplus.org/ldf/vocab/DMI-PROHIBITED-EXCEPTSEARCHENGINEINDEXING"
```

This restriction means:

| Activity | Permitted | Notes |
| --- | --- | --- |
| Manual web scraping | ❌ No | Automated extraction of image data is prohibited |
| AI/ML training data | ❌ No | Images cannot be used to train machine learning models |
| Search engine indexing | ✅ Yes | Search engines (Google, Bing) may index for search results |
| Systematic crawling | ❌ No | Automated bulk downloading is prohibited |
| Content aggregation | ❌ No | Aggregating images across sites is prohibited |

**Sources**: [img/Cheesecake.jpg L4](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L4)

### Attribution Requirements

Each image contains creator attribution information that may require display depending on the license type:

```

```

**Sources**: [img/Cheesecake.jpg L5](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L5-L5)

---

## Database Integration Impact

The image licensing affects how images are referenced in the database.

### Current Implementation

```

```

**Sources**: [database/american_food L133-L142](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L133-L142)

### Compliance Implications

The current architecture has these characteristics:

| Aspect | Status | Compliance Impact |
| --- | --- | --- |
| License data in database | ❌ Not stored | No centralized license tracking |
| AssetID tracking | ❌ Not tracked | Cannot programmatically verify licenses |
| Attribution display | ❌ Not implemented | May violate license if required |
| Usage logging | ❌ Not implemented | Cannot prove compliance if audited |
| License expiration | ❌ Not tracked | Risk of using expired licenses |

**Sources**: [database/american_food L133-L142](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L133-L142)

---

## Compliance Requirements for Deployment

### Pre-Deployment Checklist

Before deploying to production, the following must be verified:

```

```

**Sources**: [img/Cheesecake.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L6)

### Required Actions

1. **License Verification** * Log into Getty Images/iStockphoto account * For each AssetID, verify: * License is active and paid * License type (Standard, Extended, etc.) * Download date is within license terms * Number of uses within license limits
2. **Usage Rights Confirmation** * Confirm license covers: * Commercial use (restaurant business application) * Web application use * Geographic regions where service operates * Number of employees/seats if multi-user license required
3. **Attribution Implementation** * If required by license terms: * Display photographer credits * Format: "Photo by [creator_name] / iStockphoto" * Location: Footer, About page, or Credits page
4. **Compliance Documentation** * Maintain records of: * License purchase receipts * AssetID to license mapping * Download dates * License renewal dates * Attribution compliance screenshots

**Sources**: [img/Cheesecake.jpg L1-L11](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L1-L11)

---

## Risk Assessment

### Current Risk Status

| Risk Category | Severity | Likelihood | Impact |
| --- | --- | --- | --- |
| **Unlicensed Use** | 🔴 Critical | Unknown | Legal liability, fines, takedown notices |
| **License Expiration** | 🟡 High | Likely | Must replace images or renew licenses |
| **Commercial Use Violation** | 🔴 Critical | Unknown | Penalties if using Standard license commercially |
| **Attribution Violation** | 🟡 High | Likely | Breach of license terms |
| **Data Mining Prohibition** | 🟡 High | Low | If images used for AI/ML training |
| **Multi-User Violation** | 🟡 High | Unknown | If license is single-user but multiple staff access |

**Sources**: [img/Cheesecake.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L6)

### Mitigation Strategies

```

```

**Sources**: High-level Diagram 7

---

## Recommended Database Schema Extension

To improve license compliance tracking, consider extending the database schema:

### Proposed image_licenses Table

```

```

This table would enable:

* Programmatic license expiration tracking
* Compliance audit trails
* Attribution requirement enforcement
* AssetID to menu item mapping

**Sources**: [database/american_food L133-L142](https://github.com/axchisan/AmericanFood/blob/67fea4d3/database/american_food (6).sql#L133-L142)

---

## Legal Disclaimer

This documentation provides guidance on image licensing compliance but does not constitute legal advice. The development team must:

1. Review actual license agreements purchased from Getty Images/iStockphoto
2. Consult with legal counsel regarding licensing compliance
3. Maintain independent verification of all licensing requirements
4. Ensure compliance with current terms of service
5. Monitor for license agreement updates or changes

**License terms take precedence over this documentation.**

**Sources**: [img/Cheesecake.jpg L4-L6](https://github.com/axchisan/AmericanFood/blob/67fea4d3/img/Cheesecake.jpg#L4-L6)