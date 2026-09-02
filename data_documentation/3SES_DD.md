# 3SES Dataset Documentation

Data for clay shrink–swell hazard assessment — retrait-gonflement des argiles (RGA)

Dataset root: Datadocumentation\\3SES

Content: 2 split archive parts unpacking to a single tree, shrink_swell\\, of 283 files across 30 directories

Approx. size: 856 MB as delivered (compressed); 2.70 GB unpacked

Study areas: metropolitan France at national scale; département 64 (Pyrénées-Atlantiques) as the pilot area; the Bourgogne-Franche-Comté region and the Yonne département for the companion hazard; Europe for the soil and drought layers

Document date: 2026-08-20 (revised to follow the C2IMPRESS Coastal_flooding documentation conventions)

## 1. Purpose and Scope

This document describes the content, structure and schema of the 3SES data collection: a source-data assembly for a clay shrink–swell hazard service. It gathers the French national and departmental hazard maps, the geological substrate that explains them, the pan-European soil-property layers that could generalise them beyond France, and a drought indicator that supplies the meteorological trigger.

The document is organised by study area and by the role each dataset plays in a shrink–swell assessment — hazard, substrate, soil property, trigger — rather than by folder. Every extent is reported in both the national projected system and WGS 84; every raster grid is specified as an origin, a pixel size and a cell count; every positional or unlabelled numeric file has its column order stated; and every classified field has its class domain stated. All statements are derived from the files themselves: GeoPackage SQLite schemas and decoded OGC binary geometry, shapefile record headers and dBASE field descriptors read byte-wise, Idrisi raster documentation files and their binary bodies, NetCDF headers and value histograms, style definitions, and MD5 comparison for duplication.

This is a collection of acquired third-party datasets, not the output of a processing chain. No derived product, model run or service output is present. Note also that the string "3SES" does not appear as text anywhere inside the delivery — the collection identifies itself only as "shrink_swell".

### 1.1 How the collection is organised on disk

Please note that the convention does not hold throughout. Four folders carry the issue number (georisques_45, brgm_46, copernicus_drought_indicator_48, and the licence sub-folders european_soil_database_47 and georisques_49); five are named descriptively instead (EU WIDE DATASET SSR, FRENCH BRGM DATASET SSR, WATER TABLE FR, Rising of watertable, SWI INDICATOR - EU WIDE); and one file name uses the same two-digit position for something else entirely — "Inondations par remontée de nappe_89.gpkg", where 89 is the INSEE code of the Yonne département whose extent the file covers. The suffix cannot be parsed mechanically.

shrink_swell\\
  |- readme.txt                              organising principle, contact
  |- FRENCH BRGM DATASET SSR\\               national clay shrink-swell exposure  (Section 3)
  |    |- Exposure map to clay shrinkage and swelling.gpkg   847 MB, 122 222 polygons
  |    |- AleaRG_2025_Fxx_L93.{dbf,shx,prj,cpg}              2025 vintage - NO .shp
  |    |- WFS SSC.txt                                        live web-service pointer
  |- georisques_45\\                         departement 64 extract               (Section 4)
  |- brgm_46\\                               harmonised 1:50 000 geology, dept 64  (Section 5)
  |- EU WIDE DATASET SSR\\                   European Soil Database derived layers (Section 6)
  |- WATER TABLE FR\\, Rising of watertable\\   water-table rise sensitivity       (Section 7)
  |- copernicus_drought_indicator_48\\       Combined Drought Indicator            (Section 8)
  |- data_standards\\, licences\\, powerpoint\\   supporting material              (Section 9)
  |- SWI INDICATOR - EU WIDE\\               EMPTY                                (Section 10)

*Table 1.1 — Collection. Inventory by top-level folder.*

| **Folder** | **Files** | **Size** | **Principal formats** | **Section** |
| --- | --- | --- | --- | --- |
| EU WIDE DATASET SSR | 43 | 1 665.4 MB | Idrisi raster (.rst/.RDC), PDF, zip | 6 |
| FRENCH BRGM DATASET SSR | 6 | 864.7 MB | GeoPackage, partial shapefile, TXT | 3 |
| brgm_46 | 180 | 97.8 MB | Shapefile, QML, LYR, SVG, TTF, PDF, zip | 5 |
| georisques_45 | 10 | 42.6 MB | Shapefile, CSV, XLSX, PDF, zip | 4 |
| Rising of watertable | 6 | 38.9 MB | Shapefile, QMD | 7 |
| WATER TABLE FR | 20 | 25.6 MB | Shapefile, GeoPackage, QML, LYR, PDF | 7 |
| data_standards | 4 | 22.5 MB | PDF, TXT | 9 |
| copernicus_drought_indicator_48 | 2 | 2.8 MB | NetCDF-4, PNG | 8 |
| powerpoint | 1 | 3.5 MB | PPTX | 9 |
| licences | 8 | 1.4 MB | PDF, DOCX, TXT | 9 |
| SWI INDICATOR - EU WIDE | 0 | 0 B | — | 10 |
| (root) | 3 | 22.7 kB | readme.txt, .DS_Store, Office lock file | — |

The delivery is one logical tree split across two archive files by the export tool: part 001 holds 272 files (1.94 GB) and part 002 holds 12 (0.76 GB) — eight of the large .rst rasters, STU_EU_Layers.zip and three symbol SVGs. The split is by size, not by meaning; both parts must be unpacked into the same destination.

## 2. Study Areas and Reference Frames

Three coordinate reference systems are in use, plus one declared only in a pointer file. The French layers are internally consistent; the European layers are not in the same frame as anything French, and no transformation is supplied.

*Table 2.1 — Collection. Coordinate reference frames.*

| **Frame** | **EPSG** | **Used by** | **Evidence** |
| --- | --- | --- | --- |
| RGF93 / Lambert-93 | 2154 | Everything French: the national GeoPackage, the 2025 shapefile, the dept-64 extract and its CSV, all six geology layers, all water-table layers and the impermeable mask | The .prj files declare Lambert Conformal Conic on GRS 1980 with false easting 700 000 m, false northing 6 600 000 m, central meridian 3°, standard parallels 44° and 49°, latitude of origin 46.5°. Coordinates are metres. |
| ETRS89-LAEA | 3035 | The 20 European Soil Database rasters | Every .RDC declares ref. system "_etrs_laea", ref. units m, resolution 1000, over the standard LAEA Europe window. |
| WGS 84 geographic | 4326 | The Copernicus Combined Drought Indicator | Global attribute 11_DATA_SRS = "EPSG:4326"; a spatial_ref variable carries the WKT and a GeoTransform. |
| WGS 84 (service only) | 4326 | The Géorisques WFS endpoint described in WFS SSC.txt | The saved connection specifies srsname='EPSG:4326' for typename ms:ALEARG_REALISE — a different frame from the one the files are delivered in. |

*Table 2.2 — Collection. Extent of every layer, in its native frame and in WGS 84.*

| **Layer** | **Section** | **Native X / longitude** | **Native Y / latitude** | **WGS 84 longitude** | **WGS 84 latitude** |
| --- | --- | --- | --- | --- | --- |
| National RGA GeoPackage (2019) | 3.1 | 99 704.8 – 1 242 436.0 m | 6 049 646.0 – 7 109 081.3 m | −5.512890 – 10.697841 | 41.303899 – 50.839928 |
| AleaRG_2025 (.shx bbox) | 3.2 | 99 691.5 – 1 242 423.4 m | 6 049 643.1 – 7 109 081.3 m | −5.513077 – 10.697663 | 41.303862 – 50.839939 |
| Dept-64 RGA extract | 4.1 | 311 933.1 – 459 086.0 m | 6 192 984.0 – 6 283 556.0 m | −1.802989 – 0.057798 | 42.731663 – 43.610083 |
| Dept-64 geology (S_FGEOL) | 5.1 | 311 998.3 – 459 087.4 m | 6 192 987.4 – 6 283 558.2 m | −1.802186 – 0.057813 | 42.731728 – 43.610103 |
| Dept-64 water table | 7.2 | 311 755.0 – 459 086.0 m | 6 192 987.5 – 6 283 556.0 m | −1.805189 – 0.057796 | 42.731598 – 43.610083 |
| Dept-64 impermeable mask | 7.3 | 320 428.6 – 455 592.3 m | 6 193 377.7 – 6 282 886.0 m | −1.697601 – 0.014989 | 42.739725 – 43.602862 |
| Bourgogne-F-C water table | 7.2 | 688 355.0 – 1 011 646.0 m | 6 562 593.0 – 6 811 155.0 m | 2.842669 – 7.206559 | 46.091439 – 48.400584 |
| Yonne (89) water table | 7.2 | 688 654.0 – 800 332.0 m | 6 690 507.0 – 6 811 155.0 m | 2.846708 – 4.355418 | 47.307291 – 48.400589 |
| ESDB 1 km grid | 6.1 | 1 500 000 – 7 400 000 m | 900 000 – 5 500 000 m | −49.906705 – 72.906137 | 25.544711 – 60.748195 |
| Combined Drought Indicator | 8.1 | −24.979167 – 50.979167° | 22.020833 – 71.979167° | (native) | (native) |

The four dept-64 rows agree to within about 250 m in X and 100 m in Y, which confirms that the hazard extract, the geological map, the water-table layer and the mask are cut to the same département footprint and can be overlaid directly. The conversions in this table were computed for this document with pyproj and are not part of the delivery.

Caveat. Overlaying the French hazard and geology layers on the European soil rasters requires a reprojection from EPSG:2154 to EPSG:3035, and overlaying either on the drought indicator a further reprojection to EPSG:4326. None of the three transformations is supplied, and no layer has been reprojected to a common frame. Note also two cosmetic .prj variants that will defeat naive string comparison: the standard parallels appear as 49 then 44 in most files but 44 then 49 in the geology layers and the dept-64 water-table layer, and that layer names its datum "D_Reseau_Geodesique_Francais_1993_v1" rather than "D_RGF_1993". All resolve to EPSG:2154.

### 2.1 Time

Most of the collection is time-invariant: a hazard zoning, a geological map and a soil-property grid carry no time dimension. Only two elements are dated in the data itself.

*Table 2.3 — Collection. Temporal reference.*

| **Element** | **Time representation** | **Resolution** | **Extent** |
| --- | --- | --- | --- |
| Combined Drought Indicator | CF time, "days since 2025-01-01", proleptic Gregorian | dekadal (1st, 11th, 21st of each month) | 2025-01-01 to 2025-10-11, 29 steps |
| Clay shrink–swell exposure | Not in the data; vintage inferred from content | single snapshot | Two vintages: 2019 (GeoPackage and dept-64 extract) and 2025 (geometry-less shapefile) |
| Harmonised geology | Not in the data | single snapshot | File timestamps 2016–2019; toolkit archive named Outils_avant_20191127 |
| ESDB derived layers | Not in the data | single snapshot | Header timestamps 2013-10-20/21 |
| Water-table sensitivity | Not in the data | single snapshot | Methodology report BRGM/RP-65452-FR dated January 2018; layer timestamps 2023 |

## 3. National Clay Shrink–Swell Exposure

The core dataset: the BRGM / Géorisques map of exposure to the shrinkage and swelling of clay soils, covering metropolitan France. It is delivered twice, in two vintages and two formats, and only one of the two is usable.

### 3.1 Exposure map to clay shrinkage and swelling.gpkg (2019 vintage)

A GeoPackage of 847 MB. Application ID 0x47504B47 ("GPKG"), user_version 10200 (GeoPackage 1.2), written by QGIS 3.28.4, last_change 2026-05-27. One feature table, risk_of_shrinkage_and_swelling_of_clays, with 122 222 rows confirmed by both gpkg_ogr_contents and a direct count; geometry column geom declared MULTIPOLYGON, srs_id 2154, z = 0, m = 0; a gpkg_rtree_index spatial index; and a gpkg_metadata record that is a QGIS metadata document whose identifier, title, abstract and contact elements are all empty, its only substantive content being the CRS WKT.

*Table 3.1 — France. National. RGA GeoPackage. Attribute schema, in column order.*

| **Column** | **SQL type** | **Meaning** | **Domain (verified over all 122 222 rows)** |
| --- | --- | --- | --- |
| fid | INTEGER PRIMARY KEY AUTOINCREMENT | Feature identifier | 1 – 122 222, contiguous, no nulls |
| geom | MULTIPOLYGON | Hazard zone polygon | No nulls. 122 217 single-part, 5 genuinely multi-part. Blob sizes 126 B – 7.77 MB, 824 MB total. |
| DPT | TEXT(2) | INSEE département code | 95 distinct values, no nulls (Section 3.4) |
| NIVEAU | REAL | Hazard level as a number | 1.0, 2.0 or 3.0 only |
| ALEA | TEXT(6) | Hazard level as a French label | "Faible", "Moyen", "Fort" only |

NIVEAU and ALEA are perfectly redundant: the cross-tabulation contains exactly three non-empty cells, so one reconstructs the other without ambiguity. Class ordering is ascending in severity — 1 Faible (low), 2 Moyen (medium), 3 Fort (high) — so NIVEAU is a valid ordinal.

*Table 3.2 — France. National. RGA GeoPackage. Hazard-class distribution, with geometry computed from the polygons.*

| **NIVEAU** | **ALEA** | **English** | **Features** | **Area (km²)** | **Rings** | **Vertices** |
| --- | --- | --- | --- | --- | --- | --- |
| 1 | Faible | Low | 60 149 | 129 546.3 | 94 978 | 19 811 139 |
| 2 | Moyen | Medium | 50 377 | 192 523.6 | 103 143 | 24 116 496 |
| 3 | Fort | High | 11 696 | 73 519.5 | 31 510 | 7 047 200 |
| — | TOTAL | — | 122 222 | 395 589.3 | 229 631 | 50 974 835 |

The mapped area totals 395 589 km², about 72 % of the 551 500 km² of metropolitan France. Please note that the layer carries no polygon for the remaining 28 %: that ground is absent from the layer and means "outside the clay formations considered", not "no hazard". Any percentage-of-territory statistic must state its denominator.

### 3.2 AleaRG_2025_Fxx_L93 — a shapefile with no geometry

Four components of a five-component shapefile are present. The .shp — the file that holds the geometry — is absent.

*Table 3.3 — France. National. RGA 2025. Delivered and missing components.*

| **Component** | **Size** | **Status** | **What it tells us** |
| --- | --- | --- | --- |
| .shp | — | MISSING | The geometry. Its size follows from the index: the last .shx entry places a record at offset 210 395 166 words with length 736 words, implying a .shp of 420 791 812 bytes (401.3 MB). |
| .shx | 971 292 B | Present | Valid header: file length self-consistent, shape type 5 (Polygon), 121 399 records, bounding box 99 691.5 – 1 242 423.4 E, 6 049 643.1 – 7 109 081.3 N. |
| .dbf | 17 845 815 B | Present | 121 399 records, 4 fields (Table 3.4). |
| .prj | 452 B | Present | RGF_1993_Lambert_93, EPSG:2154 equivalent. |
| .cpg | 5 B | Present | "UTF-8". |

*Table 3.4 — France. National. RGA 2025. Attribute schema, in column order (121 399 records, UTF-8 as declared).*

| **Field** | **Type** | **Meaning** | **Domain** |
| --- | --- | --- | --- |
| gid | N(18,0) | Feature identifier | 1 – 121 399, contiguous |
| insee_dep | C(80) | INSEE département code | 95 distinct values, no blanks. An 80-character field for a 2- or 3-character code. |
| niveau | N(24,15) | Hazard level | 1, 2 or 3 only. Stored with 15 declared decimal places for an integer code. |
| surf_m2 | N(24,15) | Polygon area | 0 – 5 588 294 715.372 m²; 372 records hold exactly 0. Total 395 455 291 147.9 m² = 395 455.3 km². |

Consequence. The layer cannot be opened by any GIS. The .shx and .dbf agree on 121 399 records and the .shx preserves the bounding box and per-record byte offsets, but no coordinates exist anywhere in the delivery. This is the most serious defect in the collection: it removes the current-vintage national hazard map. The surf_m2 field is what makes the loss partially recoverable as a statistic — per-class and per-département areas can still be totalled without the geometry, and that is the only analysis this layer still supports.

### 3.3 The two vintages compared

The two deliveries are not copies of each other. Total mapped area agrees to within 0.03 % — 395 589.3 km² computed from the GeoPackage geometry against 395 455.3 km² summed from the 2025 attribute field — but the division into hazard classes has changed substantially.

*Table 3.5 — France. National. RGA. Hazard-class area by vintage.*

| **Class** | **2019 features** | **2019 km²** | **2025 features** | **2025 km²** | **Change in area** |
| --- | --- | --- | --- | --- | --- |
| 1 / Faible | 60 149 | 129 546.3 | 35 091 | 93 863.1 | −27.5 % |
| 2 / Moyen | 50 377 | 192 523.6 | 64 039 | 195 913.5 | +1.8 % |
| 3 / Fort | 11 696 | 73 519.5 | 22 269 | 105 678.7 | +43.7 % |
| TOTAL | 122 222 | 395 589.3 | 121 399 | 395 455.3 | −0.03 % |

Roughly 36 000 km² is reclassified upward between the two releases, most of it from Low into Medium and High, while the outer envelope of the mapping stays almost unchanged. This is a methodological revision of the class thresholds, not a change in survey coverage. Please note the consequence: the two vintages must never be mixed in one analysis or differenced as a time series. A polygon that moves from Faible to Fort between them records a change in the classification rule, not a change in the ground. Neither file carries a vintage attribute; the only in-file evidence is the class distribution itself.

### 3.4 Département coverage

The GeoPackage DPT field holds 95 distinct values: the codes 01 to 95 except 20 and 75, plus 2A and 2B. This is exactly metropolitan France as the dataset defines it — Corsica split into Corse-du-Sud (2A) and Haute-Corse (2B) rather than the historic single code 20, and Paris (75) excluded, as the source documentation states. The 2025 insee_dep field holds the same 95 values.

Please note that the two vintages disagree about which départements dominate. In the GeoPackage the largest mapped areas are Saône-et-Loire (71) at 6 989 km², Gironde (33) at 6 986 km² and Aisne (02) at 6 613 km²; in the 2025 table the highest feature counts are Haute-Vienne (87) with 3 886, Aisne (02) with 3 130 and Alpes-de-Haute-Provence (04) with 2 943. Feature count and mapped area are not interchangeable — the polygons differ by more than an order of magnitude in size.

### 3.5 WFS SSC.txt — the live service pointer

A saved QGIS layer-connection description rather than data. It records that the same layer is available as a Web Feature Service:

WFS url='https://georisques.gouv.fr/services'
Name : "Exposition au retrait-gonflement des argiles"
typename='ms:ALEARG_REALISE'   srsname='EPSG:4326'   version='auto'
Emprise : -12.3724135070462005,41.1499999882825023 : 10.3799999644426002,51.7747572936821996
Decompte d'entites : 80 000

Three details matter. The service delivers EPSG:4326, not the Lambert-93 of the files. Its declared feature count is 80 000, materially fewer than the 122 222 of the GeoPackage or the 121 399 of the 2025 table, so the service is serving a third, differently generalised version. And its declared western bound of −12.37° lies far out in the Atlantic, beyond any French land — an artefact of the service metadata rather than a real extent.

## 4. Pilot Area — Département 64 Clay Shrink–Swell Extract

The same national hazard map, clipped to the Pyrénées-Atlantiques (INSEE 64), delivered as a complete shapefile with a WKT CSV twin. Downloaded from georisques.gouv.fr; the shapefile components are timestamped 2019-06-07.

![3SES-updated figure 1](3SES-updated_media/3SES-updated_img1.png)

*Pyrénées-Atlantiques (64). Département. Géorisques RGA. The three-class exposure to clay shrinkage and swelling, drawn from AleaRG64_L93.shp in RGF93 / Lambert-93 with a WGS 84 graticule.*

### 4.1 AleaRG64_L93 shapefile

*Table 4.1 — Pyrénées-Atlantiques (64). Département. Géorisques RGA. Components.*

| **Component** | **Content** |
| --- | --- |
| .shp | Shape type 5 (Polygon), version 1000, 10 742 628 bytes. 3 059 records, 4 417 rings, 660 362 vertices. Bounding box 311 933.134 – 459 086.000 E, 6 192 984.000 – 6 283 556.000 N. |
| .dbf | dBASE III, 3 059 records, 3 fields in column order: dpt C(2), niveau N(10,0), alea C(80). |
| .prj | RGF93_Lambert_93 — EPSG:2154 equivalent. |
| .cpg | "UTF-8". |
| .shx | 24 572 bytes — present and consistent with 3 059 records. |
| .qix | 154 944 bytes — a QGIS / MapServer quadtree spatial index. Optional; it can be rebuilt. |

*Table 4.2 — Pyrénées-Atlantiques (64). Département. Géorisques RGA. Class distribution computed from the geometry.*

| **niveau** | **alea** | **Features** | **Area (km²)** | **Rings** | **Vertices** |
| --- | --- | --- | --- | --- | --- |
| 1 | Faible | 2 284 | 1 695.69 | 2 425 | 263 205 |
| 2 | Moyen | 703 | 3 832.97 | 1 741 | 341 406 |
| 3 | Fort | 72 | 710.42 | 251 | 55 751 |
| — | TOTAL | 3 059 | 6 239.08 | 4 417 | 660 362 |

Provenance verified. Querying the national GeoPackage for DPT = "64" returns 3 059 features split 2 284 Faible / 703 Moyen / 72 Fort, and the per-département area computed from the GeoPackage geometry for code 64 is 6 239.1 km². Every figure matches this extract exactly. It is therefore a clean département clip of the same 2019 vintage held in the GeoPackage — not an independently produced layer, and not the 2025 vintage.

Please note that the field names differ between the three deliveries of the same dataset: DPT / NIVEAU / ALEA in the GeoPackage, dpt / niveau / alea in this extract, insee_dep / niveau / surf_m2 in the 2025 table. Case-sensitive column references will not port between them.

### 4.2 AleaRG64_L93.csv — the WKT export

A 24.6 MB comma-separated export of the same layer, timestamped 2025-10-07 but holding the 2019 content. The order of the columns is: dpt, niveau, alea, geometry. 3 059 data rows plus a header.

dpt,niveau,alea,geometry
64,3,Fort,"POLYGON ((427342.04796496715 6239335.706023026, 427341.29322148813 6239337.011423877, ... ))"

Verified against the shapefile: the attribute triples match record for record in the same order, the ring counts match the shapefile part counts for all 3 059 records including the 114 multi-ring ones, and the coordinate-pair count matches at 660 362. No generalisation or reprojection was applied — the coordinates are Lambert-93 metres at full precision.

The geometry column uses POLYGON rather than MULTIPOLYGON throughout, which would normally risk demoting extra exterior parts to holes. It does not here: checking ring orientation shows every one of the 114 multi-ring records has exactly one clockwise exterior ring, and the 1 536 counter-clockwise rings across the layer are all genuine holes. The WKT is a faithful representation, and the CSV is a valid substitute for the shapefile for any tool that reads WKT — which matters, because it is the only geometry in the collection with no format dependency at all.

Supporting files: georisques_shrink_swell_clay_soils.zip is a redundant copy of the same six components plus six __MACOSX resource-fork stubs; Catalogue_donnees_Georisques.xlsx is the Géorisques data catalogue (one sheet, 48 rows, 28 external hyperlinks, roughly 30 hazard themes, with the relevant entry "Retrait gonflement des argiles / Carte d'exposition au risque de Retrait Gonflement des Argiles / Argiles / ALEARG"); Licence_Ouverte.pdf is the Etalab Open Licence 2.0, byte-identical to two other copies in the collection.

## 5. Geological Substrate — Harmonised 1:50 000 Geology, Département 64

The BRGM harmonised geological map of the Pyrénées-Atlantiques, downloaded from InfoTerre. This is the lithological substrate behind the hazard zoning: clay shrink–swell is a property of the formation, and this layer identifies the formations. Six vector layers plus the complete BRGM cartographic symbology toolkit.

Each layer ships .shp, .shx, .dbf, .prj, a QGIS .qml and an ArcGIS .lyr. All six declare RGF93_Lambert_93 (EPSG:2154). None ships a .cpg, so the encoding is not declared in the usual place — but the dBASE language-driver byte is 0x57 (ANSI / Windows-1252), which most modern readers honour. The French accented text decodes cleanly as CP1252 or ISO-8859-1 and fails as UTF-8.

*Table 5.1 — Pyrénées-Atlantiques (64). Département. BRGM geology. The six layers.*

| **Layer** | **Geometry** | **Records** | **Parts** | **Vertices** | **Extent (Lambert-93 m)** |
| --- | --- | --- | --- | --- | --- |
| S_FGEOL — geological formations | Polygon | 13 037 | 15 243 | 1 314 200 | 311 998 – 459 087 E, 6 192 987 – 6 283 558 N |
| L_FGEOL — formation boundaries | PolyLine | 38 858 | 38 858 | 692 671 | same as S_FGEOL |
| L_STRUCT — faults and thrusts | PolyLine | 5 719 | 5 719 | 46 628 | 313 603 – 448 976 E, 6 193 976 – 6 279 613 N |
| L_DIVERS — miscellaneous lines | PolyLine | 653 | 653 | 4 100 | 320 605 – 456 345 E, 6 199 370 – 6 281 968 N |
| P_STRUCT — structural measurements | Point | 5 927 | — | — | 314 311 – 443 984 E, 6 195 021 – 6 278 254 N |
| P_DIVERS — miscellaneous points | Point | 412 | — | — | 338 118 – 437 306 E, 6 195 564 – 6 280 656 N |

The polygon layer covers 7 694.50 km², the whole département (7 645 km² of administrative area, the excess being map-sheet overlap at the boundary). Its extent matches the dept-64 hazard extract to within 70 m in X and 3 m in Y — the two are cut to the same footprint.

### 5.1 S_FGEOL — the formation polygons

The layer that carries the lithology. 16 fields, of which four describe the geology and eleven describe how to draw it.

*Table 5.2 — Pyrénées-Atlantiques (64). Département. BRGM geology. S_FGEOL attribute schema, in column order.*

| **Field** | **Type** | **Meaning** | **Domain** |
| --- | --- | --- | --- |
| MI_PRINX | N(24,15) | MapInfo primary index carried through the conversion | 3 706 056 – 3 719 092, unique per record |
| CARTE | N(4,0) | Harmonised map identifier | Constant 1499 — the département-64 harmonisation |
| CODE | N(4,0) | Formation code | 1 – 999 |
| CODE_LEG | N(4,0) | Legend-entry code | 1 – 999 |
| NOTATION | C(50) | Stratigraphic notation as printed on the map | 350 distinct values, no blanks (e.g. "E", "e-m", "Fz", "n1-c1", "t6-7") |
| DESCR | C(254) | Full lithological description in French | 350 distinct values, in one-to-one correspondence with NOTATION |
| C_FOND, M_FOND, J_FOND, N_FOND | N(10,0) | Fill colour as CMYK percentages — Cyan, Magenta, Jaune (yellow), Noir (black) | 0 – 100 |
| NOM_SYMB | C(28) | Name of the overprint pattern, resolving to an SVG in OUTILS\\Polygon_Patterns | — |
| C_SYMB, M_SYMB, J_SYMB, N_SYMB | N(10,0) | Overprint symbol colour as CMYK percentages | 0 – 100 |
| ROT_SYMB | N(3,0) | Symbol rotation | degrees |

NOTATION and DESCR are two views of the same 350-entry legend and can be used interchangeably as a formation key. The DESCR text is the lithological content a shrink–swell assessment needs, because it names the constituent materials directly.

*Table 5.3 — Pyrénées-Atlantiques (64). Département. BRGM geology. The ten most frequent formations.*

| **NOTATION** | **DESCR (abridged)** | **Polygons** | **Clay-bearing?** |
| --- | --- | --- | --- |
| E | Éboulis actuels ou récents : cônes d'éboulis, limons et loess masquant parfois les terrasses | 1 131 | silt / loess |
| e-m | Éocène-Miocène : molasse argilo-sableuses, jaunes à vert-bleu, carbonatées, à galets | 745 | yes — argilo-sableuse |
| Fz | Alluvions fluviatiles actuelles, subactuelles et Würm : sables, argiles, tourbes, galets, graviers, limons | 669 | yes — argiles |
| C | Colluvions : limons soluflués, loess colluvionnés, colluvions de fond de vallons, sables argileux | 442 | yes — sables argileux |
| ÿ(1) | Ophites triasiques à tarditriasiques | 403 | no |
| t6-7 | Keuper : marnes bariolées, argiles gypsifères, calcaires et dolomies, cargneules | 372 | yes — marnes, argiles gypsifères |
| Gw-x | Mindel et pléistocène moyen : alluvions anciennes, terrasse à galets, matrice argilo-sableuse | 344 | yes — matrice argilo-sableuse |
| m6-p | Ponto-Pliocène : argiles à graviers, nappes alluviales, sables fauves | 324 | yes — argiles |
| hydro | Étangs, lacs, rivières | 321 | n/a — water |
| m5 | Tortonien : formation des Glaises bigarrées, argiles gris-bleu à passées blanchâtres ou rougeâtres | 281 | yes — argiles |

The "Clay-bearing?" column is an editorial reading of the French DESCR text, offered as orientation. It is not a field in the data. Please note that the layer carries no shrink–swell susceptibility attribute of any kind, and no lookup table linking NOTATION or CODE to a susceptibility class is included anywhere in the collection. Deriving susceptibility from this layer requires an external correspondence table that is not part of the delivery.

### 5.2 The line and point layers

The three line layers and two point layers share a common pattern: MI_PRINX, CARTE and CODE identify the feature, DESCR gives a French type label from a small controlled vocabulary, ARCGIS_COD gives the corresponding symbol key, and the remaining fields are rendering instructions (NOM_SYMB, WT_SYMB line weight, the four CMYK components, and for points ROT_SYMB rotation and TAIL_SYMB size).

*Table 5.4 — Pyrénées-Atlantiques (64). Département. BRGM geology. Controlled vocabularies.*

| **Layer** | **Values** | **Distribution** |
| --- | --- | --- |
| L_FGEOL | 8 | Contour géologique observé, visible 28 766; Élément linéaire structural 6 435; Limite réseau hydrographique 1 473; Limite de la carte ou du projet 1 266; Élément linéaire divers 429; Contour géologique supposé, probable, masqué 355; Limite interprétée sur la carte 114; Limite de faciès / biozone 20 |
| L_STRUCT | 4 | Faille observée, visible, de cinématique non précisée 4 350; Faille supposée, masquée, hypothétique 781; Chevauchement, base de nappe ou faille inverse visible 510; Chevauchement … supposée 78 |
| P_STRUCT | 15 | Stratification S0 avec pendage 4 077; Axe de pli synschisteux avec plongement 705; Axe de pli avec plongement 458; Stratification S0 à pendage vertical 289; Stratification S0, pendage de série inversée 140; Schistosité principale ou foliation avec pendage 128; and nine smaller classes |
| P_DIVERS | various | 412 miscellaneous cartographic point symbols |

P_STRUCT additionally carries AZIMUT N(10,0) and PENDAGE N(10,0) — the strike azimuth and dip angle of each measurement. AZIMUT ranges 0 – 359° and is zero in 257 records. Please note that PENDAGE ranges 0 – 999°, which is not a dip: 999 is an undeclared sentinel used in 903 of the 5 927 records, and 0 appears 1 834 times, partly as genuine horizontal measurements (the 45 records described as "pendage horizontal" and 28 as "foliation horizontale") and partly as absent data. Filter PENDAGE to 1 – 90 before treating it as an angle, and use DESCR to recover the genuine horizontal measurements.

### 5.3 OUTILS — the symbology toolkit

133 symbol resources plus an ArcGIS style library. These are not data but they are not optional either: the .qml and .lyr styles reference them by name through NOM_SYMB and ARCGIS_COD, and without them a rendered map falls back to plain fills and lines.

*Table 5.5 — Pyrénées-Atlantiques (64). Département. BRGM geology. Contents of OUTILS.*

| **Item** | **Count / size** | **Purpose** |
| --- | --- | --- |
| Polygon_Patterns\\ | 95 SVG | Overprint patterns for lithology fills, referenced by NOM_SYMB in S_FGEOL |
| Line_Symbols\\ | 35 SVG | Fault, thrust and boundary decorations, referenced by NOM_SYMB in the L_ layers |
| Point_Symbols\\ | 3 TrueType fonts | BRGM_NOT.TTF, BRGM_PStruct.ttf and BRGM_PDivers.ttf — glyph sets for map notation, structural measurements and miscellaneous point symbols |
| symbologie_arcgis\\ | REF_GEOL_BRGM_2008.style, 4.6 MB | The 2008 BRGM reference geological style library for ArcGIS |
| Outils_avant_20191127.zip | — | The preceding release of the same toolkit, retained alongside the current one |
| Descriptif des cartes géologiques à 1_50 000 vecteur.pdf | 465 834 B | The BRGM specification of the vector 1:50 000 map product — the authoritative description of this schema. A byte-identical copy sits in GEO050K_HARM_064\\. |
| Licence documents | 4 files | Etalab Open Licence 2.0 and the InfoTerre conditions of use, each with an English translation; all four duplicated under licences\\brgm_46\\. |

## 6. Soil Properties — European Soil Database Derived Layers

Twenty raster layers of soil properties for Europe from the JRC European Soil Data Centre derived-data product, combining the European Soil Database with the Harmonized World Soil Database and the Soil-Terrain Database. This is the pan-European generalisation path: clay content and texture class govern shrink–swell potential, and these layers supply them continent-wide where the French layers stop at the border.

### 6.1 Idrisi raster format and grid

Each layer is a pair: a headerless binary body (.rst) and a plain-text documentation file (.RDC) that supplies everything needed to read it. The .RDC is the schema — there is no other.

*Table 6.1 — Europe. Continental. ESDB. Input for the 1 km grid, common to all twenty layers.*

|  | **Value** |
| --- | --- |
| X initial (min. X) | 1 500 000 m |
| Y initial (min. Y) | 900 000 m |
| Grid rotation | 0 |
| Nr. ΔX / Nr. ΔY | 5 900 / 4 600 (27 140 000 cells) |
| ΔX / ΔY | 1 000 m / 1 000 m |
| Extent | X 1 500 000 – 7 400 000 m, Y 900 000 – 5 500 000 m |
| Reference system | _etrs_laea — ETRS89 Lambert Azimuthal Equal Area, EPSG:3035 |
| Row order | Top-left origin, row-major, no padding — verified: every file size equals rows × columns × bytes-per-cell exactly |
| "real" encoding | 32-bit IEEE float, little-endian → 108 560 000 bytes |
| "byte" encoding | 8-bit unsigned → 27 140 000 bytes |
| Declared no-data | flag value : none — in every one of the twenty headers |

The naming grammar is <unit>_EU_<horizon>_<property>: STU = Soil Typological Unit, SMU = Soil Mapping Unit; T = topsoil, S = subsoil.

*Table 6.2 — Europe. Continental. ESDB. The twenty layers, with statistics computed over the binary bodies. "Data cells" counts non-zero cells; "mean" is over those cells only.*

| **Layer** | **Title** | **Type** | **Units** | **Data cells** | **Data %** | **Range (>0)** | **Mean** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| STU_EU_T_CLAY | Clay content, topsoil | real | % | 13 381 893 | 49.31 | 3 – 76 | 20.69 |
| STU_EU_S_CLAY | Clay content, subsoil | real | % | 11 877 814 | 43.76 | 4 – 73 | 23.43 |
| STU_EU_T_SAND | Sand content, topsoil | real | % | 13 381 893 | 49.31 | 1 – 90 | 44.81 |
| STU_EU_S_SAND | Sand content, subsoil | real | % | 11 774 505 | 43.38 | 2 – 90 | 44.03 |
| STU_EU_T_SILT | Silt content, topsoil | real | % | 13 381 893 | 49.31 | 1 – 69 | 34.50 |
| STU_EU_S_SILT | Silt content, subsoil | real | % | 11 774 796 | 43.39 | 2 – 71 | 32.43 |
| STU_EU_T_GRAVEL | Gravel content, topsoil | byte | % | 13 186 101 | 48.59 | 1 – 48 | 8.02 |
| STU_EU_S_GRAVEL | Gravel content, subsoil | byte | % | 11 409 276 | 42.04 | 1 – 41 | 8.39 |
| STU_EU_T_BD | Bulk density, topsoil | real | g/cm³ | 13 381 893 | 49.31 | 0.231 – 1.78 | 1.371 |
| STU_EU_S_BD | Bulk density, subsoil | real | g/cm³ | 11 877 814 | 43.76 | 0.233 – 1.74 | 1.380 |
| STU_EU_T_OC | Organic carbon, topsoil | real | % | 13 381 893 | 49.31 | 0.07 – 39.40 | 2.97 |
| STU_EU_S_OC | Organic carbon, subsoil | real | % | 11 877 814 | 43.76 | 0.11 – 39.16 | 2.18 |
| STU_EU_T_TEXT_CLS | Texture class, topsoil | byte | class | 13 381 893 | 49.31 | 1 – 8 | — |
| STU_EU_S_TEXT_CLS | Texture class, subsoil | byte | class | 11 774 796 | 43.39 | 1 – 8 | — |
| STU_EU_T_TAWC | PTF total available water content, topsoil | real | mm | 13 381 678 | 49.31 | 12.57 – 118.57 | 52.66 |
| STU_EU_S_TAWC | PTF total available water content, subsoil (≤100 cm) | real | mm | 11 877 814 | 43.76 | 8.13 – 276.67 | 106.62 |
| SMU_EU_T_TAWC | PTR total available water content, topsoil | real | mm | 13 380 565 | 49.30 | 12.57 – 118.57 | 52.66 |
| SMU_EU_S_TAWC | PTR total available water content, subsoil (≤100 cm) | real | mm | 11 924 285 | 43.94 | 8.13 – 276.67 | 104.64 |
| STU_EU_DEPTH_ROOTS | Depth available to roots | byte | cm | 13 381 678 | 49.31 | 10 – 150 | 100.87 |
| STU_EU_ALLOCATION | Area of STU allocation | byte | flag | 4 837 066 | 17.82 | 1 only | — |

The STU / SMU distinction in the TAWC pairs is methodological, not a duplication: STU_\*_TAWC is derived with pedo-transfer functions ("PTF" in the header title), SMU_\*_TAWC with pedo-transfer rules ("PTR"). The two headers differ only in that token and the declared ranges are identical, but the binary bodies are not — their MD5 checksums differ and their data-cell counts differ by about 46 000 cells. They are two estimates of the same quantity and should not be used interchangeably.

STU_EU_ALLOCATION is not a soil property but a provenance flag: it marks the 4 837 066 cells (17.82 % of the grid) where a specific soil typological unit could be spatially allocated within its mapping unit. Everywhere else the property values are mapping-unit level, so point estimates taken outside the allocated area carry a coarser support than the 1 km cell size suggests.

### 6.2 The undeclared no-data value

Every one of the twenty .RDC files declares "flag value : none" and "flag def'n : none". No no-data value is defined anywhere in the product. Yet between 50.7 % and 58.0 % of each grid holds exactly 0 — sea, non-European land inside the LAEA window, and unmapped areas. Zero is doing double duty as both a valid measurement and an absence.

For most layers the ambiguity is harmless in practice: a clay content of exactly 0 %, a bulk density of 0 g/cm³ or a rooting depth of 0 cm are all physically implausible, and the minimum observed non-zero values (3 %, 0.231 g/cm³, 10 cm) sit well clear of zero. Treating 0 as no-data is therefore safe for the continuous layers. It is not safe to assume this silently: a naive mean over the full grid understates topsoil clay content by more than half (10.20 % against 20.69 % over data cells). Please note also that the zero fractions differ between layers — topsoil layers have data on 49.31 % of cells and subsoil layers on 43.4–43.8 % — so cell-by-cell arithmetic between two layers produces results on an intersection that must be computed rather than assumed.

### 6.3 Texture class and clay content

The texture-class layers are the only ones carrying a legend in their .RDC (legend cats : 6).

*Table 6.3 — Europe. Continental. ESDB. Texture class legend and cell counts.*

| **Code** | **Definition (as printed in the .RDC)** | **Topsoil cells** | **Subsoil cells** |
| --- | --- | --- | --- |
| 1 | Coarse (18 % < clay and > 65 % sand) | 2 789 691 | 2 385 918 |
| 2 | Medium (18 % < clay < 35 % and ≥ 15 % sand, or 18 % < …) | 8 573 895 | 7 396 428 |
| 3 | Medium fine (< 35 % clay and < 15 % sand) | 344 | 50 478 |
| 4 | Fine (35 % < clay < 60 %) | 1 367 714 | 1 356 380 |
| 5 | Very fine (clay > 60 %) | 5 387 | 10 482 |
| 8 | No mineral texture (peat soils) | 644 862 | 575 110 |
| 0 | (not in the legend — no data) | 13 758 107 | 15 365 204 |

Two observations. The class-1 definition as printed cannot be right as literally written — a coarse class cannot require more than 18 % clay; the intended definition is clay < 18 % and sand > 65 %, and the wording appears identically in both the topsoil and subsoil headers. And the class-2 definition is truncated mid-sentence at the 60-character legend field width the Idrisi format allows; the full ESDB definition continues "…or 18 % < clay < 35 % and < 15 % sand". Use EUR26082EN.pdf, delivered alongside, for the authoritative definitions. Class 3 is also almost absent from the topsoil grid — 344 cells against 50 478 in the subsoil — which is a real feature of the source classification but makes the topsoil texture layer effectively a five-class layer.

*Table 6.4 — Europe. Continental. ESDB. Clay content distribution over data cells.*

| **Band** | **Topsoil cells** | **Subsoil cells** |
| --- | --- | --- |
| 0 – 10 % | 2 443 847 | 1 738 043 |
| 10 – 18 % | 1 855 450 | 1 052 296 |
| 18 – 25 % | 6 554 214 | 3 907 909 |
| 25 – 35 % | 764 786 | 3 792 153 |
| 35 – 45 % | 855 288 | 660 185 |
| 45 – 60 % | 902 921 | 716 746 |
| > 60 % | 5 387 | 10 482 |
| ≥ 35 % (fine and very fine — the shrink–swell-relevant fraction) | 1 763 596 (13.18 %) | 1 387 413 (11.68 %) |

Percentiles over data cells: topsoil clay 5th/25th/50th/75th/95th = 5 / 11 / 21 / 23 / 45 %; subsoil 5 / 18 / 23 / 28 / 47 %. Subsoil is systematically the more clay-rich horizon, as expected from illuviation, and it is the horizon that matters for foundation-depth shrink–swell.

EUR26082EN.pdf (6.7 MB) is the JRC technical report documenting the derivation of these layers and the Idrisi format; a byte-identical copy is filed under data_standards\\. STU_EU_Layers.zip (56.9 MB) holds all 40 files of the folder — a complete redundant copy of 1.68 GB of uncompressed content.

## 7. Companion Hazard — Water-Table Rise Sensitivity

Sensitivity to groundwater rise (remontée de nappe), the companion hazard to clay shrink–swell: both are governed by the water content of the near-surface ground, so a shrink–swell service needs the water-table position to know whether the clay is wetting or drying. Delivered for three different areas, in two formats, across two top-level folders, with the BRGM methodology report.

### 7.1 Common schema

All three areal deliveries carry the identical six-field schema. The field names are ASCII; the values are long French sentences.

*Table 7.1 — France. Regional. Remontée de nappe. Attribute schema, in column order.*

| **Field** | **Type** | **Meaning** | **Domain** |
| --- | --- | --- | --- |
| CLASSE | C(80) / TEXT(80) | Sensitivity class | Three values: "Zones potentiellement sujettes aux débordements de nappe" (water-table overflow at the surface); "Zones potentiellement sujettes aux inondations de cave" (basement flooding); "Pas de débordement de nappe ni d'inondation de cave" (neither) |
| FIAB_MNT | C(80) | Reliability of the digital terrain model input | FORTE / FAIBLE, plus INCONNUE in the dept-64 layer |
| FIAB_ESO | C(80) | Reliability of the groundwater (eaux souterraines) input | FORTE / MOYENNE / FAIBLE |
| FIAB_TOT | C(80) | Combined reliability | FORTE / MOYENNE / FAIBLE / INCONNUE |
| CLASSEFIAB | C(80) | CLASSE and FIAB_TOT concatenated as one label for symbology | Up to 12 combinations (3 classes × 4 reliabilities) |
| gridcode | N(18,0) / INTEGER | Numeric encoding of CLASSE | 0 = neither, 1 = water-table overflow, 2 = basement flooding — verified one-to-one against CLASSE |

CLASSEFIAB is fully redundant — it is the string concatenation of CLASSE and FIAB_TOT — and gridcode is fully redundant with CLASSE. Only CLASSE, FIAB_MNT, FIAB_ESO and FIAB_TOT carry independent information.

Please note that the class ordering is NOT a severity ranking. gridcode 1 (water table reaching the surface) is more severe than gridcode 2 (water reaching basement depth only), so the numeric codes ascend in the opposite direction to the hazard. Sorting or colour-ramping on gridcode inverts the hazard.

### 7.2 The three areal deliveries

*Table 7.2 — France. Regional. Remontée de nappe. Coverage, geometry and encoding.*

| **Layer** | **Area covered** | **Format** | **Features** | **Parts** | **Vertices** | **Area (km²)** | **Encoding** |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Dept_64\\VECTEUR\\Re_Nappe_fr | Département 64 (Pyrénées-Atlantiques) | Shapefile | 5 859 | 8 515 | 83 638 | 7 689.71 | .cpg says ISO-8859-1 |
| Inondations par remontée de nappe_89.gpkg | Département 89 (Yonne) | GeoPackage | 9 376 | — | 136 758 | 7 451.28 | UTF-8 (GeoPackage default) |
| Rising of watertable\\BourgogneFrancheComté | Bourgogne-Franche-Comté region | Shapefile | 56 351 | 86 627 | 842 153 | 47 976.89 | .cpg says UTF-8 |

Two of the three overlap. The Yonne is one of the eight départements of Bourgogne-Franche-Comté, and the GeoPackage bounding box falls entirely inside the regional shapefile's. The Yonne file holds 16.6 % of the region's features and 16.2 % of its vertices, consistent with a clean département clip rather than a re-generalisation. Département 64 is in a different region and does not overlap either.

Please note that the three layers use three different declared encodings for the same French text — ISO-8859-1, UTF-8 and the GeoPackage's implicit UTF-8. Each declaration is correct for its own file, but a script that hard-codes one will corrupt the other two.

*Table 7.3 — France. Regional. Remontée de nappe. Class distribution by area.*

| **CLASSE** | **Dept 64: features / km²** | **Yonne: features / km²** | **Bourgogne-F-C: features / km²** |
| --- | --- | --- | --- |
| Débordements de nappe (overflow) | 2 145 / 612.51 | 1 979 / 703.07 | 16 177 / 5 831.79 |
| Inondations de cave (basement flooding) | 3 632 / 966.67 | 5 356 / 1 262.70 | 33 754 / 9 976.35 |
| Pas de débordement ni inondation (neither) | 82 / 6 110.52 | 2 041 / 5 485.51 | 6 420 / 32 168.74 |
| TOTAL | 5 859 / 7 689.71 | 9 376 / 7 451.28 | 56 351 / 47 976.89 |

Please note the shape of the "neither" class: in département 64 it is 82 polygons covering 6 111 km² — an average of 75 km² each — while the two hazard classes are thousands of small polygons. The unaffected background is mapped as a few very large shapes and the hazard as many small ones, so per-polygon statistics on these layers are meaningless without area weighting. Unlike the clay shrink–swell layers, these files carry no attribute area field, so the areas above were computed from the geometry. The dept-64 total of 7 689.71 km² is within 0.06 % of the geology layer's 7 694.50 km² for the same département.

### 7.3 The impermeable-outcrop mask

Masque_BDLISA_IMPERMEABLE is a single-record shapefile: one polygon of 459 parts and 32 575 vertices covering 663.94 km², with a single attribute Id = 0. Its .qml labels it "Entités hydrogéologiques imperméables à l'affleurement (source : BDLISA)" — hydrogeological units that are impermeable at outcrop, from the BDLISA national aquifer reference layer.

This is an exclusion mask, not a hazard class. Where impermeable formations crop out there is no shallow aquifer to rise, so the sensitivity mapping does not apply and the Re_Nappe_fr classes should be suppressed. Its extent sits inside the département-64 layer's. No equivalent mask is delivered for the Yonne or the regional layer, so those two cannot be masked consistently with this one.

### 7.4 Symbology — and a style that does not match its data

Six style files are delivered for the dept-64 layers: three QGIS .qml and three ArcGIS .lyr, all written against QGIS 2.18.14 (2017) or its ArcGIS contemporary.

*Table 7.4 — Pyrénées-Atlantiques (64). Département. Remontée de nappe. Style files.*

| **File** | **Renderer** | **Classifies on** | **Status** |
| --- | --- | --- | --- |
| QGIS\\Re_Nappe_fr.qml | categorizedSymbol | CLASSE | Works — the three category values match the data exactly |
| QGIS\\Masque_BDLISA_IMPERMEABLE.qml | categorizedSymbol | (single category) | Works |
| QGIS\\Rexite_Nappe_fr_vs_fiabilite.qml | categorizedSymbol | CLASSEFIAB | BROKEN — see below |
| ARCGIS\\Re_Nappe_fr.lyr, Re_Nappe_fr_fiabilite.lyr, Masque_BDLISA_IMPERMEABLE.lyr | ArcGIS layer files | — | Binary ArcMap format; not verified against the data |

Rexite_Nappe_fr_vs_fiabilite.qml defines 13 categories on CLASSEFIAB. Not one matches any value in the data: the style spells the reliability qualifier with a lower-case f ("…, fiabilité FORTE") while every record in the .dbf spells it with a capital F ("…, Fiabilité FORTE"). All 12 of the layer's distinct CLASSEFIAB values match a style category when case is ignored, and none matches when it is not. Applied as delivered, the style renders every one of the 5 859 features into the unclassified bucket. The fix is a case-insensitive rewrite of the 13 category values. The style also carries a category for "fiabilité INCONNUE" plus one empty value, which is why it has 13 entries where the data has 12.

### 7.5 The methodology report

Remontee_Nappe_2018_RP_65452.pdf is BRGM/RP-65452-FR, "Étude méthodologique pour l'amélioration de la cartographie de sensibilité aux remontées de nappes et réalisation d'une carte nationale", final report, January 2018, 119 pages, marked "Document public". This is the authoritative description of how the CLASSE and FIAB_\* fields were derived — the only methodology document in the collection for any of its hazard layers. A byte-identical copy is filed under data_standards\\ as georisques.pdf, where its name misdescribes it (Section 9.1).

## 8. Meteorological Trigger — Combined Drought Indicator

One year of the Copernicus European Drought Observatory Combined Drought Indicator, the meteorological driver of the shrink–swell cycle: clay shrinkage follows soil-moisture deficit, and the CDI is the operational product that tracks it.

### 8.1 File structure and grid

cdinx_m_edo_20250101_20251011_t.nc — NetCDF-4, 2.76 MB, product version 4.0.0, produced 2025-01-24.

*Table 8.1 — Europe. Continental. EDO CDI. Input for the grid.*

|  | **Value** |
| --- | --- |
| Longitude initial | −24.979167 °E (cell centre); GeoTransform origin −25.0 |
| Latitude initial | 71.979167 °N (cell centre); GeoTransform origin 72.0 |
| Nr. Δlon / Nr. Δlat | 1 824 / 1 200 |
| Δlon / Δlat | 0.04166666667° (1/24°) in both axes |
| Extent | −25.0 to 51.0 °E, 22.0 to 72.0 °N (14_EXTENT_WKT) |
| Latitude direction | Descending — lat[0] = 71.979167, step −0.0416667 |
| CRS | EPSG:4326, WGS 84, with a spatial_ref variable carrying the WKT and GeoTransform |

*Table 8.2 — Europe. Continental. EDO CDI. Variable schema.*

| **Variable** | **Type** | **Dimensions** | **Attributes** | **Content** |
| --- | --- | --- | --- | --- |
| cdinx | uint8 | (time, band, lat, lon) = (29, 1, 1200, 1824) | _FillValue 8; add_offset 0.0; scale_factor 1.0; grid_mapping spatial_ref | The drought class, 0–8 (Table 8.3). 63 475 200 values. |
| time | int64 | (time) | units "days since 2025-01-01 00:00:00"; calendar proleptic_gregorian | 29 dekadal steps, 0 to 283 |
| band | int64 | (band) | — | Constant 1 — a vestigial GeoTIFF band axis of length 1 |
| lat / lon | float64 | (lat) / (lon) | _FillValue NaN | Cell-centre coordinates |
| spatial_ref | — | scalar | crs_wkt, grid_mapping_name latitude_longitude, GeoTransform | CRS container variable |

*Table 8.3 — Europe. Continental. EDO CDI. Class legend, with observed counts over all 63 475 200 cells.*

| **Code** | **Meaning** | **Cells** | **Share** |
| --- | --- | --- | --- |
| 0 | No drought | 11 098 502 | 17.485 % |
| 1 | Watch — precipitation deficit | 1 975 272 | 3.112 % |
| 2 | Warning — soil-moisture deficit following precipitation deficit | 8 381 613 | 13.205 % |
| 3 | Alert — vegetation stress following precipitation / soil-moisture deficit | 1 081 057 | 1.703 % |
| 4 | Recovery to normal conditions | 1 026 815 | 1.618 % |
| 5 | Temporary soil-moisture recovery | 406 558 | 0.640 % |
| 6 | Temporary vegetation recovery | 172 596 | 0.272 % |
| 7 | No data inside the LISFLOOD domain | 0 | 0 % |
| 8 | Outside the LISFLOOD domain | 39 332 787 | 61.966 % |

The class codes are categorical, not ordinal: 0–3 ascend in drought severity but 4–6 are recovery states and 7–8 are absence codes. Do not treat cdinx as a numeric scale.

Please note that class 7 never occurs and class 8 — 62 % of the grid — is also declared as the variable's _FillValue, so a CF-aware reader masks class 8 automatically and cannot distinguish "outside the domain" from "missing inside the domain". Since class 7 is empty here the distinction costs nothing, but code relying on it will mis-handle a file in which class 7 does appear.

### 8.2 The time axis and its metadata

29 steps, dekadal: the 1st, 11th and 21st of each month from 2025-01-01 to 2025-10-11. The step length is therefore not constant — 22 intervals of 10 days, 5 of 11 days (the 21st to the 1st in 31-day months) and 1 of 8 days (21 February to 1 March). This is the standard dekad convention and not an error, but resampling code that assumes a regular 10-day cadence will drift by up to three days.

Please note a metadata trap. Attributes 35_START_PERIOD and 36_END_PERIOD both read "2025-01-01", the global time attribute reads "2025-01-01", long_name reads "cdinx_m_edo_t_400_z02" and source_key points at a single GeoTIFF — all of these describe the first time slice only, carried over unchanged when the 29 dekadal GeoTIFFs were stacked into one NetCDF. The file name and the time coordinate variable are the only places the true period is recorded correctly. Do not read the period from the global attributes.

The file carries 38 numbered global attributes, prefixed 01_ to 38_ so they sort in a fixed order — an unusual but self-documenting convention. They include the title, abstract, licence (CC BY 4.0), citation statement, the reference publication (Cammalleri et al., NHESS 21, 481–502, 2021, doi:10.5194/nhess-21-481-2021), the factsheet and FTP URLs, the JRC data catalogue identifier afa8a5ee-5473-439a-b062-ffdaedc38b2d, and a checksum (32_CHECKSUM_VALUE 11557313.0, method "sum of abs values"). cdinx_in_cdinx_m_edo_20250101_20251011_t.png (1040 × 704, RGB) is a rendered preview, not data.

## 9. Supporting Material

### 9.1 data_standards

*Table 9.1 — Collection. Contents of data_standards.*

| **File** | **What it actually is** | **Assessment** |
| --- | --- | --- |
| shapefile_standards.pdf | 7 pages — the ESRI Shapefile Technical Description whitepaper | Correct. Documents the .shp/.shx/.dbf structure used by ten layers here. |
| idrisi_raster_european_soil_database.pdf | Byte-identical to EU WIDE DATASET SSR\\EUR26082EN.pdf, the JRC report on the ESDB derived data and its Idrisi encoding | Correct, though redundant with the copy filed alongside the data. |
| cf_conventions_netcdf.txt | 85 bytes — a single URL to the CF-1.13 conventions | A pointer, not a document. Note that the CDI NetCDF declares no Conventions attribute, so which CF version it targets is not recorded in the file. |
| georisques.pdf | Byte-identical to the BRGM water-table methodology report (BRGM/RP-65452-FR, January 2018, 119 pages, French) | MISLABELLED. Not a Géorisques data standard but a report about a different hazard. No Géorisques data specification exists anywhere in the collection. |

### 9.2 licences

Licence terms are recorded per dataset, which is the right structure, but three of the five folders duplicate files held elsewhere and one records an unresolved question.

*Table 9.2 — Collection. Licence coverage by dataset.*

| **Dataset** | **Licence folder** | **Terms** |
| --- | --- | --- |
| Géorisques clay shrink–swell (45) | licences\\georisques_45\\Licence_Ouverte.pdf | Etalab Open Licence 2.0 — reuse permitted including commercially, attribution required. Byte-identical copies also in georisques_45\\ and licences\\georisques_49\\. |
| Géorisques water table (49) | licences\\georisques_49\\Licence_Ouverte.pdf | Etalab Open Licence 2.0, the same file. |
| BRGM geology (46) | licences\\brgm_46\\ — 3 files | Etalab Open Licence 2.0 with an English translation, plus the InfoTerre conditions of use with an English translation. All four also present in brgm_46\\OUTILS\\. |
| Copernicus CDI (48) | licences\\copernicus_drought_indicator_48\\creative_commons.txt | CC BY 4.0, © European Union 1995–2025. Consistent with the NetCDF 09_LICENSE attribute. |
| European Soil Database (47) | licences\\european_soil_database_47\\maybe_creative_commons.txt | UNRESOLVED — see below. |

The ESDB licence file is the one to read before reusing anything. Its full text is:

Licence:
The permission to use the data specified above is granted on condition that, under NO
CIRCUMSTANCES are these data passed to third parties. They can be used for any purpose,
including commercial gain.

? Creative commons ?

The file name ("maybe_creative_commons.txt") and the trailing question mark record that the collector was unsure. The quoted terms are not Creative Commons and are not compatible with any CC licence: a no-redistribution condition contradicts every CC variant. Taken at face value, the twenty ESDB rasters — 1.6 GB, the bulk of the collection — may be used but may not be passed on. That constraint governs any onward publication of this collection and should be resolved with ESDAC (ec-esdac@ec.europa.eu) before the data is redistributed.

### 9.3 powerpoint\\data_exploration_gl.pptx

A four-slide deck, one slide per dataset, rendering rows of the tracking spreadsheet named in readme.txt. Each slide uses the same five-heading frame — Source, Standards, File Details, Licence, Variable — plus Experiment, Scale, Type, Contact, Dimensions and Projection.

*Table 9.3 — Collection. What the deck documents, and what it omits.*

| **Slide** | **Dataset** | **Corresponds to** |
| --- | --- | --- |
| 1 | Areas exposed to shrinkage and swelling of clay soils (RGA zones) | georisques_45 and FRENCH BRGM DATASET SSR (Sections 3–4) |
| 2 | Geotechnical behaviour (ability to absorb water and shrink/swell capacity) | brgm_46 (Section 5) |
| 3 | Mineralogy — proportion of clay fraction containing swelling clay minerals such as smectites | EU WIDE DATASET SSR (Section 6) |
| 4 | Combined Drought Indicator | copernicus_drought_indicator_48 (Section 8) |
| — | (no slide) | The water-table datasets of Section 7 — roughly 64 MB and three layers — are not described in the deck at all. |

Two of the deck's statements do not survive checking against the files. Slide 2 gives the coordinate system of the BRGM geological data as "urn:ogc:def:crs:EPSG:6.11 / NTF Lambert II Extended"; the delivered layers are all RGF93 / Lambert-93 (EPSG:2154), a different datum and a different projection. And slide 1 gives the attribute schema as "Dpt (str), Niveau (int), elea (str), geometry (POLYGON)" — the third field is spelled alea, not elea, and in the GeoPackage delivery Niveau is a REAL rather than an integer. Slide 3's "Variables:" heading is left empty, so the twenty ESDB layers are nowhere enumerated in the deck.

~$data_exploration_gl.pptx (165 bytes) at the collection root is a PowerPoint owner-lock file left behind by an open editing session, containing only a user name. It is not data and should be deleted.

## 10. SWI INDICATOR – EU WIDE — not delivered

The folder exists and is empty. No file, no README, no placeholder.

From its name the intended content is the Soil Water Index, the pan-European soil-moisture product distributed by the Copernicus Global Land Service, which would have complemented the Combined Drought Indicator of Section 8 by giving a continuous moisture state rather than a drought class. Nothing in readme.txt, the deck or the licence folders refers to it, so neither its intended source nor its intended resolution can be established from the delivery. No schema can be documented.

## 11. Cross-Dataset Linkages

The collection is an assembly of independently sourced datasets, so most of its parts have no formal relationship. The linkages that do exist were verified against the file contents.

*Table 11.1 — Collection. Verified relationships.*

| **A** | **B** | **Relationship** | **Evidence** |
| --- | --- | --- | --- |
| National RGA GeoPackage (2019) | georisques_45\\AleaRG64_L93 | B is a département-64 clip of A | Querying A for DPT = "64" returns 3 059 features split 2 284 / 703 / 72 and a computed area of 6 239.1 km²; B has 3 059 features, the same split and 6 239.08 km². |
| AleaRG64_L93 (shapefile) | AleaRG64_L93.csv | B is a WKT export of A | Identical attribute triples in identical record order; ring counts match part counts for all 3 059 records including the 114 multi-ring ones; 660 362 coordinate pairs in both. |
| National RGA GeoPackage (2019) | AleaRG_2025_Fxx_L93 | Two vintages of one product, NOT interchangeable | Total mapped area agrees to 0.03 % but Fort grows from 73 520 to 105 679 km² (+43.7 %) while Faible falls from 129 546 to 93 863 km² (−27.5 %). |
| georisques_45\\AleaRG64_L93 | brgm_46\\S_FGEOL | Same département, same footprint, no formal join | Extents agree to within 70 m in X and 3 m in Y. But the hazard layer carries no formation key and the geology layer no susceptibility class, so the two can only be related spatially, by overlay. |
| brgm_46 layers | brgm_46\\OUTILS | The layers reference the toolkit by name | NOM_SYMB values resolve to SVG files in Polygon_Patterns\\ and Line_Symbols\\; ARCGIS_COD values resolve into REF_GEOL_BRGM_2008.style. |
| BourgogneFrancheComté | Inondations par remontée de nappe_89.gpkg | B is a Yonne clip of the region A covers | B's bounding box lies wholly inside A's; B holds 16.6 % of A's features and 16.2 % of its vertices; identical six-field schema; B's 7 451 km² is the area of the Yonne. |
| Dept_64\\VECTEUR | Dept_64\\MASQUES | The mask is an exclusion layer for the sensitivity layer | The mask's .qml identifies it as impermeable hydrogeological units at outcrop from BDLISA; its extent sits inside the sensitivity layer's. |
| Dept_64 water-table layers | Remontee_Nappe_2018_RP_65452.pdf | The report is the method behind CLASSE and FIAB_\* | BRGM/RP-65452-FR is the national methodology study for exactly this mapping. |

What is missing is the join that would make the collection into a service. Three of the four hazard-relevant inputs — the geological formations, the European clay-content rasters and the drought indicator — have no key connecting them to the shrink–swell classes, and no correspondence table is supplied. A susceptibility assessment built from this collection would need a lookup from BRGM NOTATION or CODE to a susceptibility class, a rule relating ESDB clay content and texture class to the same scale, and a reprojection chain between EPSG:2154, EPSG:3035 and EPSG:4326. None of the three is present.

One further gap is worth stating plainly. Département 64 is covered by the hazard extract, the geological map and the water-table sensitivity layer, and is the only area where all three coincide — it is evidently the pilot area. But the drought indicator and the ESDB rasters are pan-European, and the Bourgogne water-table layers cover an unrelated region. The collection has one deep vertical slice and several broad shallow ones, with no intermediate coverage.

## 12. Data Quality Notes and Usage Caveats

Ordered by severity. Each is also flagged inline where the file is described.

1. AleaRG_2025_Fxx_L93 has no .shp. The current-vintage national clay shrink–swell map is delivered without its geometry; the .shx, .dbf, .prj and .cpg are present and mutually consistent at 121 399 records, and the index implies a missing file of 420 791 812 bytes (401.3 MB). The layer cannot be opened by any GIS. Only the attribute table, including the per-polygon surf_m2 areas, remains usable, and only as a non-spatial table. Re-download from Géorisques before relying on the 2025 vintage.
2. Two incompatible vintages of the same hazard map are delivered side by side with nothing to distinguish them but a year buried in one file name. Roughly 36 000 km² is reclassified upward between them. Mixing them, or differencing them as though the change were physical, produces a spurious result.
3. The ESDB licence is unresolved and, as quoted, prohibits redistribution. Until this is settled with ESDAC, the 1.6 GB of soil rasters — 60 % of the collection by volume — cannot safely be passed on with it.
4. The twenty Idrisi rasters declare "flag value : none" but use 0 as an undeclared no-data value across 50.7–58.0 % of each grid. A mean over the full grid understates topsoil clay content by more than half. The zero fraction also differs between layers, so cell-by-cell arithmetic between any two operates on an intersection that must be computed.
5. Rexite_Nappe_fr_vs_fiabilite.qml matches none of its data: all 13 style categories spell the qualifier "fiabilité" in lower case; all 12 data values spell it "Fiabilité". Zero categories match exactly, all 12 match case-insensitively, and the style renders every one of the 5 859 features as unclassified.
6. data_standards\\georisques.pdf is not a Géorisques standard. It is a byte-identical copy of the BRGM water-table methodology report filed under a name that describes something else. No Géorisques data specification is present anywhere in the collection.
7. The Géorisques WFS pointer describes a third version of the hazard layer: 80 000 features against 122 222 and 121 399, served in EPSG:4326 rather than EPSG:2154, with a declared western bound far out in the Atlantic. Anything fetched live from that endpoint will not match either delivered file.
8. The folder-naming convention stated in readme.txt does not hold throughout, and one file name uses the same two-digit position for an INSEE département code rather than an issue number. The suffix cannot be parsed mechanically.
9. gridcode is not an ordinal. In all three water-table layers code 1 (surface overflow) is more severe than code 2 (basement flooding), so the numeric codes ascend opposite to the hazard.
10. The PENDAGE field of P_STRUCT uses 999 as an undeclared sentinel in 903 of 5 927 records, and 0 in a further 1 834 where it means both "genuinely horizontal" and "not measured".
11. Three different declared character encodings are used for the same French text across the three water-table layers — ISO-8859-1, UTF-8 and the GeoPackage's implicit UTF-8. A pipeline that hard-codes one will corrupt the other two.
12. The brgm_46 shapefiles ship no .cpg. The encoding is recoverable from the dBASE language-driver byte (0x57, ANSI / Windows-1252), but readers that ignore it and default to UTF-8 fail outright on the accented French in DESCR and NOTATION.
13. Neither hazard dataset carries the rule that produced its classes. The clay shrink–swell layers give a class per polygon with no thresholds; the water-table layers give a class and three reliability grades whose derivation is described only in the accompanying 119-page French report. Neither classification can be reproduced or extended from the delivered data alone.
14. The Combined Drought Indicator's global attributes describe only its first time slice, although the file holds 29 dekads. Read the period from the time coordinate variable or the file name, never from the attributes.
15. CDI class 8 ("outside the LISFLOOD domain", 62 % of the grid) is also the variable's declared _FillValue, so a CF-aware reader cannot distinguish it from class 7. Class 7 is empty here, so nothing is lost, but code relying on the distinction will mis-handle files where it is not.
16. The CDI time step is dekadal, not regular: 22 intervals of 10 days, 5 of 11 and 1 of 8. Resampling code that assumes a fixed cadence will drift.
17. The CDI class codes are categorical, not ordinal — 0–3 ascend in severity but 4–6 are recovery states and 7–8 absence codes. Do not treat cdinx as a numeric scale.
18. Substantial verbatim duplication, all verified by MD5: STU_EU_Layers.zip duplicates the 1.68 GB STU_EU_Layers folder; GEO050K_HARM_064.zip and Outils.zip duplicate their folders; georisques_shrink_swell_clay_soils.zip duplicates the loose dept-64 shapefile; Licence_Ouverte.pdf exists in three places; EUR26082EN.pdf and the BRGM water-table report each exist twice; the four brgm licence documents each exist twice; and the "Descriptif des cartes géologiques" PDF exists twice. Roughly 1.75 GB of the 2.70 GB unpacked total is redundant.
19. The GeoPackage embeds a QGIS metadata record whose identifier, title, abstract, contact and fees elements are all empty; its only substantive content is the CRS WKT. The 2026-05-27 last_change stamp records when QGIS wrote the file, not the vintage of the data inside it.
20. The four-slide deck omits the water-table datasets entirely and misstates two facts about the ones it covers: the BRGM geology CRS (NTF Lambert II Extended where the files are RGF93 / Lambert-93) and the hazard attribute name ("elea" where the field is "alea"). Its slide 3 "Variables:" heading is empty, so the twenty ESDB layers are never enumerated.
21. The Idrisi texture-class legend as printed defines class 1 as "Coarse (18 % < clay and > 65 % sand)", which cannot be right as written; the intended definition is clay < 18 % and sand > 65 %. The class-2 definition is truncated mid-sentence by the format's legend field width.
22. STU_EU_ALLOCATION is a provenance flag, not a property: it marks only the 17.82 % of cells where a soil typological unit could be spatially allocated. Elsewhere the twenty property layers hold mapping-unit averages.
23. The STU and SMU total-available-water-content layers are two different estimates, not duplicates. Their headers differ only in "PTF" against "PTR" and their declared ranges are identical, but their binary bodies differ by MD5 and by about 46 000 data cells. Choosing between them is a methodological decision the file names do not signal.
24. The "no hazard" background in the water-table layers is mapped as a handful of very large polygons — 82 polygons covering 6 111 km² in dept 64 against 5 777 small hazard polygons. Per-polygon statistics are meaningless without area weighting.
25. The clay shrink–swell layers have no "not exposed" polygon at all. The mapped 395 589 km² is about 72 % of metropolitan France; the remaining 28 % is absent from the layer and means "outside the formations considered", not "no hazard".
26. Housekeeping artefacts are present throughout: seven macOS .DS_Store files, an orphaned PowerPoint owner-lock file, six __MACOSX resource-fork stubs inside georisques_shrink_swell_clay_soils.zip, and one empty directory. None is data.
27. Minor .prj inconsistencies will defeat string comparison: standard parallels ordered 49 then 44 in most files but 44 then 49 in the geology and dept-64 water-table layers, and a datum named "D_Reseau_Geodesique_Francais_1993_v1" rather than "D_RGF_1993". All resolve to EPSG:2154.
28. Field names for the same concept differ across the three deliveries of the clay hazard: DPT / NIVEAU / ALEA, dpt / niveau / alea, and insee_dep / niveau / surf_m2. Case-sensitive column references will not port.
29. The 2025 attribute table over-specifies its field widths: insee_dep is C(80) for a 2- or 3-character code, and niveau is N(24,15) for an integer in the range 1–3. Readers honouring the declared precision return 1.000000000000000 rather than 1.

## 13. Glossary

*Table 13.1 — Abbreviations and terms.*

| **Term** | **Meaning** |
| --- | --- |
| Aléa | French: hazard. Here the classified level of exposure — Faible / Moyen / Fort = Low / Medium / High |
| ALEARG | The Géorisques layer code for the clay shrink–swell exposure map |
| BDLISA | Base de Données des Limites des Systèmes Aquifères — the French national hydrogeological reference layer |
| BRGM | Bureau de Recherches Géologiques et Minières — the French geological survey |
| CDI | Combined Drought Indicator — the Copernicus EDO product combining precipitation, soil-moisture and vegetation anomalies |
| CMYK / C, M, J, N | The four process-colour components. The French abbreviations in the BRGM fields are C (cyan), M (magenta), J (jaune) and N (noir) |
| Débordement de nappe | Water-table overflow — groundwater reaching the ground surface |
| Dekad | A ten-day reporting period beginning on the 1st, 11th and 21st of each month; the last runs 8 to 11 days |
| EDO | European Drought Observatory, part of the Copernicus Emergency Management Service |
| ESDB / ESDAC | European Soil Database, distributed by the European Soil Data Centre at the JRC |
| Etalab Open Licence | The French open-government licence; version 2.0 permits reuse including commercial use, subject to attribution |
| ETRS89-LAEA | European Terrestrial Reference System 1989 with Lambert Azimuthal Equal Area, EPSG:3035 |
| Fiabilité | Reliability; graded FORTE / MOYENNE / FAIBLE / INCONNUE |
| GEO050K_HARM | BRGM harmonised geological map at 1:50 000, assembled per département from the individual sheets |
| GeoPackage | OGC container format built on SQLite; geometry stored as OGC binary with a GPKG header |
| Géorisques | The French public portal for natural and technological risk data |
| Idrisi raster | A two-file raster format: a headerless binary body (.rst) plus a plain-text documentation file (.RDC) |
| Inondation de cave | Basement flooding — groundwater reaching cellar depth without reaching the surface |
| INSEE code | The French département code: 01–95 with 2A and 2B for Corsica; 64 = Pyrénées-Atlantiques, 89 = Yonne, 75 = Paris |
| Lambert-93 | RGF93 / Lambert-93, EPSG:2154 — the standard French national projected CRS, in metres |
| LISFLOOD | The JRC hydrological model whose domain defines the valid area of the CDI product |
| Nappe | Groundwater body / water table |
| NOTATION | The stratigraphic shorthand printed on a French geological map (e.g. "Fz" for recent fluvial alluvium) |
| Pendage / Azimut | Dip and strike azimuth of a structural measurement, in degrees |
| PTF / PTR | Pedo-transfer function / pedo-transfer rule — two ways of estimating a soil property from more readily measured ones |
| Remontée de nappe | Water-table rise — the companion hazard of Section 7 |
| RGA | Retrait-gonflement des argiles — clay shrinkage and swelling, the subject of the collection |
| SMU / STU | Soil Mapping Unit / Soil Typological Unit — the two levels of the ESDB spatial hierarchy |
| Smectite | The clay mineral group whose interlayer water uptake drives shrink–swell behaviour |
| SWI | Soil Water Index — the Copernicus soil-moisture product for which an empty folder is reserved |
| TAWC | Total Available Water Content — the water a soil can hold and release to plants, in millimetres |
| Texture class | The FAO/ESDB grouping by particle-size distribution: Coarse, Medium, Medium fine, Fine, Very fine, and peat |
| WFS | OGC Web Feature Service — a protocol for serving vector features over HTTP |

## 14. Provenance of This Document

Every schema, count, range, coordinate, area and checksum in this document was read from the delivered files. GeoPackage structure was read from the SQLite catalogue and the standard gpkg_ tables; geometry was decoded from the GPKG binary header and the OGC well-known-binary payload, and areas computed by shoelace summation over the rings in the layer's own projected units with holes subtracted. Shapefile geometry was read from the .shp record headers and part indices, and attributes from the .dbf field descriptors, decoded as CP1252, ISO-8859-1 or UTF-8 according to the .cpg or the dBASE language-driver byte. Idrisi rasters were read as raw arrays typed from their .RDC headers, and the header-declared dimensions checked against the actual file sizes for all twenty. NetCDF headers, coordinate variables and value histograms were read with the netCDF4 library. Style files were parsed as XML and their category values compared literally against the attribute values they classify. All duplication claims were verified by MD5. The Lambert-93, LAEA and Greek Grid to WGS 84 conversions of Table 2.2 were computed with pyproj for this document and are not part of the delivery. The figure was drawn from the delivered dept-64 shapefile with no external basemap.

Where a delivered README, a file name, a slide or an embedded attribute contradicts the file contents, the contents are treated as authoritative and the contradiction recorded in Section 12 rather than resolved. Two judgements in this document are editorial rather than measured, and are marked as such where they appear: the "clay-bearing" reading of the French geological descriptions in Table 5.3, and the interpretation of the intended content of the empty SWI folder in Section 10.

This revision follows the documentation conventions of the C2IMPRESS Coastal_flooding dataset description: the structure is organised by study area and by the role each dataset plays in the assessment rather than by folder; every extent is reported in both the native projected system and WGS 84; every raster grid is specified as origin, rotation, cell count and cell size; the column order of every positional or unlabelled file is stated explicitly; sign, unit and class conventions are stated wherever a value field appears; gaps and defects are flagged inline at first mention as well as collected in Section 12; and figure captions follow the Site. Domain. Model. Content hierarchy.
