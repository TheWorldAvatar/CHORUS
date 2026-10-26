# Validation framework

Each LLM-generated ontology will be systematically evaluated using the seven complementary validation steps presented below. Together, these steps assess syntactic validity, logical consistency, modelling quality, constraint conformance, functional adequacy, semantic accuracy and LLM-specific generation errors.

  ----------------------------------------------------------------------------------------
  \#      Validation layer            Method/tool        What it evaluates
  ------- --------------------------- ------------------ ---------------------------------
  **1**   **Syntactic & OWL           OWL API / parser / Parseability, syntax, IRIs,
          Conformance**               profile checker    namespaces, imports, OWL profile

  **2**   **Logical Verification**    **HermiT**         Consistency, satisfiability,
                                                         inferred hierarchy, entailments,
                                                         contradictions

  **3**   **Ontology Pitfall &        **OOPS!**          Modelling pitfalls, completeness,
          Structural Quality**                           conciseness, clarity,
                                                         annotations, structural issues

  **4**   **Constraint Validation**   **SHACL**          Cardinality, datatype, required
                                                         properties, structural/data
                                                         constraints

  **5**   **Requirements/Functional   **Competency       Vocabulary coverage, structural
          Validation**                Questions +        coverage, answerability and
                                      SPARQL**           correctness of answers

  **6**   **Semantic Accuracy &       **Gold ontology +  Entity/axiom precision, recall,
          Completeness**              reasoner + expert  F1; entailment precision/recall;
                                      assessment**       domain correctness

  **7**   **LLM-Specific Quality**    **Gold standard +  Hallucinations, omissions,
                                      expert analysis**  redundancy, invented
                                                         concepts/relations/axioms
  ----------------------------------------------------------------------------------------
