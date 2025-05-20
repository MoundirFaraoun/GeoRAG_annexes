# GeoRAG_annexes

## A. Address anomlies
### A.1 Type of anomalies inbserved query-delivies
![image](https://github.com/user-attachments/assets/13c03734-51ac-4a2c-8407-e4fc58c1e358)

### A.1 Similar non matching query-deliveries
Below is an example of two similar query addresses where address names helped disambiguate the correct top-ranked match.

![name_context](https://github.com/user-attachments/assets/ace09b5e-e5af-415a-a620-a3d5ba2b16c2)

**Figure 3.** Correct candidate address disambiguation

## B. Failed address identification
### B.1 Google-Maps failed address identification
In Figure 4, ”`sogif dept cond fce et travaux neuf dsm chemin de la plaine
89 villeneuve-sur-yonne`” returns multiple potential POIs and non of them is the
intended address.

![multi_recip](https://github.com/user-attachments/assets/45893d9f-27f5-4920-b6e8-96a011c5ae09)

**Figure 4.** Example of multiple POIs returned in red on Google Maps

In Figure 5, ”`amd solutions france sime auxerre zi la gare seche nord
rue naurat bp 1 89250 chemilly sur yonne`” returns no POI.

![no_recip](https://github.com/user-attachments/assets/7a0c76ed-17e0-498f-a4c6-968e0b5a0b0e)

**Figure 5.** Example of no returned POI on Google Maps

### B.2 Example of failed kNN-vote Top-ranked retrieval

![top_k_retrieval](https://github.com/user-attachments/assets/b7f941ce-52c8-4ce2-bc05-955c4fb70c6c)

**Figure 6.** Correct query-delivery match ranked lower in the list.

## C. Precision_recall curves

We plot precision-recall curves, highlighting strong separability between valid and invalid matches across all retrieval models.

![image](https://github.com/user-attachments/assets/33d63b80-0052-4249-ad95-cee06f292fb4)

**Figure 7.** Precision-recall curves estimated during bootstrapping on validation data retrieval scores.

## D. Retrieval similarity scores used for validation

![image](https://github.com/user-attachments/assets/51616d72-c517-44ff-982a-acbcd592578d)

## E. Prompt
### E.1 Mistral prompt for LLM fine-tuning

![image](https://github.com/user-attachments/assets/888370db-bab9-46c0-920b-5e3a5dd15087)

### E.2 Example of augmented retrieved reference address

If we take for example the query-delivery, **Nicolas industrie bp 3 Route nationale 6
89290 Champs Sur Yonne**, we can fetch its top-k reference list in a way where each candidate address or alias from the database is augmented with normalized address attributes. We then presented in the prompt in the form of a JSON object as follows:

`{”ID”: ”573”, ”ADDRESS”: ”nicolas industrie bp 3 rte nationale 6 89290 champs sur
yonne”, ”ENTITIES”: {”NAME”: ”nicolas industrie”, ”STREET”: ”avenue du tertre”,
”POSTAL CODE”: ”89290”, ”CITY”: ”champs sur yonne”}}`

Here, we take note on the advantage of using address aliases already identified in the
retrieval set. In fact, if the LLM was only presented with the normalized version of the
query matching address, it would have no problem rejecting the address candidate. Indeed, it is clearly apparent that the normalized street, **Avenue du tertre**, is completely
different from the alias street, **Rte nationale 6**.

## E. NLI scoring function
Based on the NLI scoring strategy used in [31], we define our NLI scoring function $$s_{nli}(\cdot)$$ as the average of NLI similarities of three pre-trained multilingual cross-encoders[^8][^9][^10]. We purposely chose to aggregate different models' scores to alleviate the impact of false entailment. Specifically, the input of an NLI model is the concatenation of a query $$q$$ and a retrieved address $$d$$ upon which the model returns probabilities $$p_i$$ with $$i\in\{contradiction, neutral, entailement\}$$. We can then compute the NLI similarity as $$1-p_{contradcition}$$. As mentioned in [31], both orders $$(q,d)$$ and $$(d,q)$$ are considered to mitigate positional bias of the NLI system. The NLI similarity of a model $$E$$ is then denoted:


$$SIM_{E} = \frac{1}{2}(1-p_{contradiction} + 1-p'_{contradiction})$$ 

Where $$1-p_{contradiction}$$ and $$1-p'_{contradiction}$$ are NLI similarities of $$(q, d)$$ and $$(d,q)$$ respectively.

## G. Encoders description

**DistilBERT [22]**: A lightweight transformer encoder distilled from BERT [9]. We fine-tuned its multilingual version11 to adapt it on our French postal address dataset.

**CamemBERT [23]**: A RoBERTa-based [44] encoder pretrained specifically on large-scale French text[^12]. It is widely used on various downstream task due to its French language specificity. We fine-tuned it on our dataset to improve its ability to produce french postal address sentence embedding.

**FlauBERT [25]**: Another French specific language encoder based on BERT. This model was trained on very large and heterogeneous French corpus. we used the base-cased version[^13] for our fine-tuning.

**FrALBERT [26]**: An ALBERT-based [?] encoder, proposed as an alternative to BERT encoder. We chose the French language version[^14] to further fine-tune it.

**XLM-RoBERTa [24]**: Yet another RoBERTa-based encoder. This encoder is very popular among researchers for its improved performance compared to other multilingual encoders. We adapt the base version[^15] as well on our dataset.

## D. ccuracy and Precision at different values of $$k$$

![image](https://github.com/user-attachments/assets/3d086fb1-572f-4fd3-b605-da74c9dede74)

**Figure 8.** Accuracy and Precision at different $$k$$ values.

## I. Pair-wise address agrement

![image](https://github.com/user-attachments/assets/02ed5f2c-0c61-474a-86b8-cbbb8f7f88e5)

**Figure 9.** Pair-wise address agreement (Jaccard similarity heatmaps).

## J. Hallucinations
