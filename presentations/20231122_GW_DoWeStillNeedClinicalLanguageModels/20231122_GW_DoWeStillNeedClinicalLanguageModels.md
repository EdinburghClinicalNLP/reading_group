# 20231122_GW_DoWeStillNeedClinicalLanguageModels

# ****Do We Still Need Clinical Language Models?****

## Metadata

- **Date**: November 22nd, 2023
- **Presenter**: [Giwon Hong](https://honggiwon.github.io/)
- **Original paper**: [https://arxiv.org/abs/2302.08091](https://arxiv.org/abs/2302.08091)
- **Code & Model**: [https://www.physionet.org/content/clinical-t5/1.0.0/](https://www.physionet.org/content/clinical-t5/1.0.0/)

## TL;DR:

This paper compared LMs of various sizes and training data in clinical tasks to investigate whether a language model specialized for the clinical domain is needed (**Answer: Yes**).

## Background

- Emergence of Large Language Models
    - LLMS with over 100B+ parameters already contain clinical knowledge [(Singhal et al., 2022)](https://arxiv.org/abs/2212.13138)
    - ChatGPT achieved passing scores on the US Medical Licensing Exam (USMLE)
    - **Do We Still Need Clinical Language Models?**
- Specialized Clinical Language Model
    - The authors define a specialized clinical model to be a **model pretrained over clinical notes**
    - As the opposite of general-purpose **models pretrained on open-domain web text**
    - Clinical notes
        - Electronic health record (EHR) written by clinicians about a patient's past medical histroy, lab results, medications, and current clinical presentation
        - They often contain **grammatical errors**, **abbrviations** not defined in the context, and **domain specific terminology**
        - Different from Biomedical text: biomedical text is fluent, edited and polished
- In-context Learning (ICL)
    
    ![Untitled](20231122_GW_DoWeStillNeedClinicalLanguageModels%2066946e421c4d4c1e8b10382bc50da02b/Untitled.png)
    
    - In-context demonstration (examples) of downstream tasks are included in the prompt
    - Unlike finetuning model parameters are not updated
    - ICL is especially useful in data-poor domains (clinical, etc.)
- How to deal with Clinical Notes?
    
    ![Untitled](20231122_GW_DoWeStillNeedClinicalLanguageModels%2066946e421c4d4c1e8b10382bc50da02b/Untitled%201.png)
    
    1. Training a clinical model from scratch
        - Pretraining a model from scratch incurs a **high computational cost**
        - It may retain sensitive patient information
    2. Domain-adaptive pretraining (DAPT, [Gururangan et al., 2020](https://arxiv.org/abs/2004.10964))
        - DAPT from general text LMs or biomedical models
        - It may retain sensitive patient information
    3. Finetuning general-purpose models
        - The semi-structured and abbreviated text in downstream clinical tasks may adversely affect performance
        - **More expensive inference-time costs** as it must be larger to obtain the same performance as specialized clinical models
    4. In-content Learning on general-purpose models
        - Large scale models are typically accessible only though API → adding concerns about **security and data privacy**
        - There is a tendency to generate **realistic but factually incorrect content** → critical problem in medical domain

## Models

![Untitled](20231122_GW_DoWeStillNeedClinicalLanguageModels%2066946e421c4d4c1e8b10382bc50da02b/Untitled%202.png)

PTT stands for pretraining tokens

1. Training a clinical model from scratch
    - Clinical-T5-Base, Clinical-T5-Large: Trained by the authors (T5 architecture)
2. Domain-adaptive pretraining
    - Clinical-T5-Base-Ckpt: Trained by the authors from the T5-Base checkpoint
3. Finetuning general-purpose models
    - T5 architectures: T5-Base, T5-Large, T5-XL
    - RoBERTa architectures (RoBERTa-Large)
        - BioClinRoBERTa ([Lewis et al., 2020](https://aclanthology.org/2020.clinicalnlp-1.17/))
        - GatorTron ([Yang et al., 2022](https://www.nature.com/articles/s41746-022-00742-2))
    - PubMedGPT ([Bolton et al., 2022](https://www.mosaicml.com/blog/introducing-pubmed-gpt))
4. In-content Learning on general-purpose models
    - Flan-T5-XXL: Instruction-tuning (Flan) T5-XXL model
    - GPT-3 (text-davinci-003): InstructGPT model
- Presenter's opinion
    - In subsequent experiments, comparisons between the T5 models will be made
    - However, the experimental settings of the T5 models are not fair
        - T5-Base vs. Clinical-T5-Base-Ckpt vs. Clinical-T5-Base: Total PTTs are different
        - Clinical-T5-Base-Ckpt is cased model, where other models are uncased

## Tasks & Dataset

- Clinical notes: [MIMIC III](https://physionet.org/content/mimiciii/1.4/) and [MIMIC IV](https://physionet.org/content/mimiciv/2.2/)

![Untitled](20231122_GW_DoWeStillNeedClinicalLanguageModels%2066946e421c4d4c1e8b10382bc50da02b/Untitled%203.png)

![Untitled](20231122_GW_DoWeStillNeedClinicalLanguageModels%2066946e421c4d4c1e8b10382bc50da02b/Untitled%204.png)

- MedNLI ([Romanov and Shivade, 2018](https://aclanthology.org/D18-1187/))
    - Hypothesis written by a doctor + premise taken directly from a clinical note
- RadQA ([Soni et al., 2022](https://aclanthology.org/2022.lrec-1.672/))
    - Extractive question-answering (QA) task on radiology reports
- CLIP ([Mullenbach et al., 2021](https://arxiv.org/abs/2106.02524))
    - Multi-label classification task to identify key-sentences contain some follow-up information

![Untitled](20231122_GW_DoWeStillNeedClinicalLanguageModels%2066946e421c4d4c1e8b10382bc50da02b/Untitled%205.png)

## When Should We Pretrain the Models from Scratch?

Pretraining the specialized clinical models from scratch will cost a lot, but inference costs will be reduced due to small parameter size

**When is pretraining from scratch more efficient w.r.t. the number of inference tokens?**

- The training cost C_train and the inference cost C_inference in FLOPs
    - P = the number of parameters, T = the number of tokens
    
    ![Untitled](20231122_GW_DoWeStillNeedClinicalLanguageModels%2066946e421c4d4c1e8b10382bc50da02b/Untitled%206.png)
    
    - If we train the model from scratch, it will enable the use of an in-domain vocabulary
        - On clinical notes, T5-Base tokenizer will produce 1.12 times more tokens than the Clinical-T5-Base tokenizer (additional token cost weight w)
- The total cost required to pretrain, finetune, and perform inference
    - T_pt = pretraining tokens, T_ft = finetuning tokens, T_i = inference tokens
    
    ![Untitled](20231122_GW_DoWeStillNeedClinicalLanguageModels%2066946e421c4d4c1e8b10382bc50da02b/Untitled%207.png)
    
- The point at which the costs of clinical model == general model
    
    ![Untitled](20231122_GW_DoWeStillNeedClinicalLanguageModels%2066946e421c4d4c1e8b10382bc50da02b/Untitled%208.png)
    
    - For Clinical-T5-Large and T5-XL, it is about **40B inference tokens**
- Presenter's opinion
    - Obviously, this does not take into account parameter-efficient finetuning
    - Oftentimes, Adapter Layers or LoRA require few parameters (<1% of the original model size)
        
        ![Untitled](20231122_GW_DoWeStillNeedClinicalLanguageModels%2066946e421c4d4c1e8b10382bc50da02b/Untitled%209.png)
        
    - Also, I couldn’t get 40B with the equation above

## Comparison between T5 Models

![Untitled](20231122_GW_DoWeStillNeedClinicalLanguageModels%2066946e421c4d4c1e8b10382bc50da02b/Untitled%2010.png)

- Clinical-T5-Base model outperformed the T5-Base and the larger T5-Large model
- Clinical-T5-Large model outperformed the T5-Large and the larger T5-XL model
- Pretraining from scratch yields the largest performance gains

## Importance of In-Domian Tokens

![Untitled](20231122_GW_DoWeStillNeedClinicalLanguageModels%2066946e421c4d4c1e8b10382bc50da02b/Untitled%2011.png)

- BioClinRoBERTa and GatorTron outperform RoBERTa (which has more pretraining FLOPs)
- GatorTron vs. PubMedGPT
    - GatorTron outperforms PubMedGPT even with smaller model size and FLOPs (on BioMed)
    - However, on United Sates Medical Licensing Exam, PubMedGPT outperforms GatorTron by 10 points
    - There is a difference between medical knowledge and clinical notes

## In-Context Learning Underperforms Task Specific Models

- To demonstrate the usefulness of ICL, the authors simulate a setting with less than 100 samples
    - 1-3 randomly sampled in-context examples

![Untitled](20231122_GW_DoWeStillNeedClinicalLanguageModels%2066946e421c4d4c1e8b10382bc50da02b/Untitled%2012.png)

- Comparsion of models trained with 1%, 5%, 10%, 25%, and100% of available training data
    - MedNLI: specialized clinical models outperform ICL models in all settings
    - RadQA: ICL models outperform the the clinical models when 1% of training data is used
    - CLIP: ICL models significantly underperform than the clinical models (This is probably because it is a type of task that LLMs did not encounter during the pretraining process)

## Presenter's Takeaways

### Pros

- The authors showed the need for the specialized clinical models
- The authors presented criteria for when to pretrain clinical models.

### Cons

- There are problems with the experimental designs, making fair comparisons difficult
    - The settings of T5 models (different total step/tokens, different tokenization)
    - Why there is no Clinical-RoBERTa-Large to comapre with BioClinRoBERTa and GatorTron?
    - ICL models’ performance in table is a result based on only 25% of the test set
- Parameter-efficient finetuning methods were not considered. As this is the one of the most commonly used method when training LLM (and considering its usefulness in domain adoption), it should have been covered.