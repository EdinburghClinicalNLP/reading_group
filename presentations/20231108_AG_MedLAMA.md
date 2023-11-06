# Rewire-then-Probe: A Contrastive Recipe for Probing Biomedical Knowledge of Pre-trained Language Models

## TL;DR:
The authors introduce **MedLAMA** and **Contrastive-Probe**.

## Methods: 
### Base models:
BERT, BlueBERT, BioBERT, PubMedBERT, SciFive, T5, BART

### Baseline probing methods:
- Mask predict: Fill-in-the-blank style. In case of multi-mask tokens, the authors experimented with independent mask predictions or sequential predictions.
- Generative: Autoregressive LM.
- Retrieval: ranking the knowledge graph candidate entities based on the query representations.

### Proposed methods:
Contrastive Probe which comprises of several steps:
- Self-supervised Contrastive Rewiring: Sample a small set of sentences from the pretraining corpus, and replace 50% of the tail tokens with MASK token. Mainly to adapt the LLM to the self-retrieving game using InfoNCE objective function.
- Retrieval-based Probing: Nearest neighbour retrieval using the query with respect to the entity representations of all possible candidates.

## Experiments:
### Datasets:
[MedLAMA](https://github.com/cambridgeltl/medlama)
### Dataset preprocessing:
- Discard relationships with "insufficient" triples.
- Discard relationships that contain ≥ 10 gold answers.
- Manually discard triples that contain unnatural language entities
- Entities have to be ≤ 10 subwords (90% of the entities).
- Differentiate full set and hard query set. Hard query set filters out the easy triples via exact matching or ROUGE-L score.
### Metrics:
Accuracy@k, prompt bias, synonym variance, Qualitative expert evaluations (15 queries with top-10 answers are manually evaluated)
## Findings:
- "More comprehensive" analyses compared to BioLAMA paper
- Contrastive-Probe performance is very unstable. Changes in random seeds affect the performance.
- Human expert evaluation shows that the existing evaluation criteria could over-penalise the models since UMLS ground truth does not fully capture the real-world valid answers.
