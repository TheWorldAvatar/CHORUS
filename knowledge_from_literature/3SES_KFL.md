# Reusable Axioms from the HIP Ontology

A machine-consumable list of axioms extracted from **Stephen, S., Schildhauer, M., Janowicz, K., Currier, K., Hitzler, P., Shimizu, C., Fisher, C. K., & Rehberger, D. (2024). *The HIP Ontology: a formal framework to support disaster risk reduction and management*. CEUR Workshop Proceedings 3882 (JOWO 2024, co-located with FOIS 2024).**

### 0.1 Namespace block

```turtle
@prefix hip:    <https://undrr-hip.org/> .
@prefix deo:    <http://knowwheregraph/ontology/deo#> .
@prefix dpo:    <http://knowwheregraph/ontology/dpo#> .
@prefix skos:   <http://www.w3.org/2004/02/skos/core#> .
@prefix sosa:   <http://www.w3.org/ns/sosa/> .
@prefix ssn:    <http://www.w3.org/ns/ssn/> .
@prefix geo:    <http://www.opengis.net/ont/geosparql#> .
@prefix time:   <http://www.w3.org/2006/time#> .
@prefix prov:   <http://www.w3.org/ns/prov#> .
@prefix dct:    <http://purl.org/dc/terms/> .
@prefix qudt:   <http://qudt.org/schema/qudt/> .
@prefix schema: <https://schema.org/> .
```
---

## 1. HIP classification structure

| **#** | **Axiom** | **Source** |
| --- | --- | --- |
| H1 | `hip:HazardClassification rdfs:subClassOf skos:ConceptScheme .` | `[txt]` `[fig 4a]` |
| H2 | `hip:HazardType rdfs:subClassOf hip:HazardClassification .` — *prose reading* | `[txt]` |
| H3 | `hip:HazardCluster rdfs:subClassOf hip:HazardClassification .` — *prose reading* | `[txt]` |
| H4 | `hip:SpecificHazard rdfs:subClassOf hip:HazardClassification .` — *prose reading* | `[txt]` |
| H5 | **Alternative to H2–H4, read off the diagram:** the three facets are not subclasses of the scheme but are linked to it by `skos:inScheme`. Fig. 4a draws exactly one edge, labelled `skos:inScheme`, from the facet group to `hip:HazardClassification`. | `[fig 4a]` |
| H6 | `[ a owl:AllDisjointClasses ; owl:members ( hip:HazardType hip:HazardCluster hip:SpecificHazard ) ] .` | `[txt]` `[fig 4a]` — the facet group in Fig. 4a carries the MOMo disjointness marker |
| H7 | `hip:HazardCluster rdfs:subClassOf skos:Collection .` | `[txt]` `[fig 4a]` `[ttl]` |
| H8 | `hip:broader rdfs:subPropertyOf skos:broader .` | `[txt]` `[ttl]` |
| H9 | `hip:broader` is **not** transitive — no `owl:TransitiveProperty` assertion, deliberately. | `[txt]` |
| H10 | `hip:narrower owl:inverseOf hip:broader .` | `[tbl]` `[ttl]` |
| H11 | `hip:broader rdfs:domain [ owl:unionOf ( hip:SpecificHazard hip:HazardCluster ) ] ; rdfs:range hip:HazardType .` | `[txt]` `[fig 4a]` `[drv]` — the paper says the relation is domain/range-constrained but never prints the constraint |
| H12 | `hip:isMemberOf rdfs:domain hip:SpecificHazard ; rdfs:range hip:HazardCluster .` | `[txt]` `[fig 4a]` `[drv]` |
| H13 | `hip:hasMember owl:inverseOf hip:isMemberOf ; rdfs:subPropertyOf skos:member .` | `[tbl]` `[ttl]` |

**For 3SES.** This is the single most transferable finding in the paper. Shrink–swell sits in a multi-parent position in every classification 3SES touches — it is a geo-hazard, a soil-property phenomenon and a climate-triggered hazard at once.

---

## 2. HIP metadata and provenance framework

All from Table 1 unless noted. The table's `o` operator denotes the property's range.

| **#** | **Axiom** | **Source** |
| --- | --- | --- |
| M1 | `hip:identifier rdfs:subPropertyOf dct:identifier .` (the HIP reference number, e.g. `GH0025`) | `[tbl]` `[ttl]` |
| M2 | `rdfs:label` and `hip:vernacularName` carry the hazard name | `[tbl]` |
| M3 | `hip:synonym` carries synonyms and non-English equivalents | `[tbl]` `[ttl]` |
| M4 | `hip:definedAs rdfs:range hip:Definition .` | `[tbl]` |
| M5 | `hip:Definition` is a **reified entity with provenance** — not a literal. This is what lets a definition carry its source, its date and its authority. | `[tbl]` |
| M6 | `hip:describedAs rdfs:range hip:Description .` | `[tbl]` |
| M7 | `hip:definitionSource rdfs:range prov:Entity .` | `[tbl]` |
| M8 | `hip:descriptionSource rdfs:range prov:Entity .` | `[tbl]` |
| M9 | `hip:coordinatingEntity rdfs:range prov:Agent .` (the UN body giving technical guidance). The published file names this property `hip:coordinationOrganization`. | `[tbl]` vs. `[ttl]` |
| M10 | `hip:definitionSource`, `hip:coordinationOrganization rdfs:subPropertyOf prov:hadPrimarySource .` | `[ttl]` |
| M11 | `hip:measurementUnit rdfs:range qudt:Unit .` (globally used metrics and numeric limits) | `[tbl]` |
| M12 | `hip:relatedInstrument rdfs:range hip:Instrument .` (UN conventions, multilateral treaties) | `[tbl]` |
| M13 | `hip:hasDriver rdfs:range hip:Driver .` | `[tbl]` |
| M14 | `hip:hasOutcome rdfs:range hip:Outcome .` | `[tbl]` |

---

## 3. Disaster Event Ontology — event core

| **#** | **Axiom** | **Source** |
| --- | --- | --- |
| E1 | `deo:Event rdfs:subClassOf geo:Feature .` — gives every event standardised geometry and spatial relations | `[txt]` `[fig 5]` |
| E2 | `deo:Event rdfs:subClassOf sosa:FeatureOfInterest .` | `[txt]` `[fig 5]` |
| E3 | `deo:Hazard rdfs:subClassOf deo:Event .` | `[txt]` `[fig 5]` `[ttl]` |
| E4 | `deo:Disaster rdfs:subClassOf deo:Event .` | `[txt]` `[fig 5]` `[ttl]` |
| E5 | `deo:DisasterImpact rdfs:subClassOf deo:Event .` | `[txt]` `[fig 5]` `[ttl]` |
| E6 | `deo:Hazard` and `deo:Disaster` are **disjoint** — Fig. 5 draws them inside a group carrying the disjointness marker | `[fig 5]` `[drv]` |
| E7 | `deo:hasTemporalScope rdfs:domain deo:Event ; rdfs:range time:TemporalEntity .` | `[txt]` `[fig 5]` |
| E8 | `deo:hasPart rdfs:domain deo:Event ; rdfs:range deo:Event .` — mereology over event segments and episodes | `[txt]` `[fig 5]` |
| E9 | `deo:hasPart` is intended to be **specialised**, not used raw: spatiotemporal parts (hurricane track segments) and impact parts (the impacts of one episode) are different sub-relations. | `[txt]` |
| E10 | `geo:Feature geo:hasGeometry geo:Geometry .` — inherited, not minted | `[fig 5]` |

---

## 4. Hazard typing, and the DEO ↔ HIP bridge

| **#** | **Axiom** | **Source** |
| --- | --- | --- |
| B1 | `deo:HazardType owl:equivalentClass hip:SpecificHazard .` — **this single axiom is the whole bridge between the two ontologies** | `[txt]` `[fig 5]` |
| B2 | `deo:hazardType rdfs:domain [ owl:unionOf ( deo:Hazard deo:Disaster ) ] ; rdfs:range deo:HazardType .` | `[txt]` `[fig 5]` |
| B3 | `deo:HazardType` is **the ultimate feature of interest** in SOSA terms — the hazard *theme*, not the occurrence | `[txt]` `[ttl]` |
| B4 | `deo:hasHazardProperty rdfs:domain deo:HazardType ; rdfs:range deo:HazardProperty .` (a hurricane's size, intensity, speed, direction) | `[txt]` `[fig 5]` |
| B5 | `deo:HazardProperty rdfs:subClassOf sosa:ObservableProperty .` | `[fig 5]` `[ttl]` |
| B6 | `deo:impactType rdfs:domain deo:DisasterImpact ; rdfs:range deo:ImpactType .` | `[txt]` `[fig 5]` |
| B7 | `deo:ImpactType deo:observedProperty sosa:ObservableProperty .` | `[fig 5]` |

---

## 5. Causality — the reified `possiblyCauses` pattern

| **#** | **Axiom** | **Source** |
| --- | --- | --- |
| C1 | `deo:possiblyCauses rdfs:domain deo:Event ; rdfs:range deo:Event .` — the general, deliberately hedged causal/correlational super-relation | `[txt]` `[fig 6]` |
| C2 | `deo:resultOf rdfs:subPropertyOf deo:possiblyCauses .` | `[fig 6]` |
| C3 | `deo:relatedImpact rdfs:subPropertyOf deo:possiblyCauses .` | `[fig 6]` |
| C4 | `deo:resultOf schema:domainIncludes deo:Disaster, deo:Hazard ; schema:rangeIncludes deo:Disaster, deo:Hazard .` — relates a disaster to the hazard it came from | `[txt]` `[fig 6]` |
| C5 | `deo:relatedImpact schema:domainIncludes deo:Disaster ; schema:rangeIncludes deo:Impact .` | `[txt]` `[fig 6]` |
| C6 | `deo:hasPossiblyCausesRelation schema:domainIncludes deo:Hazard, deo:Disaster ; schema:rangeIncludes deo:PossiblyCausesRelation .` | `[fig 6]` `[ttl]` |
| C7 | `deo:PossiblyCausesRelation deo:resultsIn` → `deo:Hazard`, `deo:Disaster` or `deo:Impact` | `[fig 6]` `[ttl]` |
| C8 | `deo:PossiblyCausesRelation` is a subclass of an *entity with provenance*: the reified relation is the carrier for provenance, interacting factors and conditions, the quantitative model used, or the participatory method used to assert the link | `[txt]` `[fig 6]` |

---

## 6. Element at risk, exposure and vulnerability

| **#** | **Axiom** | **Source** |
| --- | --- | --- |
| R1 | `deo:ElementAtRisk` — entities of value that may be adversely affected: living beings, buildings, facilities, economic activities, social structures | `[txt]` `[ttl]` |
| R2 | `deo:ElementAtRisk rdfs:subClassOf geo:Feature .` | `[fig 5]` |
| R3 | `deo:ElementAtRisk rdfs:subClassOf sosa:FeatureOfInterest .` | `[fig 5]` |
| R4 | `deo:affectedBy rdfs:domain deo:ElementAtRisk ; rdfs:range [ owl:unionOf ( deo:Hazard deo:Disaster ) ] .` | `[txt]` `[fig 5]` |
| R5 | `deo:involvedInImpact rdfs:domain deo:ElementAtRisk ; rdfs:range deo:DisasterImpact .` | `[txt]` `[fig 5]` |
| R6 | `deo:affectedBy` covers **both direct and indirect** effect — a house flooded, a person injured, *and* a service interrupted or a road blocked. No sub-relation distinguishes them. | `

---

## 7. The observation kernel (Figs. 1, 5, 9)

The pattern that makes hazard, disaster and impact data uniformly queryable.

| **#** | **Axiom** | **Source** |
| --- | --- | --- |
| O1 | `deo:HazardObservation`, `deo:DisasterObservation`, `deo:ImpactObservation` — one observation class per event class | `[fig 5]` `[ttl]` |
| O2 | `deo:hasHazardOfInterest rdfs:range deo:Hazard .` | `[fig 5]` `[ttl]` |
| O3 | `deo:hasDisasterOfInterest rdfs:range deo:Disaster .` | `[fig 5]` `[ttl]` |
| O4 | `deo:hasImpactOfInterest rdfs:range deo:DisasterImpact .` | `[fig 5]` `[ttl]` |
| O5 | `deo:hasUltimateFeatureOfInterest rdfs:range deo:HazardType .` — the observation points past the occurrence to the *theme* | `[fig 5]` `[fig 9]` `[ttl]` |
| O6 | `deo:observedProperty rdfs:range sosa:ObservableProperty .` | `[fig 5]` `[ttl]` |

---
