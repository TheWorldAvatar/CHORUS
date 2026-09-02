# 3SES Data Schema

## 1. How to Read This Schema

Every type, width, domain and count below was measured from the delivered files, not taken from vendor documentation. Field types are given as the **storage type as delivered** (dBASE field descriptor, GeoPackage column declaration, Idrisi `data type`, or NetCDF variable dtype), followed by the **logical type** a consumer should use.

Notation used in the attribute tables:

| **Column** | **Meaning** |
| --- | --- |
| Field | Name exactly as stored, case-sensitive |
| Storage | Delivered type and width/precision. `String (2.0)` = 2-byte character field; `Real (24.15)` = dBASE numeric, 24 wide, 15 decimals; `Integer64 (18.0)` = numeric widened by the reader |
| Logical | Type to model the field as |
| Domain | Complete observed value set, or range, with record counts where the domain is closed |
| Null | Whether nulls or empty strings occur, measured over all records |

Two structural cautions apply throughout and are not repeated in every table:

- **The collection has no cross-dataset foreign keys.** Every linkage between themes is spatial, and requires a reprojection that is not supplied (Section 10).
- **The delivery is split across two archive parts.** `shrink_swell-20260820T150507Z-1-001.zip` holds 272 entries, `…-002.zip` holds 12. Seven raster bodies live only in part 002 (Section 6.3). A schema validated against part 001 alone will report those seven layers as header-without-body.

## 2. Entity Inventory

*Table 2.1 — Collection. All data-bearing entities, with their delivered format and cardinality.*

| **Entity** | **Role** | **Format** | **Path (under `shrink_swell\`)** | **Geometry** | **CRS** | **Records / cells** |
| --- | --- | --- | --- | --- | --- | --- |
| `risk_of_shrinkage_and_swelling_of_clays` | National RGA hazard, 2019 vintage | GeoPackage | `FRENCH BRGM DATASET SSR\Exposure map to clay shrinkage and swelling.gpkg` | MultiPolygon | 2154 | 122 222 |
| `AleaRG_2025_Fxx_L93` | National RGA hazard, 2025 vintage | Shapefile — **`.shp` absent** | `FRENCH BRGM DATASET SSR\` | None | 2154 (declared) | 121 399 |
| `AleaRG64_L93` | Dept-64 RGA extract | Shapefile | `georisques_45\` | Polygon | 2154 | 3 059 |
| `AleaRG64_L93.csv` | Dept-64 RGA extract, WKT export | CSV, UTF-8 | `georisques_45\` | WKT column | 2154 (implicit) | 3 059 + header |
| `GEO050K_HARM_064_S_FGEOL_2154` | Geological formation polygons | Shapefile | `brgm_46\GEO050K_HARM_064\` | Polygon | 2154 | 13 037 |
| `GEO050K_HARM_064_L_FGEOL_2154` | Formation boundary lines | Shapefile | `brgm_46\GEO050K_HARM_064\` | LineString | 2154 | 38 858 |
| `GEO050K_HARM_064_L_STRUCT_2154` | Structural lines (faults, thrusts) | Shapefile | `brgm_46\GEO050K_HARM_064\` | LineString | 2154 | 5 719 |
| `GEO050K_HARM_064_L_DIVERS_2154` | Miscellaneous lines | Shapefile | `brgm_46\GEO050K_HARM_064\` | LineString | 2154 | 653 |
| `GEO050K_HARM_064_P_STRUCT_2154` | Structural measurement points | Shapefile | `brgm_46\GEO050K_HARM_064\` | Point | 2154 | 5 927 |
| `GEO050K_HARM_064_P_DIVERS_2154` | Miscellaneous points | Shapefile | `brgm_46\GEO050K_HARM_064\` | Point | 2154 | 412 |
| `Re_Nappe_fr` | Water-table rise sensitivity, dept 64 | Shapefile | `WATER TABLE FR\Dept_64\VECTEUR\` | Polygon | 2154 | 5 859 |
| `re_nappe_fr` (GPKG) | Water-table rise sensitivity, Yonne (89) | GeoPackage | `WATER TABLE FR\Inondations par remontée de nappe_89.gpkg` | MultiPolygon | 2154 | 9 376 |
| `BourgogneFrancheComté` | Water-table rise sensitivity, BFC region | Shapefile | `Rising of watertable\` | Polygon | 2154 | 56 351 |
| `Masque_BDLISA_IMPERMEABLE` | Impermeable-outcrop mask, dept 64 | Shapefile | `WATER TABLE FR\Dept_64\MASQUES\` | Polygon | 2154 | 1 |
| 20 × ESDB soil layers | Soil properties, Europe | Idrisi `.rst` + `.RDC` | `EU WIDE DATASET SSR\STU_EU_Layers\` | Raster grid | 3035 | 27 140 000 cells each |
| `cdinx` | Combined Drought Indicator | NetCDF-4 | `copernicus_drought_indicator_48\cdinx_m_edo_20250101_20251011_t.nc` | Raster cube | 4326 | 29 × 1 200 × 1 824 |

Non-data entities not schematised here: `readme.txt`, `WFS SSC.txt` (a service pointer, Section 4.6), licence documents, the `data_standards\` PDF set, `powerpoint\data_exploration_gl.pptx`, the BRGM `OUTILS\` symbology toolkit (SVG patterns, TrueType symbol fonts, ArcGIS `.style`), and the QGIS `.qml` / ArcGIS `.lyr` style sidecars. The `SWI INDICATOR - EU WIDE\` directory is empty.

## 3. Reference Frames, Grids and Encodings

*Table 3.1 — Collection. Coordinate reference systems, by entity group.*

| **Group** | **EPSG** | **Units** | **Declared as** |
| --- | --- | --- | --- |
| All French vector layers | 2154 — RGF93 v1 / Lambert-93 | metre | `.prj` WKT, and `gpkg_spatial_ref_sys` for the two GeoPackages |
| ESDB soil rasters | 3035 — ETRS89-extended / LAEA Europe | metre | `.RDC` key `ref. system : _etrs_laea` (a name, not an EPSG code) |
| Combined Drought Indicator | 4326 — WGS 84 | decimal degree | Global attribute `11_DATA_SRS`, plus a `spatial_ref` variable carrying `crs_wkt` and `GeoTransform` |
| Géorisques WFS endpoint | 4326 | decimal degree | `WFS SSC.txt`, `srsname='EPSG:4326'` — the service frame, not the delivered frame |

Three cosmetic `.prj` variants all resolve to EPSG:2154 and will defeat naive string comparison of projection files:

| **Variant** | **Appears in** |
| --- | --- |
| `PROJCS["RGF93 v1 / Lambert-93"…]`, standard parallels 49 then 44 | National GeoPackage, `AleaRG_2025`, `BourgogneFrancheComté`, `Masque_BDLISA_IMPERMEABLE` |
| `PROJCS["RGF93 Lambert 93"…]`, standard parallels 44 then 49 | `AleaRG64_L93`, all six geology layers |
| `PROJCS["RGF_1993_Lambert_Conformal_Conic"…]`, datum named `D_Reseau_Geodesique_Francais_1993_v1` | `Re_Nappe_fr` |

*Table 3.2 — Collection. Character encoding of every attribute table.*

| **Entity** | **Declared by** | **Encoding** |
| --- | --- | --- |
| `AleaRG_2025_Fxx_L93`, `AleaRG64_L93`, `BourgogneFrancheComté` | `.cpg` sidecar | UTF-8 |
| `Re_Nappe_fr`, `Masque_BDLISA_IMPERMEABLE` | `.cpg` sidecar | ISO-8859-1 |
| All six `GEO050K_HARM_064_*` layers | **No `.cpg`**; dBASE language-driver byte at offset 29 = 0x57 | Windows-1252 (ANSI) |
| Both GeoPackages | SQLite | UTF-8 by specification |
| `AleaRG64_L93.csv` | `.cpg` of the source shapefile | UTF-8 |

The geology layers are the trap: they carry accented French text in `DESCR` with no `.cpg`, so a reader that defaults to UTF-8 will mis-decode. Read them as Windows-1252 per the language-driver byte. Note separately that `NOTATION` in `S_FGEOL` is **not** text — it holds codepoints for the BRGM symbol fonts in `OUTILS\Point_Symbols\`, so values such as `ìò` are correct as stored and must not be "repaired".

## 4. Clay Shrink–Swell Hazard Entities

The hazard is delivered in two vintages that share a semantic model but not a schema. Neither carries a date field; the vintages are distinguished by file, not by attribute.

### 4.1 `risk_of_shrinkage_and_swelling_of_clays` — national, 2019

GeoPackage table, `fid` INTEGER PRIMARY KEY, geometry column `geom`, MultiPolygon, EPSG:2154. A QGIS metadata document is attached in `gpkg_metadata` (`GPKG_METADATA_ITEM_1`) with an empty title, abstract and contact, and a zeroed extent — it carries the CRS definition and nothing else of use.

*Table 4.1 — France. National. RGA 2019. Attribute schema.*

| **Field** | **Storage** | **Logical** | **Semantics** | **Domain** | **Null** |
| --- | --- | --- | --- | --- | --- |
| `fid` | Integer, PK | identifier | GeoPackage row id | 122 222 rows | no |
| `geom` | MultiPolygon | geometry | Hazard zone footprint | — | no |
| `DPT` | String (2.0) | code | INSEE département code | 95 distinct values, all exactly 2 characters | no |
| `NIVEAU` | Real (0.0) | ordinal integer | Hazard level, numeric | 1, 2, 3 | no |
| `ALEA` | String (6.0) | ordinal label | Hazard level, French label | `Faible`, `Moyen`, `Fort` | no |

`NIVEAU` and `ALEA` are a strict 1:1 redundant pair (Section 9.1). Class counts: `Faible`/1 = 60 149 features across 84 départements; `Moyen`/2 = 50 377 across 94; `Fort`/3 = 11 696 across 71. Extent 99 704.8 – 1 242 436.0 m E, 6 049 646.0 – 7 109 081.3 m N.

Note that `NIVEAU` is stored as a GeoPackage `REAL` although it holds only the integers 1–3. Cast on read; do not equality-test it as a float.

### 4.2 `AleaRG_2025_Fxx_L93` — national, 2025 — **attributes only, no geometry**

A shapefile delivered as `.dbf` + `.shx` + `.prj` + `.cpg`. **The `.shp` geometry file is absent from both archive parts.** The `.shx` index is intact and internally consistent — (971 292 − 100) ÷ 8 = 121 399 records, matching the `.dbf` — so what is lost is the geometry body alone, not the record set. This entity is usable as a statistical table and not as a map layer.

*Table 4.2 — France. National. RGA 2025. Attribute schema.*

| **Field** | **Storage** | **Logical** | **Semantics** | **Domain** | **Null** |
| --- | --- | --- | --- | --- | --- |
| `gid` | Integer64 (18.0) | identifier | Source-system row id | 1 … 121 399, unique and dense — a usable primary key | no |
| `insee_dep` | String (80.0) | code | INSEE département code | all values exactly 2 characters | no |
| `niveau` | Real (24.15) | ordinal integer | Hazard level | 1, 2, 3 | no |
| `surf_m2` | Real (24.15) | measure | Polygon area, m² | 0 … 5.588 × 10⁹; sums to 395 455 km² | no |

Class counts: `niveau` 1 = 35 091 records across 74 départements; 2 = 64 039 across 94; 3 = 22 269 across 83.

Three schema-level differences from the 2019 vintage, all of which must be handled explicitly rather than assumed away:

1. **Field names are lower-case** (`niveau`, not `NIVEAU`) and the département field is renamed `insee_dep`.
2. **There is no `ALEA` label field.** The 1–3 → Faible/Moyen/Fort mapping must be applied from Section 9.1.
3. **The class balance is inverted.** In 2019 `Faible` is the largest class (49.2 % of features); in 2025 `Moyen` is (52.8 %). The two vintages are not interchangeable, and a difference between them is not a change map.

`surf_m2` needs a validity rule: 4 374 records (3.6 %) carry an area below 1 m², i.e. sliver polygons whose attributes are real but whose geometry is negligible. The total, 395 455 km², is 71.7 % of metropolitan France — consistent with a hazard map that zones only clay-bearing terrain, not the whole country.

### 4.3 `AleaRG64_L93` — département 64 extract, 2019

Shapefile, Polygon, EPSG:2154, with a `.qix` spatial index. Extent 311 933.1 – 459 086.0 m E, 6 192 984.0 – 6 283 556.0 m N.

*Table 4.3 — France. Dept 64. RGA extract. Attribute schema.*

| **Field** | **Storage** | **Logical** | **Semantics** | **Domain** | **Null** |
| --- | --- | --- | --- | --- | --- |
| `dpt` | String (2.0) | code | INSEE département code | `64` only — constant | no |
| `niveau` | Integer64 (10.0) | ordinal integer | Hazard level | 1, 2, 3 | no |
| `alea` | String (80.0) | ordinal label | Hazard level, French label | `Faible`, `Moyen`, `Fort` | no |

Class counts: `Faible` 2 284, `Moyen` 703, `Fort` 72. This is the 2019 model with lower-cased names and a widened `alea` field (80 rather than 6 characters). It is a subset of the national 2019 GeoPackage by content but not a byte-level extract of it — the geometry type differs (Polygon here, MultiPolygon nationally).

### 4.4 `AleaRG64_L93.csv` — WKT export of 4.3

A four-column CSV, UTF-8, comma-separated, double-quote quoting, header row present, 3 059 data rows.

*Table 4.4 — France. Dept 64. RGA extract, CSV form. Column schema.*

| **Position** | **Column** | **Logical** | **Notes** |
| --- | --- | --- | --- |
| 1 | `dpt` | code | As 4.3 |
| 2 | `niveau` | ordinal integer | As 4.3 |
| 3 | `alea` | ordinal label | As 4.3 |
| 4 | `geometry` | WKT | `POLYGON ((x y, …))`, EPSG:2154 metres, ~9 decimal places |

No `.csvt` type sidecar is supplied, so a naive reader infers all four columns as text. The CRS is implicit — it appears nowhere in the CSV and must be taken from the companion `.prj`.

### 4.5 Vintage comparison

*Table 4.5 — France. National. The two RGA vintages side by side.*

| **Aspect** | **2019 (GeoPackage)** | **2025 (`.dbf` only)** |
| --- | --- | --- |
| Features | 122 222 | 121 399 |
| Geometry | MultiPolygon, present | **absent** |
| Département field | `DPT` String(2) | `insee_dep` String(80), 2 chars used |
| Level field | `NIVEAU` Real + `ALEA` String | `niveau` Real, no label field |
| Area field | none | `surf_m2` Real |
| Row identifier | `fid` (GeoPackage) | `gid` (source system) |
| Départements present | 95 | 95 |

### 4.6 `WFS SSC.txt` — service pointer, not data

A saved QGIS connection string, schematised here because it declares a third representation of the same hazard:

| **Key** | **Value** |
| --- | --- |
| URL | `https://georisques.gouv.fr/services` |
| Type name | `ms:ALEARG_REALISE` |
| Geometry | Polygon |
| SRS name | `EPSG:4326` — **not** the 2154 of the files |
| Declared feature count | 80 000 |
| Declared extent | −12.372414, 41.150000 : 10.380000, 51.774757 |

The 80 000 features advertised by the service reconcile with neither the 122 222 of 2019 nor the 121 399 of 2025. Treat the WFS as a fourth, independently versioned source.

## 5. Harmonised Geology — Département 64

Six layers from the BRGM harmonised 1:50 000 geological map, all EPSG:2154, all Windows-1252, all sharing a common core of five fields plus a per-geometry-type symbology block. `CARTE` = 1499 in every record of every layer: this is a single harmonised département sheet, not a mosaic of map sheets.

### 5.1 Core fields, common to all six layers

*Table 5.1 — France. Dept 64. Geology. Fields present in every layer.*

| **Field** | **Storage** | **Logical** | **Semantics** | **Null** |
| --- | --- | --- | --- | --- |
| `MI_PRINX` | Real (24.15), or Integer64 (11.0) in `P_DIVERS` | identifier | MapInfo primary index, carried through the conversion. Unique within each layer — verified 13 037 distinct over 13 037 rows in `S_FGEOL`. Use as the layer primary key | no |
| `CARTE` | Integer (4.0) | code | Harmonised map identifier | no |
| `CODE` | Integer (4.0) | code | Object type within the layer's own code list (Section 9.3) | no |
| `DESCR` | String (254.0), 250 in `L_STRUCT` | text | French description; strictly 1:1 with `CODE` in all five line and point layers | no |
| `NOM_SYMB` | String (28.0), 10 in the two point layers | code | Symbol name in the BRGM symbology toolkit | no |

Colour is stored as separated CMYK components, never as a hex or RGB triple. Two families exist and they are not interchangeable:

| **Field group** | **Storage** | **Meaning** |
| --- | --- | --- |
| `C_SYMB`, `M_SYMB`, `J_SYMB`, `N_SYMB` | Integer (3.0), or Integer64 (10.0) in `S_FGEOL` | Symbol/stroke colour — Cyan, Magenta, Jaune (yellow), Noir (black) |
| `C_FOND`, `M_FOND`, `J_FOND`, `N_FOND` | Integer64 (10.0) | Polygon fill colour — `S_FGEOL` only |

`ARCGIS_COD` String (12.0 in `L_FGEOL`, 13.0 elsewhere) carries an ArcGIS symbol reference and is present in all layers except `S_FGEOL`.

### 5.2 `GEO050K_HARM_064_S_FGEOL_2154` — formation polygons

The substrate layer, and the one that matters for shrink–swell. 13 037 polygons, extent 311 998.3 – 459 087.4 m E, 6 192 987.4 – 6 283 558.2 m N.

*Table 5.2 — France. Dept 64. Geology. `S_FGEOL` fields beyond the common core.*

| **Field** | **Storage** | **Logical** | **Semantics** | **Domain** | **Null** |
| --- | --- | --- | --- | --- | --- |
| `CODE_LEG` | Integer (4.0) | code | Legend entry id | 350 distinct; equal to `CODE` in every record | no |
| `NOTATION` | String (50.0) | symbol code | Stratigraphic notation (`Fz`, `n1-c1`, …) rendered with the BRGM fonts; **not plain text** | 350 distinct, 1:1 with `CODE` | no |
| `C_FOND` / `M_FOND` / `J_FOND` / `N_FOND` | Integer64 (10.0) | CMYK component | Fill colour | — | no |
| `ROT_SYMB` | Integer (3.0) | degrees | Symbol rotation | — | no |

The formation vocabulary is 350 units, with `CODE`, `CODE_LEG`, `NOTATION` and `DESCR` all in strict 1:1 correspondence — verified: zero `CODE` values map to more than one `NOTATION`. `CODE` is therefore the join key to any external lithology-to-shrink-swell lookup, and the other three are labels for it. The list is too long to enumerate here; extract it with `SELECT DISTINCT CODE, NOTATION, DESCR`.

### 5.3 Line layers — `L_FGEOL`, `L_STRUCT`, `L_DIVERS`

Common core plus `WT_SYMB` Real (5.2) — line weight — and the four `*_SYMB` colour components. `L_DIVERS` adds `ATTR` String (100.0); `L_FGEOL` and `L_STRUCT` have no `ATTR`.

Each line layer has a small closed `CODE` domain, fully enumerated in Section 9.3.

### 5.4 Point layers — `P_STRUCT`, `P_DIVERS`

Common core plus:

| **Field** | **Storage** | **Layer** | **Semantics** |
| --- | --- | --- | --- |
| `AZIMUT` | Integer (3.0) in `P_DIVERS`, Integer64 (10.0) in `P_STRUCT` | both | Azimuth of the measured plane or lineation, degrees |
| `PENDAGE` | Integer64 (10.0) | `P_STRUCT` only | Dip angle, degrees |
| `ATTR` | String (254.0) | both | Free-text attribute |
| `ROT_SYMB`, `TAIL_SYMB` | Integer (3.0) | both | Symbol rotation and size |

`P_STRUCT` is the structural-measurement layer (bedding, schistosity, fold axes, lineations); `P_DIVERS` records quarries, springs, fossil sites and mineral occurrences.

## 6. Soil Properties — European Soil Database Derived Layers

Twenty Idrisi raster layers on one shared grid. Each layer is a headerless binary body (`.rst`) plus a plain-text documentation file (`.RDC`). **The `.RDC` is the schema — there is no other**, and no `.rst` can be read without it.

### 6.1 `.RDC` header schema

A key-value text file, one pair per line, key left-padded to 12 characters, separator `: `. Keys appear in fixed order; `code N` and `lineage` repeat.

*Table 6.1 — Europe. ESDB. `.RDC` keys and their role.*

| **Key** | **Type** | **Role** | **Value across the 20 layers** |
| --- | --- | --- | --- |
| `file format` | text | Format tag | `IDRISI Raster A.1` — constant |
| `file title` | text | Human-readable layer name | per layer, Table 6.3 |
| `data type` | enum | Cell encoding | `real` (32-bit IEEE float LE) or `byte` (8-bit unsigned) |
| `file type` | enum | Body layout | `binary` — constant |
| `columns` / `rows` | integer | Grid size | 5900 / 4600 — constant |
| `ref. system` | text | CRS name | `_etrs_laea` — constant; **not an EPSG code** |
| `ref. units` / `unit dist.` | text / real | Linear unit | `m` / `1.0` — constant |
| `min. X` `max. X` `min. Y` `max. Y` | real | Bounding box, metres | 1 500 000 / 7 400 000 / 900 000 / 5 500 000 — constant |
| `resolution` | integer | Cell size, metres | `1000` — constant |
| `min. value` / `max. value` | numeric | Declared value range | per layer, Table 6.3 |
| `display min` / `display max` | numeric | Rendering stretch | equal to min/max value in every layer |
| `value units` | text | Physical unit | `percent`, `milimeter` *(sic)*, `g / cm^3`, `centimeter`, `unspecified` |
| `pos'n error` / `value error` | text | Accuracy | `unknown` — constant |
| `flag value` / `flag def'n` | text | No-data declaration | **`none` in all twenty** — see 6.5 |
| `legend cats` | integer | Number of class entries | `6` for the two texture layers, `0` for the other eighteen |
| `code N` | text | Class definition, one line per class | texture layers only, Table 6.4 |
| `lineage` | text ×4 | Producer attribution | JRC Ispra, constant across all layers |

### 6.2 Grid definition

*Table 6.2 — Europe. ESDB. The grid, common to all twenty layers.*

| **Property** | **Value** |
| --- | --- |
| CRS | ETRS89-LAEA, EPSG:3035 (declared only as the name `_etrs_laea`) |
| Origin (upper-left) | X 1 500 000 m, Y 5 500 000 m |
| Cell size | 1 000 × 1 000 m |
| Columns × rows | 5 900 × 4 600 = 27 140 000 cells |
| Extent | X 1 500 000 – 7 400 000 m; Y 900 000 – 5 500 000 m |
| Row order | Top-left origin, row-major, no padding or row headers |
| Body size, `byte` layers | 27 140 000 bytes = rows × columns × 1 |
| Body size, `real` layers | 108 560 000 bytes = rows × columns × 4 |
| Rotation | 0 |

Body sizes match the declared grid exactly for all thirteen bodies present in part 001, which confirms the row order and the absence of padding.

### 6.3 Layer catalogue

Naming grammar: `<unit>_EU_<horizon>_<property>` — `STU` = Soil Typological Unit, `SMU` = Soil Mapping Unit; `T` = topsoil, `S` = subsoil.

*Table 6.3 — Europe. ESDB. The twenty layers as declared in their headers. "Part" is the archive part carrying the `.rst` body.*

| **Layer** | **`file title`** | **Type** | **Units** | **Declared range** | **Cats** | **Part** |
| --- | --- | --- | --- | --- | --- | --- |
| `STU_EU_T_CLAY` | Clay content, topsoil | real | percent | 0 – 76.0 | 0 | 001 |
| `STU_EU_S_CLAY` | Clay content, subsoil | real | percent | 0 – 73.0 | 0 | **002** |
| `STU_EU_T_SAND` | Sand content, topsoil | real | percent | 0 – 90.0 | 0 | **002** |
| `STU_EU_S_SAND` | Sand content, subsoil | real | percent | 0 – 90.0 | 0 | **002** |
| `STU_EU_T_SILT` | Silt content, topsoil | real | percent | 0 – 69.0 | 0 | 001 |
| `STU_EU_S_SILT` | Silt content, subsoil | real | percent | 0 – 71.0 | 0 | 001 |
| `STU_EU_T_GRAVEL` | Gravel content, topsoil | byte | percent | 0 – 48 | 0 | 001 |
| `STU_EU_S_GRAVEL` | Gravel content, subsoil | byte | percent | 0 – 41 | 0 | 001 |
| `STU_EU_T_BD` | Bulk density, topsoil | real | g / cm^3 | 0 – 1.78 | 0 | **002** |
| `STU_EU_S_BD` | Bulk density, subsoil | real | g / cm^3 | 0 – 1.74 | 0 | **002** |
| `STU_EU_T_OC` | Organic carbon content, topsoil | real | percent | 0 – 39.4000015 | 0 | **002** |
| `STU_EU_S_OC` | Organic carbon content, subsoil | real | percent | 0 – 39.1599998 | 0 | 001 |
| `STU_EU_T_TEXT_CLS` | Texture class, topsoil | byte | unspecified | 0 – 8 | 6 | 001 |
| `STU_EU_S_TEXT_CLS` | Texture class, subsoil | byte | unspecified | 0 – 8 | 6 | 001 |
| `STU_EU_T_TAWC` | PTF Total available water content, topsoil | real | milimeter | 0 – 118.5718536 | 0 | 001 |
| `STU_EU_S_TAWC` | PTF Total available water content, subsoil, max. 100cm depth | real | milimeter | 0 – 276.6676636 | 0 | 001 |
| `SMU_EU_T_TAWC` | PTR Total available water content, topsoil | real | milimeter | 0 – 118.5718536 | 0 | **002** |
| `SMU_EU_S_TAWC` | PTR Total available water content, subsoil, max. 100cm depth | real | milimeter | 0 – 276.6676636 | 0 | 001 |
| `STU_EU_DEPTH_ROOTS` | Depth available to roots | byte | centimeter | 0 – 150 | 0 | 001 |
| `STU_EU_ALLOCATION` | Area of STU allocation | byte | unspecified | 0 – 1 | 0 | 001 |

Two entries in this table are not soil properties and must not be modelled as such:

- **`STU_EU_ALLOCATION`** is a provenance flag. Value 1 marks cells where a specific typological unit could be allocated within its mapping unit; elsewhere the property values carry mapping-unit support, coarser than the 1 km cell implies.
- **The `STU`/`SMU` TAWC pair** are two independent estimates of the same quantity — pedo-transfer *functions* (`STU_*`, header title `PTF`) versus pedo-transfer *rules* (`SMU_*`, `PTR`). Their declared ranges are identical and their bodies are not. They are alternatives, not duplicates, and must not be substituted for one another.

### 6.4 Texture class code list

`legend cats : 6`, but the codes are not contiguous — 6 and 7 are unused, 8 is out of sequence. The class definition strings are **truncated in the header at 50 characters**; code 2 in particular is cut mid-expression and its printed text is incomplete.

*Table 6.4 — Europe. ESDB. Texture class codes, verbatim from `STU_EU_*_TEXT_CLS.RDC`.*

| **Code** | **Definition as printed** |
| --- | --- |
| 1 | `Coarse (18% < clay and > 65% sand)` |
| 2 | `Medium (18% < clay < 35% and >= 15% sand, or 18% <` — truncated |
| 3 | `Medium fine (< 35% clay and < 15% sand)` |
| 4 | `Fine (35% < clay < 60%)` |
| 5 | `Very fine (clay > 60 %)` |
| 8 | `No mineral texture (Peat soils)` |
| 0 | Not in the legend — see 6.5 |

Codes 4, 5 and the upper part of 2 are the shrink–swell-relevant classes.

### 6.5 No-data — undeclared

Every one of the twenty headers declares `flag value : none` and `flag def'n : none`. **No no-data value is defined anywhere in the product**, yet roughly half of each grid holds exactly 0, representing sea, non-European land inside the LAEA window, and unmapped areas.

The schema-level rule for a loader:

- Treat **0 as no-data** on every layer. It is safe on physical grounds — a clay content of 0 %, a bulk density of 0 g/cm³ and a rooting depth of 0 cm are all implausible, and the lowest observed non-zero values sit well clear of zero.
- **Do not compute statistics over the full grid.** A naive mean of topsoil clay content across all 27 140 000 cells understates it by more than half.
- **Do not assume two layers share a data mask.** Topsoil layers hold data on ~49.3 % of cells and subsoil layers on ~43.4–43.8 %. Any cell-by-cell arithmetic between layers is defined on an intersection that has to be computed, not assumed.

## 7. Water-Table Rise Sensitivity

Three areal deliveries of the same product in two formats, plus a mask. All EPSG:2154. The three areal layers share an identical six-field schema and an identical code list, which makes them safely concatenable.

*Table 7.1 — France. Water-table sensitivity. Attribute schema, common to all three deliveries.*

| **Field** | **Storage** | **Logical** | **Semantics** | **Null** |
| --- | --- | --- | --- | --- |
| `CLASSE` | String (80.0) | nominal label | Sensitivity class, French | no |
| `gridcode` | Integer64 (18.0) in shapefiles, (0.0) in the GeoPackage | code | Numeric form of `CLASSE`, 1:1 | no |
| `FIAB_MNT` | String (80.0) | ordinal label | Reliability of the digital terrain model input | no |
| `FIAB_ESO` | String (80.0) | ordinal label | Reliability of the groundwater (eaux souterraines) input | no |
| `FIAB_TOT` | String (80.0) | ordinal label | Combined reliability | no |
| `CLASSEFIAB` | String (80.0) | composite label | `CLASSE` + `, Fiabilité ` + `FIAB_TOT`, concatenated | no |

*Table 7.2 — France. Water-table sensitivity. The three deliveries and their class distribution.*

| **Delivery** | **Format** | **Features** | **gridcode 0** | **gridcode 1** | **gridcode 2** |
| --- | --- | --- | --- | --- | --- |
| `Re_Nappe_fr` (dept 64) | Shapefile, ISO-8859-1 | 5 859 | 82 | 2 145 | 3 632 |
| `re_nappe_fr` (Yonne 89) | GeoPackage | 9 376 | 2 041 | 1 979 | 5 356 |
| `BourgogneFrancheComté` | Shapefile, UTF-8 | 56 351 | 6 420 | 16 177 | 33 754 |

The `CLASSE` code list (Section 9.2) is identical across all three, as is the `gridcode` mapping. The Yonne GeoPackage falls inside the Bourgogne-Franche-Comté extent, but is a separate delivery with its own feature set — the counts do not nest.

`CLASSEFIAB` is a derived field: it is exactly `CLASSE || ', Fiabilité ' || FIAB_TOT`. Its domain is therefore the 12 combinations observed in dept 64 (3 classes × 4 reliability levels). Store it as derived, not as an independent attribute.

*Table 7.3 — France. Dept 64. Reliability field domains, measured on `Re_Nappe_fr`.*

| **Field** | **FORTE** | **MOYENNE** | **FAIBLE** | **INCONNUE** |
| --- | --- | --- | --- | --- |
| `FIAB_MNT` | 1 267 | — | 4 552 | 40 |
| `FIAB_ESO` | 517 | 2 457 | 2 885 | — |
| `FIAB_TOT` | 258 | 724 | 4 837 | 40 |

The ordinal scale is `FORTE` > `MOYENNE` > `FAIBLE`, with `INCONNUE` as an unranked unknown. Note that the domains are not uniform: `FIAB_MNT` never takes `MOYENNE` and `FIAB_ESO` never takes `INCONNUE`. A schema that declares one shared enumeration across the three reliability fields is correct only if it permits all four values in each.

### 7.4 `Masque_BDLISA_IMPERMEABLE`

A single-feature mask polygon delimiting impermeable outcrops in dept 64.

| **Field** | **Storage** | **Logical** | **Domain** |
| --- | --- | --- | --- |
| `Id` | Integer64 (18.0) | identifier | single record |

Extent 320 428.6 – 455 592.3 m E, 6 193 377.7 – 6 282 886.0 m N — inset from the dept-64 hazard and geology extents, as expected of a mask over a subset of the département.

## 8. Combined Drought Indicator — NetCDF

`cdinx_m_edo_20250101_20251011_t.nc`, NetCDF-4 (HDF5), one data variable on a four-dimensional grid. This is the only entity in the collection with a time axis.

### 8.1 Dimensions and variables

*Table 8.1 — Europe. CDI. Dimensions.*

| **Dimension** | **Size** | **Unlimited** |
| --- | --- | --- |
| `time` | 29 | no |
| `band` | 1 | no |
| `lat` | 1 200 | no |
| `lon` | 1 824 | no |

*Table 8.2 — Europe. CDI. Variables.*

| **Variable** | **dtype** | **Dimensions** | **Attributes** |
| --- | --- | --- | --- |
| `cdinx` | uint8 | `(time, band, lat, lon)` | `_FillValue = 8`, `add_offset = 0.0`, `scale_factor = 1.0`, `grid_mapping = 'spatial_ref'` |
| `time` | int64 | `(time,)` | `units = 'days since 2025-01-01 00:00:00'`, `calendar = 'proleptic_gregorian'` |
| `band` | int64 | `(band,)` | — |
| `lat` | float64 | `(lat,)` | `_FillValue = NaN` |
| `lon` | float64 | `(lon,)` | `_FillValue = NaN` |
| `spatial_ref` | int64, scalar | — | `crs_wkt` (WGS 84), `GeoTransform`, and the full CF grid-mapping attribute set |

The `band` dimension is degenerate (length 1) and carries no semantics; index it away on read. `scale_factor` 1.0 and `add_offset` 0.0 mean the packing attributes are present but inert — the stored uint8 values are the class codes directly.

### 8.2 Grid and time axis

| **Property** | **Value** |
| --- | --- |
| CRS | EPSG:4326, WGS 84 geographic |
| GeoTransform | `-25.0  0.0416666667  0.0  72.0  0.0  -0.0416666667` |
| Cell size | 1/24° = 0.041666667° |
| `lon` (cell centres) | −24.979167 … 50.979167, ascending |
| `lat` (cell centres) | 71.979167 … 22.020833, **descending** |
| Extent (declared, `14_EXTENT_WKT`) | `POLYGON ((-25 22, 51 22, 51 72, -25 72, -25 22))` |
| Time origin | 2025-01-01 |
| Time offsets, days | 0, 10, 20, 31, 41, 51, 59, 69, 79, 90, 100, 110, 120, 130, 140, 151, 161, 171, 181, 191, 201, 212, 222, 232, 243, 253, 263, 273, 283 |
| Cadence | Dekadal — the 1st, 11th and 21st of each month; step lengths 8, 10 or 11 days |
| Time extent | 2025-01-01 to 2025-10-11, 29 steps |

The dekadal cadence means the time axis is **not evenly spaced**. Any resampling, differencing or rolling window must use the actual `time` values, never the index.

### 8.3 Value code list and observed distribution

The classes come from the global attribute `08_VALUES_LEGEND`. Counts are over all 63 475 200 cells (29 steps × 2 188 800 cells).

*Table 8.3 — Europe. CDI. Class codes.*

| **Code** | **Definition** | **Cells** | **Share** |
| --- | --- | --- | --- |
| 0 | No drought | 11 098 502 | 17.48 % |
| 1 | Watch. Precipitation deficit | 1 975 272 | 3.11 % |
| 2 | Warning. Soil moisture deficit following precipitation deficit | 8 381 613 | 13.20 % |
| 3 | Alert. Vegetation stress following precipitation / soil moisture deficit | 1 081 057 | 1.70 % |
| 4 | Recovery to normal condition | 1 026 815 | 1.62 % |
| 5 | Temporary soil moisture recovery | 406 558 | 0.64 % |
| 6 | Temporary vegetation recovery | 172 596 | 0.27 % |
| 7 | No data inside LISFLOOD domain | 0 | 0 % |
| 8 | Outside LISFLOOD domain | 39 332 787 | 61.96 % |

Two modelling consequences:

- **The code list is nominal, not ordinal.** Codes 0–3 do form an escalating severity sequence, but 4, 5 and 6 are recovery states and 7, 8 are absence of data. Do not average, interpolate or threshold `cdinx` as if it were a numeric scale.
- **Code 8 is `_FillValue`** and covers 62 % of the cube — sea and land outside the LISFLOOD hydrological domain. Code 7 is declared but never occurs in this file; a loader should still accept it.

### 8.4 Provenance attributes

The file carries 38 numbered global attributes plus a CF/GDAL set. Those a catalogue should ingest:

| **Attribute** | **Value** |
| --- | --- |
| `01_PROD_CODE` | `cdinx` |
| `04_TITLE` | Combined Drought Indicator (CDI) |
| `07_DATA_UOM` | `dimensionless` |
| `09_LICENSE` | Creative Commons Attribution v4 |
| `11_DATA_SRS` / `12_RESOLUTION` | `EPSG:4326` / 0.04166666667 decimal degree |
| `16_START_DAY` | 2012-01-01 — the product archive start, **not** this file's coverage |
| `20_REF_PUBLICATION_URL` | `https://doi.org/10.5194/nhess-21-481-2021` |
| `23_JRC_DATA_ID` | `afa8a5ee-5473-439a-b062-ffdaedc38b2d` |
| `28_PRODUCT_VERSION` | 4.0.0 |
| `31_UPDATE_STATUS` | `final` |
| `35_START_PERIOD` / `36_END_PERIOD` | 2025-01-01 / 2025-01-01 — describe the **first slice only**, not the 29-step cube |

`16_START_DAY`, `35_START_PERIOD` and `36_END_PERIOD` are inherited from the single-slice source product and do not describe this aggregated file. The authoritative temporal extent is the `time` variable.

`DATASET_README` carries the producer's warning that files whose names end `pNN` are provisional. This file's name ends `_t`, so it is not provisional.

## 9. Consolidated Code Lists

### 9.1 Clay shrink–swell hazard level

Applies to `NIVEAU`/`ALEA` (2019 GeoPackage), `niveau`/`alea` (dept 64) and `niveau` (2025, unlabelled).

| **Numeric** | **French label** | **English** |
| --- | --- | --- |
| 1 | `Faible` | Low |
| 2 | `Moyen` | Medium |
| 3 | `Fort` | High |

No zero or "none" class exists: absence of a polygon means the terrain is not zoned, which is not the same as zero hazard.

### 9.2 Water-table rise sensitivity

| **`gridcode`** | **`CLASSE`** | **English** |
| --- | --- | --- |
| 0 | `Pas de débordement de nappe ni d'inondation de cave` | Neither water-table overflow nor cellar flooding |
| 1 | `Zones potentiellement sujettes aux débordements de nappe` | Potentially subject to water-table overflow |
| 2 | `Zones potentiellement sujettes aux inondations de cave` | Potentially subject to cellar flooding |

### 9.3 Geology object codes

Closed `CODE` domains for the line and point layers, each in strict 1:1 correspondence with `DESCR`. `S_FGEOL` has an open 350-value formation vocabulary and is not enumerated here.

*`L_FGEOL` — 8 codes, 38 858 features*

| **Code** | **`DESCR`** | **n** |
| --- | --- | --- |
| 1 | Limite de la carte ou du projet | 1 266 |
| 3 | Limite réseau hydrographique (lacs, canaux ou rivières délimitant une zone) | 1 473 |
| 10 | Limite interprétée sur la carte (arrêts couleur) | 114 |
| 11 | Contour géologique observé, visible | 28 766 |
| 12 | Contour géologique supposé, probable, masqué | 355 |
| 20 | Elément linéaire structural (contour géologique superposé à une faille) | 6 435 |
| 21 | Elément linéaire divers (contour géologique superposé à un élément linéaire divers) | 429 |
| 26 | Limite de faciès, limite de biozone, passage progressif entre deux faciès | 20 |

*`L_STRUCT` — 4 codes, 5 719 features*

| **Code** | **`DESCR`** | **n** |
| --- | --- | --- |
| 1 | Faille observée, visible, de cinématique non précisée | 4 350 |
| 2 | Faille supposée, masquée, hypothétique, de cinématique non précisée | 781 |
| 3 | Chevauchement, base de nappe ou faille inverse visible | 510 |
| 4 | Chevauchement, base de nappe ou faille inverse supposée | 78 |

*`L_DIVERS` — 4 codes, 653 features*

| **Code** | **`DESCR`** | **n** |
| --- | --- | --- |
| 5 | Limite d'extension d'exploitation, limite d'extension de corps minéralisé | 2 |
| 7 | Niveau-repère lithologique | 27 |
| 10 | Direction de l'écoulement par gravité dans cône de déjection | 536 |
| 47 | Arc et cordon morainique | 88 |

*`P_STRUCT` — 15 codes, 5 927 features.* Bedding (`So`) with dip dominates at 4 077 features (code 1); the remainder are horizontal, vertical and inverted bedding (2, 3, 4), schistosity and foliation (5, 7, 19), fold axes (8, 9, 20, 22), lineations (32, 33, 34) and fracture planes (13).

*`P_DIVERS` — 16 codes, 412 features.* Open-cast quarries dominate at 170 (code 4), followed by fossil sites (1) and point lithological indications (33); the remainder are mineral occurrences, springs, underground workings and absolute datings.

## 10. Keys, Joins and Cross-Entity Relationships

### 10.1 Within-entity keys

| **Entity** | **Primary key** | **Verified** |
| --- | --- | --- |
| National RGA 2019 | `fid` | GeoPackage INTEGER PRIMARY KEY |
| National RGA 2025 | `gid` | 121 399 distinct over 121 399 rows, dense 1…121 399 |
| Geology, all six layers | `MI_PRINX` | 13 037 distinct over 13 037 rows in `S_FGEOL` |
| Dept-64 RGA, water-table layers, mask | none | No candidate key field; use the shapefile record number |

### 10.2 Attribute joins

Only a handful of attribute joins exist, all internal to a single theme:

| **From** | **To** | **Key** | **Cardinality** |
| --- | --- | --- | --- |
| Any RGA entity | Hazard code list (9.1) | `NIVEAU` / `niveau` | many-to-one |
| `S_FGEOL` | Formation vocabulary | `CODE` = `CODE_LEG` → `NOTATION`, `DESCR` | many-to-one, 350 units |
| Geology line/point layers | Object code lists (9.3) | `CODE` → `DESCR` | many-to-one |
| Water-table layers | Sensitivity code list (9.2) | `gridcode` | many-to-one |
| Any French layer | Département | `DPT` / `dpt` / `insee_dep` (INSEE code) | many-to-one — but no département table is delivered |

### 10.3 Spatial joins, and what they cost

There is no key that joins one theme to another. Every cross-theme relationship is spatial, and only one group shares a frame without transformation:

| **Relationship** | **Transformation required** | **Note** |
| --- | --- | --- |
| RGA 2019 ↔ geology ↔ water table ↔ mask, all within dept 64 | **none** — all EPSG:2154 | The four dept-64 extents agree to ~250 m in X and ~100 m in Y; they are cut to the same footprint and overlay directly |
| Any French layer ↔ ESDB soil rasters | EPSG:2154 → EPSG:3035 | Not supplied |
| Any French layer ↔ CDI | EPSG:2154 → EPSG:4326 | Not supplied |
| ESDB soil rasters ↔ CDI | EPSG:3035 → EPSG:4326, plus resampling 1 km ↔ 1/24° | Not supplied; the two grids are unrelated |
| RGA 2025 ↔ anything spatial | **impossible** | Geometry absent; joins only through `insee_dep` |

Support differs by a factor of tens between themes — RGA polygons are irregular zones, the geological map is 1:50 000, the soil rasters are 1 km cells with mapping-unit support outside the allocation mask, and the CDI is ~4 km. Any cell-by-cell combination of hazard, soil and trigger is a change of support and needs an explicit rule, not a resample.

### 10.4 Theme roles in a shrink–swell assessment

| **Theme** | **Role** | **Time-varying** |
| --- | --- | --- |
| RGA hazard (2019, 2025, WFS) | Predisposing factor — the answer, where France already publishes it | no |
| Harmonised geology | Substrate — explains the hazard, and is the generalisation basis outside mapped zones | no |
| ESDB soil layers | Predisposing factor, continental — clay content and texture class govern shrink–swell potential | no |
| CDI | Triggering factor — the meteorological driver | **yes**, dekadal |
| Water-table rise | Companion hazard — separate phenomenon, shares the dept-64 footprint | no |

## 11. Validation Rules for a Loader

Rules a schema-conformant reader should enforce, each traceable to a measured property above:

1. Read the six `GEO050K_HARM_064_*` `.dbf` files as **Windows-1252**, not UTF-8; `Re_Nappe_fr` and `Masque_BDLISA_IMPERMEABLE` as **ISO-8859-1**; everything else as UTF-8 (§3.2).
2. Do not treat `NOTATION` in `S_FGEOL` as text — it is symbol-font codepoints (§5.2).
3. Cast `NIVEAU` (GeoPackage `REAL`) and `niveau` (dBASE `Real(24.15)`) to integer before comparison (§4.1, §4.2).
4. Reject `AleaRG_2025_Fxx_L93` from any spatial pipeline: `.shp` is absent (§4.2).
5. Flag `surf_m2 < 1` records — 4 374 of them — as sliver geometry (§4.2).
6. Apply the 1–3 → Faible/Moyen/Fort mapping to the 2025 vintage, which ships no label field (§4.2, §9.1).
7. Treat raster value **0 as no-data** on all twenty ESDB layers despite `flag value : none`, and compute the data mask per layer rather than sharing one (§6.5).
8. Verify each `.rst` body size equals rows × columns × (1 or 4) bytes before reading; expect seven bodies to be missing if only archive part 001 is unpacked (§6.2, §6.3).
9. Do not substitute `SMU_*_TAWC` for `STU_*_TAWC` — different derivation methods (§6.3).
10. Treat `cdinx` code 8 as fill and the code list as non-ordinal; never interpolate the variable numerically (§8.3).
11. Take the CDI temporal extent from the `time` variable, not from `16_START_DAY` / `35_START_PERIOD` / `36_END_PERIOD`, which describe the source slice (§8.4).
12. Index away the degenerate `band` dimension (§8.1).
13. Do not compare `.prj` files as strings — three cosmetic variants all mean EPSG:2154 (§3.1).
14. Store `CLASSEFIAB` as derived from `CLASSE` and `FIAB_TOT`, not as an independent attribute (§7).
15. Permit all four reliability values (`FORTE`, `MOYENNE`, `FAIBLE`, `INCONNUE`) in each of `FIAB_MNT`, `FIAB_ESO`, `FIAB_TOT`, even though no single field uses all four (§7.3).

## 12. Provenance of This Document

Every figure was measured from the delivered files on 2026-09-02:

- Vector schemas, feature counts, extents and value domains: GDAL/OGR 3.8.5 (`ogrinfo`, with the SQLite dialect for grouped queries) over the two GeoPackages, the eight shapefiles and the geometry-less `.dbf`.
- dBASE version and language-driver bytes: direct byte reads at offsets 0 and 29.
- Idrisi raster schemas: the twenty `.RDC` files read as text; body sizes from the filesystem, checked against rows × columns × bytes-per-cell.
- NetCDF structure, attributes, time axis and the full value histogram: the `netCDF4` Python library over all 63 475 200 cells.
- Archive composition: `zipfile` listings of both delivery parts.

Where this document and `3SES-updated.md` overlap, the two agree. `3SES-updated.md` remains the reference for provenance, licensing, duplication analysis and narrative data-quality discussion; the per-layer raster statistics it reports (data-cell counts, means over data cells) are not repeated here.
