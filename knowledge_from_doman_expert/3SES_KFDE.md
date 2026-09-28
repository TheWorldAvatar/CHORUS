# Expert Briefing: Reuse HIP GH0310 for Shrink-Swell

**A note from the domain side to whoever is building the 3SES ontology.**

The short version: please don't write your own definition of shrink-swell subsidence. A good one already exists. It went through UNDRR-ISC scientific consultation and peer review, it's the wording our partners will recognise, and it's what the Sendai reporting chain expects. Reinventing it costs us the one thing that makes a shared vocabulary worth having.

So this document hands you the published record and tells you, section by section, what I'd like done with each part of it. Where I've quoted the source, treat the quote as fixed text to transcribe rather than prose to improve. Some of it is awkwardly written and a bit of it is plainly wrong; I'll point out which bits, and §8 says what to do about them.

| | |
| --- | --- |
| **Authority** | UNDRR-ISC Hazard Information Profiles (HIPs), 2025 update |
| **Source endpoint** | `https://www.preventionweb.net/api/terms/hips/gh0310` |
| **Retrieved** | 2026-09-28 |
| **Concept IRI** | `https://www.undrr.org/terms/hips/gh0310` |
| **Scheme** | `https://www.preventionweb.net/drr-glossary/hips` |
| **Publisher** | UNDRR, ISC, and contributors |
| **Rights** | Creative Commons CC BY 4.0 — attribution required, and §7 says what to attribute |

---

## 1. The term to anchor on

**Shrink-Swell Subsidence** — `GH0310`

Start here. Everything else in the shrink-swell branch hangs off this one concept.

> Subsidence is a lowering or collapse of the ground, caused by various factors, including groundwater lowering, sub-surface mining or tunnelling, consolidation, sinkholes, or changes in moisture content in expansive soils. Shrink-swell is the term applied to the behaviour of expansive soils. These are a group of soils that exhibit volumetric change in response to changes in moisture content, such that they shrink in response to desiccation and swell by hydration, resulting in ground subsidence and ground heave respectively (BGS, 2020).

Carry that text as the definition of the 3SES shrink-swell hazard concept, credited to BGS 2020 by way of the HIP record. I'd ask you to make it a reified definition with its own source and date rather than a bare string on the class. The reason is practical: this wording will be revised in a future HIPs update, and when it is, we need to be able to say which version a given dataset was annotated against. A plain literal can't tell us that.

The source to carry with it is `http://www.bgs.ac.uk/geology-projects/shallow-geohazards/clay-shrink-swell` — *BGS, 2020. Swelling and Shrinking Soils. British Geological Survey (BGS). 27 September 2024.* The record marks it with both `dct:source` and `prov:wasQuotedFrom`.

It also records the policy link: *Sendai Framework for Disaster Risk Reduction 2015-2030.* Keep that. It's the reason this vocabulary exists at all.

One warning before you use the definition as-is: it defines two things at once. See §8.4.

---

## 2. Synonyms: take all eight, mint none

The record gives eight alternative labels. Not one of them is a separate concept.

| **Term** | **Note from the record** |
| --- | --- |
| Problem soils | |
| Expansive clay subsidence | |
| Expansive soils | |
| Clay shrink-swell | |
| Clay-rich subsidence | |
| Pipe clays | Marked in the source as "(American term)" |
| Swelling soils | |
| Shrinkable soils | |

I've seen ontologies pull "expansive soils" and "shrinkable soils" apart into two classes, presumably because they read as opposites. They aren't. They're two names for the same soil behaving in two directions, and the HIP record is unambiguous that all eight labels point at one thing. Attach them as alternative labels and leave it there.

Do keep the "(American term)" qualifier on *pipe clays*. Regional naming will bite us the moment we ingest anything from outside Europe — it's the same trap the HIP authors describe with hurricane, typhoon and tropical cyclone all naming one phenomenon.

These eight also give you a synonym list for matching against free text. One thing they don't give you: *retrait-gonflement des argiles* is absent, because HIP carries no French label here. Add it as a 3SES label of our own, and don't credit HIP with it.

---

## 3. Classification: reuse the placement, respect the poly-hierarchy

Three parents, one on each facet.

| **Facet (`dct:type`)** | **Term** | **IRI** |
| --- | --- | --- |
| `chapeau` | Gravitational Mass Movement (Landslide) | `https://www.preventionweb.net/hips-chapeau/gravitational-mass-movement-landslide` |
| `type` | Geological | `https://www.preventionweb.net/hips-type/geological` |
| `cluster` | Ground Failure | `https://www.preventionweb.net/hips-cluster/ground-failure` |

No definitions for any of the three — labels and IRIs only.

Reuse all three placements as asserted. This is the one point in the document I'd rather not negotiate: **model the placement with a non-transitive broader relation, never `rdfs:subClassOf`.**

Shrink-swell genuinely belongs under three different parents at the same time, and subsumption can't express that without lying. Assert it as subclassing and a reasoner will start telling us our ground-failure instances are landslides, which they are not, and which will quietly poison anything downstream that trusts the inferences. The HIP Ontology team walked into this and wrote it up; `litreview.md` has it as axiom H18. No need for us to find out the hard way.

The placement here also differs from the older KnowWhereGraph formalisation, which is part of the wider mess in §8.2.

---

## 4. Mineralogy: the material basis

This passage is the mechanism behind the predisposing factor, which is why mineralogy earns a place in the ontology rather than sitting in a footnote. Verbatim:

> The properties of expansive soils are attributable to the presence of swelling clay minerals. These clays range in their potential to absorb water according to their different structures. Expansive clay groups with increasing susceptibility to swelling include kandites (e.g., kaolinite, haloysite), illites (e.g., phengite, glauconite), vermiculites and smectites (e.g., montmorillonite, talc). These minerals are a product of weathering, commonly formed on land and then transported to the oceans. Their distribution reflects the underlying source rock geology, its diagenesis and stress history (e.g., stress-induced smectite to illite transformations) and the nature of the weathering, for example, wet climates are associated with kaolinite rich soils and dry environments are characterised by smectite clays (Environmental Characteristics of Clays and Clay Mineral Deposits (usgs.gov)).

| **Term** | **Kind** | **What the record says, and only that** |
| --- | --- | --- |
| Kandites | Clay mineral group | Lowest susceptibility to swelling of the four named. Examples: kaolinite, haloysite |
| Illites | Clay mineral group | Second in increasing susceptibility. Examples: phengite, glauconite |
| Vermiculites | Clay mineral group | Third in increasing susceptibility |
| Smectites | Clay mineral group | Highest susceptibility to swelling. Examples: montmorillonite, talc |

The ordering is the knowledge here. Four groups on a rising scale of swelling susceptibility, kandites at the bottom and smectites at the top — flatten that into an unordered code list and you've thrown away the only thing the passage tells us. So model it as ordered.

Be equally clear about what you're *not* being handed. There are no numeric thresholds, and no definitions of the individual minerals. If you need those, take them from a mineralogical vocabulary and credit it there. Don't let them arrive in the ontology looking as though HIP supplied them.

Two more claims in that passage are worth representing, because between them they're what makes the hazard mappable at all:

- Clay mineral distribution follows source rock geology, diagenesis and stress history, including stress-induced smectite-to-illite transformation.
- Climate decides the weathering product. Wet climates give kaolinite-rich soils; dry environments give smectite clays.

---

## 5. The explanatory notes, and where each belongs

Six typed notes come with the record. All quoted verbatim, with what I'd like each to drive.

### 5.1 Hazard drivers (`drivers`)

> Drivers of shrink-swell subsidence include groundwater lowering, groundwater extraction, sub-surface mining or tunnelling, consolidation, sinkholes, heatwaves and drought cycles, or changes in moisture content in expansive soils.

This is your triggering-factor vocabulary, and it has the advantage of being the sanctioned list rather than one we drafted ourselves. Use it in preference to anything home-grown.

It does mix two kinds of driver. Extraction, mining and tunnelling are anthropogenic; heatwaves and drought cycles are climatic. 3SES is mostly interested in the climatic ones, but please build the category so the anthropogenic drivers have somewhere sensible to live — CSTB will want them for the building-side work.

### 5.2 Impacts (`impacts`)

> Beneath the depth of influence of atmospheric change in moisture content, the water demand of vegetation, particularly trees on clay soils dominates the moisture content changes that lead to the soil shrinking (subsidence) and swelling (heave). Where subsidence and heave occur beneath or close to properties and infrastructure this can result in damage (Florida Department of Environmental Protection, 2020). The most obvious way in which expansive soils can damage foundations is by uplift as they swell with moisture increases. Swelling soils lift up and crack lightly-loaded, continuous strip footings, and frequently cause distress in floor slabs. Uplift is commonly differential, reflecting the different resisting forces across the structural foundations. Shrint-swell can influence structural stability during seismic events, resulting in infrastructure damage.

*("Shrint-swell" is the source's own typo, kept on purpose — see §8.7.)*

Two things in that paragraph have to survive into the model, and both are easy to lose in the translation.

The first is vegetation. Trees on clay soils aren't a minor contributing factor; below the depth where the atmosphere reaches, the record says their water demand *dominates*. Treat vegetation as a first-class driver.

The second matters even more. The damage mechanism is **differential** movement, not movement as such. A house that rises uniformly by some centimetres is largely fine. A house where one corner rises and the other doesn't is cracked. If the ontology can't tell those two apart, it cannot represent how this hazard actually causes damage, and every vulnerability model we build on top of it will be wrong in the same direction.

The structures named here — lightly-loaded continuous strip footings, floor slabs — give you a starting exposure vocabulary.

### 5.3 Metrics and numeric limits (`metrics`)

> For the most expansive clays, expansions of 10% are common (Nelson and Miller, 1992). In the field, expansive clay soils are recognisable in the dry season by the deep cracks that form in roughly polygonal patterns, commonly known as mudcracks. The zone of seasonal moisture content fluctuation is usually the upper 1.5-2 m, but can be up to 5 m, of the subsurface (BGS, 2020). This creates cyclic shrink/swell behaviour in the upper part of the soil column, and cracks can extend to considerable depths.
> Soils can be tested using the Expansion Index which provides an indication of their swelling potential, which engineers can use to specify building design measures (ASTM D 4829).

| **Term** | **Value or meaning as stated** |
| --- | --- |
| Expansion (most expansive clays) | 10% is common |
| Zone of seasonal moisture content fluctuation | Usually the upper 1.5–2 m of the subsurface; can be up to 5 m |
| Mudcracks | Deep cracks in roughly polygonal patterns, by which expansive clay soils are recognisable in the field in the dry season |
| Expansion Index | A test giving an indication of a soil's swelling potential, used by engineers to specify building design measures; standard ASTM D 4829 |

The Expansion Index is a named, standardised test with a document number behind it, so give it the standing of a measurement procedure identified by ASTM D 4829, not a loose property name on a class.

The depth figures deserve particular care, because they're the shape almost every quantity in this domain takes. "Usually 1.5–2 m, but can be up to 5 m" is a typical band *and* an outer bound, and both halves carry information a geotechnical reader will use. Collapse it to a single number and you've silently thrown away the part that matters for foundation design. Whatever pattern you pick for this, it needs to work for the rest of our quantities too.

### 5.4 Multi-hazard context (`multiHazardContext`)

> The figure below summarises common interactions between shrink-swell subsidence and other hazards. This information should be used with caution and not be solely relied upon in Disaster Risk Management, particularly as some interactions may not have been included. Note that hazardous events occurring together or locally in space or time may not necessarily cause, amplify or be otherwise related to each other. Specific examples of multi-hazard context can be found in the 'Hazard drivers' and 'Impacts' sections above.

The figure is a `prov:Entity` of `dct:type` `diagram`, labelled *"Hazard diagram for shrink-swell subsidence"*, `image/png`, at `https://www.preventionweb.net/sites/default/files/2025-08/hips_images-169.png`.

It's tempting to skim this as boilerplate. Don't — it's a modelling requirement in disguise. The authors are telling you two concrete things: co-occurrence in space or time is not causation, and their own list of interactions has gaps. Both mean the causal links in §6 have to be defeasible and attributed rather than asserted as flat facts.

I'd like this caveat carried into the ontology itself, as a scope note on the causal relations, where a reasoner-wielding user will actually meet it. Not tucked into documentation that nobody opens.

### 5.5 Risk management (`riskManagement`)

> The extensive distribution of these soils across the world has necessitated characterisation through index testing to inform remedial measures. At its simplest, the plasticity indices are used to define inorganic clays with inherent swelling capacity (e.g., BRE, 1993). Expansion of soils can also be measured in the laboratory directly, by immersing a remolded soil sample and measuring its volume change or using LiDAR techniques (Hobbs et al., 2014) and Earth Observation applications (Jones et al,. 2023).
> Prevention strategies include the use of non-expansive soils in construction or applying soil stabilisers to mitigate shrink-swell behavior before construction begins. However. the best way to avoid damage from expansive soils is to extend building foundations beneath the zone of water content fluctuation as modified to reflect the presence of vegetation (Rogers et al., no date).
> Dramatic changes such as clearing of vegetation associated with building plots on expansive soils should be limited to prevent change in soil behaviour (e.g., Jones, 2012).

| **Term** | **Role as stated** |
| --- | --- |
| Plasticity indices | Used, at its simplest, to define inorganic clays with inherent swelling capacity (BRE, 1993) |
| Direct laboratory expansion measurement | Immersing a remoulded soil sample and measuring its volume change |
| LiDAR techniques | Alternative measurement route (Hobbs et al., 2014) |
| Earth Observation applications | Alternative measurement route (Jones et al., 2023) |
| Use of non-expansive soils in construction | Prevention strategy |
| Soil stabilisers | Applied to mitigate shrink-swell behaviour before construction begins |
| Foundation extension below the fluctuation zone | Stated as the best way to avoid damage; depth modified to reflect the presence of vegetation |
| Limiting vegetation clearance | Restriction on building plots on expansive soils, to prevent change in soil behaviour |

Look at what those first four rows have in common. Plasticity indices, direct laboratory expansion, LiDAR and Earth Observation are four different ways of getting at one underlying thing: swelling potential. They disagree, they have different costs and coverage, and none of them is simply the right answer.

That's our indicator-plurality problem in miniature, and it's exactly what BRGM, ARTELIA and CSTB are each going to hand us a different version of. The model has to let all four sit side by side as distinct procedures observing one property, each with its own provenance and date. If the structure forces us to pick a winner, it's the wrong structure — and we'll find that out in a partner meeting rather than a review.

One separation to keep clean: the prevention strategies in the second and third paragraphs are operational measures, not properties of the hazard. They belong in an operational module. Please don't tangle them into the hazard branch.

### 5.6 Monitoring and early warning (`monitoringEarlyWarning`)

> The section and the table below offer an overview of monitoring river erosion & accretion. This information can be used for forecasting within a national early warning system (EWS). Since EWS capacities and processes differ across countries, the most current and specific information regarding EWS should be obtained from the appropriate national or regional agency/authority responsible for disaster management.
> | Which institution(s) produce(s) Disaster Risk Data/Information? | National Geological Surveys (e.g. British Geological Survey) often product data regarding shrink-swell probability, occurrence and likelihood referring to bedrock and superficial geology, often the context of a changing climate.
> | How is the Hazard Observed/Monitored/Forecast? | GNSS, InSAR, ground penetrating radar and soil moisture sensors are all used to monitor soil conditions.

*(That opening clause names the wrong hazard entirely. Skip it — §8.1.)*

Past the bad first sentence there are two useful things. The sensing vocabulary is worth carrying as named observation procedures: GNSS, InSAR, ground penetrating radar, soil moisture sensors.

The institutional statement is worth more than it looks. National geological surveys are named as the expected producers of shrink-swell probability and likelihood data, which is precisely the role BRGM plays for us. So model the producing agent, not just the dataset it produced. When two surveys give us different numbers for the same commune, we'll want to be able to say who said what.

---

## 6. Related hazards: take the links, but reify them

The record asserts causal relations to other HIP concepts. Labels and identifiers only — no definitions here. Each would need fetching from its own `https://www.undrr.org/terms/hips/{id}` IRI.

### 6.1 Caused by shrink-swell subsidence (`xkos:causes`) — 14 terms

| **ID** | **Term** |
| --- | --- |
| GH0309 | Subsidence and Uplift |
| GH0311 | Surface Rupture and Fissuring |
| TL0201 | Building Collapse |
| TL0204 | Bridge Failure |
| TL0206 | Supply Chain Failure |
| TL0208 | Nuclear Plant Failure |
| TL0209 | Power Outage/ or Blackout |
| TL0210 | Water Supply Failure |
| TL0211 | Emergency Telecommunications Failure |
| TL0212 | Radio and Other Telecommunication Failures |
| TL0402 | Inland Water Ways Transportation Accident |
| TL0404 | Rail Accident |
| TL0405 | Road Traffic Accident |
| TL0511 | Tailings |

### 6.2 Causing shrink-swell subsidence (`xkos:causedBy`) — 4 terms

| **ID** | **Term** |
| --- | --- |
| GH0401 | Compressive Soils |
| EN0305 | Permafrost Loss |
| TL0210 | Water Supply Failure |
| TL0307 | Mining Hazards |

Take the identifiers. They're how we'll join to other hazard vocabularies and to the sibling coastal-flooding work, and that's worth a lot.

Don't take the relation form. These arrive as flat predicates with no provenance, no conditions and no indication of strength. And look at TL0210: Water Supply Failure sits on both lists, as a cause of shrink-swell and as an effect of it. That isn't an extraction slip on our side — it's in the published record, and honestly it's a fair reflection of reality, since a leaking main wets the ground and ground movement shears the main. But it does tell you these links are indicative rather than logical, and a reasoner handed them as plain predicates will draw conclusions nobody intended.

So reify each one: the assertion, who made it, under what conditions, with what confidence. `litreview.md` axiom C11 has the pattern worked out. This matters more for us than it did for HIP, because our entire service *is* a causal claim — predisposing factor plus triggering factor yields damage — carrying an uncertainty the partners have already told us we cannot remove at building scale.

---

## 7. Attribution to carry

CC BY 4.0 obliges us here. These are the sources the record cites, labels verbatim.

| **Source** | **Locator** |
| --- | --- |
| BGS, 2020. Swelling and Shrinking Soils. British Geological Survey (BGS). 27 September 2024. | `http://www.bgs.ac.uk/geology-projects/shallow-geohazards/clay-shrink-swell` |
| ASTM, 2021. ASTM D 4829-21. Standard Test Method for Expansion Index of Soils. Accessed 1st May 2025 | `https://store.astm.org/d4829-21.html` |
| Building Research Establishment (BRE), 1993. Digest 240: Low-rise buildings on shrinkable clay soils: Part 1. Accessed 15 October 2024. | `http://www.brebookshop.com/details.jsp?id=138700` |
| Florida Department of Environmental Protection, 2020. Problem Soils. Accessed 15 October 2024. | `https://floridadep.gov/fgs/geologic-topics/content/problem-soils` |
| Foley, N.K. 1999. Accessed 1st August, 2024. | `https://pubs.usgs.gov/info/clays/` |
| Hobbs, P.R.N., L.D. Jones, M.P. Kirkham, P. Roberts, E.P. Haslam and D.A. Gunn, 2014. A new apparatus for determining the shrinkage limit of clay soils. Géotechnique, 64:195-203. Accessed 1st August 2024. | `https://nora.nerc.ac.uk/id/eprint/508951/` |
| Jones, Lee D.; Jefferson, Ian. 2012. Expansive soils. In: Burland, J., (ed.) ICE manual of geotechnical engineering. Volume 1, geotechnical engineering principles, problematic soils and site investigation. London, UK, ICE Publishing, 413-441. | *(no URI; blank node `#descriptionSource6`)* |
| Jones, L.D., Bateson, L., Hulbert, A. and Cigna, F., 2023. Modelling Potential Rates of Natural Subsidence using Geological and PSI Ground Motion Data: An Experiment in Europe and Great Britain. Authorea Preprints. DOI: 10.22541/essoar.170355052.24628258/v1 | *(no URI; blank node `#descriptionSource7`)* |
| Nelson, J.D. and D.J. Miller, 1992. Expansive Soils: Problems and Practice in Foundation and Pavement Engineering. Wiley. | *(no URI; blank node `#descriptionSource8`)* |
| Rogers, J.D., R. Olshansky and R.B. Rogers, no date. Damage to Foundations from Expansive Soils. Accessed 1st August 2024. | `https://web.mst.edu/~rogersda/expansive_soils/DAMAGE%20TO%20FOUNDATIONS%20FROM%20EXPANSIVE%20SOILS.pdf` |

Three of these arrive as blank nodes with nothing but a bibliographic string. Carry the string as it stands. Please don't go hunting for a plausible DOI and attach it — a citation we invented is worse than one that's merely incomplete.

---

## 8. Where I'd rather you didn't follow the source

Reusing wherever possible doesn't mean reusing uncritically. Here's where the record is defective and what I'd like done instead. If you hit something not on this list that looks wrong, come and ask rather than deciding alone.

**8.1 Drop the first sentence of the monitoring note.** It opens with "an overview of monitoring river erosion & accretion" in a record about shrink-swell subsidence. Everything after it is about the right hazard. It reads like an uncorrected copy-paste from a neighbouring HIP. Cut the clause, keep the rest.

**8.2 The identifier is `GH0310`, not `GH0025` — sort this out before you mint anything.** Both `litreview.md` §9.1 and `3SES-ontology-literature-review.md` §3.2 cite `hip:GH0025` for Shrink-Swell Subsidence, taken from the 2024 KnowWhereGraph Turtle. The live record is the 2025 update and has renumbered the term. One hazard with two identifiers is the exact problem this vocabulary was built to eliminate, so we can't shrug at it. Work out which is current, keep the other as a historical identifier, and whatever you decide, write down why. What I don't want is for one of them to be picked quietly and the choice to surface in six months.

**8.3 The namespace has moved too.** Concepts here live under `https://www.undrr.org/terms/hips/`, in scheme `https://www.preventionweb.net/drr-glossary/hips`. The paper uses `https://undrr-hip.org/`. Same situation as 8.2: resolve it, document it, prefer the live one unless you find a reason not to.

**8.4 The definition defines two things. Cut it if you must, but keep the whole.** Its opening sentence defines *subsidence* generally and lists causes — sub-surface mining, tunnelling, sinkholes — with no bearing on the shrink-swell mechanism at all. If you need something tighter, take the second and third sentences and label the result honestly as an extract, with the full quotation alongside. A trimmed version presented as *the* HIP definition would be a misrepresentation, and a reviewer who knows the source will spot it.

**8.5 Don't copy the flat causal predicates.** Covered in §6. Reify them.

**8.6 Don't invent definitions for terms that have none.** That's all of §3, all of §6, and the individual minerals in §4. A bare label with an honest "no definition supplied at source" is worth far more to us than a confident-sounding definition we can't defend when someone asks where it came from.

**8.7 The typos are deliberate, so don't quietly fix them.** "Shrint-swell" in §5.2; "often product data" and "often the context of" in §5.6; "However." for "However," and "Jones et al,. 2023" in §5.5. They're in the published record. Correcting them is defensible, but mark it where you do, or our text stops matching theirs and someone loses an afternoon working out which copy drifted.

---

## 9. What I'll look for when I review the branch

- [ ] The anchor concept points at the HIP IRI, and the identifier question in 8.2 has an answer with reasoning attached
- [ ] The definition is verbatim, reified, credited to BGS 2020, and dated
- [ ] All eight synonyms sit on one concept, with nothing split out
- [ ] All three classification parents are asserted, through a non-transitive broader relation rather than `rdfs:subClassOf`
- [ ] The four clay mineral groups keep their swelling-susceptibility ordering
- [ ] Differential ground movement can be told apart from uniform movement
- [ ] Depth and expansion figures survive as ranges with a typical band and an outer bound
- [ ] All four routes to swelling potential coexist, none privileged
- [ ] Causal links are reified and attributed, and carry the §5.4 caveat where a user will see it
- [ ] Terms with no HIP definition say so, and none has been filled in from elsewhere
- [ ] CC BY 4.0 attribution is present for all ten sources

If something here turns out to be unworkable once you're inside the model, tell me which part and why — several of these are my reading of what the domain needs, not laws of nature, and I'd rather revise one than have it worked around silently.
