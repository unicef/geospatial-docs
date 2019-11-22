---
description: >-
  This document describes the concepts of using ArcGIS Online (AGOL) as UNICEF
  Geospatial Repository.
---

# UNICEF Geospatial Repository on ArcGIS Online

## Overview

UNICEF requires a corporate, central geospatial data repository. The primary focus is on central repository of admin boundaries.This document describes the concepts of using ArcGIS Online \(AGOL\) as UNICEF Geospatial Data Repository \(GDR\). The key objective is to have a single point of truth for admin boundaries so that all UNICEF corporate systems can use the same geospatial reference data. This will help ensure data interoperability between systems, reduce data redundancy, facilitate the geoenabling of existing corporate systems and reduce the overall costs of data maintenance.

Due to the technical limitations of ArcGIS Online, the AGOL version of the GDR needs to be regarded as a temporary, short-term solution. The key benefits of using AGOL are:

* direct integration with the Esri stack \(including desktop apps, web maps, story maps, web dashboards\),
* data is available via API,
* cloud-based infrastructure - no need for costly deployments.

The main constraints of using AGOL are:

* limited validation capabilities,
* no built-in or system-enforced versioning,
* no backups, roll-back etc,
* limited relational table,
* performance,
* potential costs of extra storage \(credit-based pay-as-you-go approach\).

## Assumptions

* Datasets are updated per country, for all admin levels \(e.g. Sudan lev 0-1-2-3\) 
* In case updates \(changes\) affects only selected levels, all other unchanged levels must be provided for consistency 
* One INPUT admin level dataset is stored in one shapefile / ArcGIS Feature Layer 
* UUIDs need to be generated for all new features 
* Hierarchy generated using Pcodes on import but stored in the DB using UUIDs
* Admin levels need to be uploaded from level 0 to lower levels 
* Level Type shall be added to the shapefile / dataset for all features \(ADM0\_TYPE\) 
* UNICEF needs to maintain its own repository of admin boundaries, not always COD-ABs / OCHA will have a suitable datasets. Preference will be for country office / internal datasets 
* No data can be deleted once imported into Master Database

## General workflow: validate and upload new datasets

The following diagram shows a general data workflow.

![](.gitbook/assets/image.png)

All data is stored on AGOL in the COD\_Admin\_Boundaries\_Repository\_MASTER Feature Layer \(hosted\), which contains one \(1\) Layer and two \(2\) Tables:

1. **Layer "cod\_admin\_boundaries\_MASTER"**, which contains all admin boundaries \(archived and active, for all countries and admin levels\). All admin boundaries must by polygons in WGS84 \(EPSG:4326\) coordinate system.
2. **Table "tbl\_upload\_history\_MASTER"**, which contains the history of data uploads.
3. **Table "tbl\_remap\_history\_MASTER"**, which contains the history of remaps.

Additionally, View Layers are created for each country, that contain the active admin boundaries for all available admin levels for a given country, e.g.:

* "AGO\_View" - a Feature Layer \(hosted, view\) containing most up-to-date admin boundaries for Angola,
* "BOL\_View" - a Feature Layer \(hosted, view\) containing most up-to-date admin boundaries for Bolivia,

## Validations

### Validations per admin level

* Duplicated Pcodes within admin level
* Invalid geometries
* Spatial overlaps

### Cross-level validations

* Check if parent pcodes exist
* Check if parent pcodes match spatially

## Remapping concept

Remapping approach is implemented as a versioning mechanism. The general idea is that once the new version of the dataset for a given country and admin level is uploaded, all previous \(old version\) locations are matched with \(remapped to\) **exactly one location** in a new dataset. 

The current matching algorithm works as follows:

1. Iterate through all "old" locations \(for a given country and admin level\)
2. Get the "source" admin boundary
3. Find the admin boundaries in the "target" dataset that intersect with the bounding box \(envelope\) of the "source" admin boundary
4. Intersect Source AB with Nearest Neighbor Admin Boundaries
5. Calculate the percentage of spatial overlap using two measures:
   1. Percentage of the "intersecting" area comparing to the "old" boundary
   2. Percentage of the "intersecting" area comparing to the "new" boundary
   3. 

