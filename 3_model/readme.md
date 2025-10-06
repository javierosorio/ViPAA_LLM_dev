### A. Model From scratch
....
....

### B. Fine-tuned on ConfliBERT
#### Përshkrim i procesit dhe skriptit
####  **E përgjitshme**

We prepared and fine-tuned a **multi-label ConfliBERT classifier** to automatically assign violence-event categories (e.g., *ASESINATO*, *BIENES CIVILES*, *PILLAJE*, etc.) to text descriptions of incidents from the **VIPAA corpus** (~45 K Spanish event reports downloaded from nocheyniebla.org).

The workflow covered full preprocessing, label normalization, and fine-tuning of **snowood1/ConfliBERT-cont-uncased** under the UA HPC environment.

####  **Çdo hap**

#### 1 Data preparation

* Two Excel files were used:

  * **vipaa_for_training.xlsx** — main dataset with event descriptions and coded labels.
  * **list_of_labels.xlsx** — master list of 65 standardized action labels (actions_clean column).
* Both were loaded into Pandas DataFrames.

#### 2 Label extraction and cleaning

* The relevant column was **Tipificacion_with_codes** (entries like
  "D:2:80 BIENES CIVILES, D:2:801 ATAQUE A OBRAS E INST. QUE CONT. FUERZAS PELIGR.").
* We removed numeric codes (B:2:40, D:4:701, etc.) using a regular-expression parser, yielding clean upper-case labels such as:

  text
  ['BIENES CIVILES', 'ATAQUE A OBRAS E INST. QUE CONT. FUERZAS PELIGR.']
  
* Nested or repeated lists (e.g., [["A","B"]] or [A,A,A]) were flattened and deduplicated:

  python
  data_df[label_col] = data_df[label_col].apply(lambda lst: sorted(set(lst)))
  

#### 3 Normalization and mapping

* Accents were stripped (unicodedata.normalize), text upper-cased, and matched to the 65 canonical labels in list_of_labels.xlsx.
* A **label->ID** dictionary (label_map) and **ID->label** reverse map (id2label) were created.
* Each event's label list was converted to numeric IDs (e.g., [6, 36]).

#### 4 Multi-hot encoding

* A binary matrix y ∈ ℝ^{45511×65} was built, where 1 marks presence of a label.
  Example: row ₀ -> 1 for columns 6 and 36.

#### 5 Environment setup on UA HPC

* Installed missing dependencies inside the HPC Jupyter environment:

  bash
  pip install --user accelerate
  pip install --user --upgrade transformers torch pandas scikit-learn
  
* Verified versions:
  accelerate 1.0.1, transformers 4.46.3, torch 2.4.1+cu121.

#### 6 Model and tokenizer

* Loaded **ConfliBERT**:

  python
  tokenizer = AutoTokenizer.from_pretrained("snowood1/ConfliBERT-cont-uncased")
  model = AutoModelForSequenceClassification.from_pretrained(
      "snowood1/ConfliBERT-cont-uncased",
      num_labels=65,
      problem_type="multi_label_classification"
  )
  

#### 7 Train/validation split and tokenization

* 90 % train / 10 % validation split (train_test_split).
* Tokenized descriptions (max_length = 256).

#### 8 Training configuration

* Used Hugging Face Trainer API with BCEWithLogitsLoss (multi-label default).
* Key arguments:

  python
  TrainingArguments(
      output_dir="./confli_bert_vipaa",
      evaluation_strategy="epoch",
      save_strategy="epoch",
      load_best_model_at_end=True,
      metric_for_best_model="micro/f1",
      learning_rate=2e-5,
      per_device_train_batch_size=8,
      num_train_epochs=3,
      weight_decay=0.01,
      fp16=True  # mixed precision on GPU
  )
  
* Custom metrics: micro/macro F1, precision, recall using sigmoid + 0.4 threshold.

#### 9 Threshold tuning and evaluation

* Post-training, we tuned the sigmoid threshold (0.2–0.6) on validation data to maximize micro-F1.
* Produced per-label F1 reports and saved the best model/tokenizer.

#### 10 Output

* Saved fine-tuned model directory: ./confli_bert_vipaa_best/
  (contains config.json, pytorch_model.bin, tokenizer.*)
* Achieved multi-label predictions for new event texts using:

  python
  preds, probs = predict_labels(["Guerrilleros atacaron el puesto de policía..."])
  

#### 11 **Idea kryesore**

We turned raw textual incident narratives into structured multi-label conflict event classifications aligned with the **VIPAA typology** and the **ConfliBERT** language representation, creating a pipeline that:

1. Preprocesses and normalizes domain-specific labels.
2. Encodes them in a multi-hot representation.
3. Fine-tunes a Spanish conflict-domain transformer for multi-label classification.
4. Evaluates and saves a deployable model.
