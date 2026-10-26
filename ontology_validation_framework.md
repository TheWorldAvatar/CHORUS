# Validation framework

Each LLM-generated ontology will be systematically evaluated using the seven complementary validation steps presented below. Together, these steps assess syntactic validity, logical consistency, modelling quality, constraint conformance, functional adequacy, semantic accuracy, and LLM-specific generation errors.

| # | Validation layer | Method/tool | What it evaluates |
|---|---|---|---|
| **1** | **Syntactic & OWL Conformance** | OWL API / parser / profile checker | Parseability, RDF/OWL syntax, valid IRIs, namespaces, imports, and OWL profile conformance |
| **2** | **Logical Verification** | **HermiT** | Ontology consistency, class satisfiability, inferred class/property hierarchies, expected/unexpected entailments, and logical contradictions |
| **3** | **Ontology Pitfall & Structural Quality** | **OOPS!** | Common ontology modelling pitfalls involving classes, properties, hierarchy, domains/ranges, annotations, naming, and structural design |
| **4** | **Constraint Validation** | **SHACL** | Conformance to required cardinalities, datatypes, mandatory properties, value constraints, and structural/data constraints |
| **5** | **Requirements & Functional Validation** | **Competency Questions + SPARQL** | Vocabulary and structural coverage of requirements, CQ answerability, and correctness of returned answers |
| **6** | **Semantic Accuracy & Completeness** | **Gold-standard ontology + reasoner + expert assessment** | Entity and axiom precision/recall/F1, semantic/entailment precision and recall, missing knowledge, and domain correctness |
| **7** | **LLM-Specific Quality** | **Gold standard/source material + expert analysis** | Hallucinated concepts, relationships and axioms; omissions; unnecessary/redundant entities; and other unsupported generated knowledge |
