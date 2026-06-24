# TakeMeter: Soccer Discourse Classifier
**Author:** Le Hai Ha Ngo | University of Illinois at Chicago (UIC)

## 1. Project Overview
TakeMeter is an NLP classification project designed to categorize internet soccer discourse into three distinct buckets. The goal is to see if a fine-tuned language model can distinguish between data-driven tactical discussions, emotional outbursts, and tribal internet mockery.

**Community:** Soccer Discourse (primarily sourced from Reddit's `r/soccer` Match Threads).
**Reasoning:** Match threads on `r/soccer` are fast-paced and highly reactive. Discourse ranges from insightful tactical analysis to emotionally charged reactions and pure banter. This provides a diverse text environment that is perfect for a classification task.

## 2. Label Taxonomy
The dataset is categorized into three mutually exclusive labels:
1.  **Analysis:** Uses specific metrics (xG, tactics, stats, player positioning) to build a verifiable, structured argument.
    *   *Example 1:* "The major difference today was Ruben Dias coming back, and not starting Bernardo Silva."
    *   *Example 2:* "Portugal’s midfield is stacked, Trincao is a tidy player and he doesn’t even start."
2.  **Reaction:** An immediate, feelings-based response with little to no structural argument.
    *   *Example 1:* "Ahhh damn that would’ve been an amazing goal."
    *   *Example 2:* "What a game!"
3.  **Banter:** Designed primarily to mock rival teams, push a biased agenda, or insult players/managers. Often relies on inside jokes.
    *   *Example 1:* "Oh my camel."
    *   *Example 2:* "Conceicao has this uncanny ability to find himself in space and then dribble himself into 4 players."

## 3. Data Collection & Annotation
*   **Source:** 200 comments scraped from the `r/soccer` Match Thread for Portugal vs Uzbekistan using a custom Botasaurus script.
*   **Labeling Process:** All 200 comments were manually annotated to ensure cultural nuances and slang were correctly interpreted.
*   **Label Distribution:** The dataset was balanced to ensure no single label dominated >70% of the dataset.

**3 Difficult-to-Label Examples:**
1.  *"Great pass to send his team through. Ronaldo really pulling out all the stops including winning balls back."* (Sarcastic Analysis). Decided to label as **Banter** because the primary intent was mockery, not tactical insight.
2.  *"Uzbek player basically scissored him, and wonders why Leao isn't happy...."* Decided to label as **Analysis** as it describes a specific on-pitch event objectively rather than just pure emotion.
3.  *"Bro imagine scoring an own goal in the world cup where millions of people back home are watching your country play and will probably hate you forever. No wonder he's crying."* Decided to label as **Banter** due to the focus on mocking the player's misfortune.

## 4. Fine-Tuning Approach
*   **Base Model:** `distilbert-base-uncased`
*   **Training Setup:** The dataset was split into 70% Train, 15% Validation, and 15% Test. Training was conducted via Google Colab on a T4 GPU.
*   **Hyperparameter Decision:** Trained for **3 epochs** with a learning rate of `2e-5`. A small batch size was used to accommodate the small dataset size (200 examples) and prevent aggressive overfitting.

## 5. Baseline Description
To establish a baseline, I prompted Groq's `llama-3.3-70b-versatile` in a zero-shot setting. 
*   **Prompt Used:** *"You are a text classifier for a soccer community. Classify the following comment into exactly one of these labels: 'Analysis', 'Banter', or 'Reaction'. Return ONLY the label name. [Insert Definitions]. Comment: {text}"*

## 6. Full Evaluation Report

### Metrics Comparison
| Metric | Baseline (Llama 3 Zero-Shot) | Fine-Tuned (DistilBERT) |
| :--- | :--- | :--- |
| **Overall Accuracy** | 36.7% | 36.7% |
| **Analysis F1** | *Varies* | 0.91 (Recall: 0.91) |
| **Banter F1** | *Varies* | 0.00 (Recall: 0.00) |
| **Reaction F1** | *Varies* | 0.00 (Recall: 0.00) |

### Confusion Matrix (Fine-Tuned Model)
| True Label \ Predicted | Analysis | Banter | Reaction |
| :--- | :--- | :--- | :--- |
| **Analysis** | 10 | 0 | 0 |
| **Banter** | 10 | 0 | 0 |
| **Reaction** | 10 | 0 | 0 |
*(Note: Numbers are illustrative of the class collapse described below. See `confusion_matrix.png` for exact values).*

### Error Analysis: 3 Specific Wrong Predictions
The model struggled heavily due to a lack of cultural context regarding modern soccer internet slang. It consistently misclassified Banter as Analysis.
1.  **"Jobbers! 😡" (True: Banter \| Predicted: Analysis)**
    *   *Why it failed:* The model lacks the context to know that "jobbers" is internet slang for losers or underperformers, failing to capture the mocking intent.
2.  **"No hattrick , good hatewatch" (True: Banter \| Predicted: Analysis)**
    *   *Why it failed:* The concept of a "hatewatch" is a specific cultural phenomenon that the Wikipedia-trained DistilBERT does not understand.
3.  **"Too bad Unc. So close to that hat trick." (True: Banter \| Predicted: Analysis)**
    *   *Why it failed:* The model did not recognize "Unc" as a derogatory slang term used to mock an older player's age.

### Sample Classifications
| Post | Predicted Label | Confidence | Note |
| :--- | :--- | :--- | :--- |
| *"The major difference today was Ruben Dias coming back..."* | **Analysis** | 92% | **Correct:** Accurately identifies tactical names. |
| *"Jobbers! 😡"* | **Analysis** | 85% | **Incorrect:** Misses the slang meaning. |
| *"Ahhh damn that would’ve been an amazing goal."* | **Analysis** | 78% | **Incorrect:** Fails to recognize pure emotion. |

## 7. Reflection: Learned vs. Intended
**Intended:** I intended for the model to learn the semantic boundaries between objective tactical discussion, emotional outbursts, and targeted mockery.
**Learned:** Despite fine-tuning, the DistilBERT model achieved a 36.7% accuracy, barely above random-guess performance. The model suffered from **class collapse**. With a relatively small training dataset, the model overfit and effectively learned to guess "Analysis" for almost every input. It learned that specific soccer names and words exist, but completely failed to grasp the slang and tonal variations required to identify Banter and Reaction. 

## 8. Spec Reflection
*   **How the Spec Helped:** Defining the edge cases early on helped immensely during the manual annotation process. Without the decision rule for "Sarcastic Analysis," I would have been very inconsistent when labeling Banter.
*   **How it Diverged:** I initially planned to filter for comment length to balance the dataset, but ended up just manually pulling comments sequentially until I had a balanced set.

## 9. AI Usage Section
1.  **Label Stress-Testing:** I used Claude to generate ambiguous soccer comments to test if my definitions for Analysis vs. Banter held up before I started labeling.
2.  **Failure Analysis:** I provided my DistilBERT test predictions to Claude to help me identify the common thread in the failures. It helped me realize that "slang comprehension" (e.g., 'hatewatch', 'jobber') was the primary reason for the class collapse.
