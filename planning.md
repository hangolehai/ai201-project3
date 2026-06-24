# Project Planning: TakeMeter

## 1. Community
**Chosen Community:** Soccer Discourse (primarily sourced from Reddit's `r/soccer` Match Threads).
**Reasoning:** Soccer discourse is highly polarized, emotional, and data-driven all at once. Separating objective tactical discussions from emotional reactions or tribal mockery is a challenging NLP task due to heavy slang and implicit context. The quality and intent of the comments vary drastically, making it perfect for a classification task.

## 2. Labels
The dataset will be categorized into exactly one of the following three labels:
*   **Analysis:** Uses specific metrics (e.g., xG, tactics, possession stats, historical data) to build a verifiable, structured argument about the game or players.
    *   *Example 1:* "The major difference today was Ruben Dias coming back, and not starting Bernardo Silva."
    *   *Example 2:* "Portugal’s midfield is stacked, Trincao is a tidy player and he doesn’t even start."
*   **Reaction:** An immediate, feelings-based response with little to no structural argument. Usually short and emotional (e.g., celebration, frustration, disbelief).
    *   *Example 1:* "Ahhh damn that would’ve been an amazing goal."
    *   *Example 2:* "What a game!"
*   **Banter:** Designed primarily to mock rival teams, push a biased agenda, or insult players/managers. Often relies on inside jokes, catchphrases, and intentional disrespect.
    *   *Example 1:* "Oh my camel."
    *   *Example 2:* "Conceicao has this uncanny ability to find himself in space and then dribble himself into 4 players."

## 3. Hard Edge Cases
**Ambiguous Case 1 (Sarcastic Analysis):** A post that mimics the structure of an "Analysis" post but the ultimate goal is to mock a player.
*   *Example:* "Great pass to send his team through. Ronaldo really pulling out all the stops including winning balls back."
*   **Decision Rule:** If the primary purpose of the post is to mock or joke, it will be labeled `Banter`. If it genuinely points out a tactical flaw despite a sarcastic tone, it leans towards `Analysis`.

**Ambiguous Case 2 (Reactionary Banter):** A highly emotional, immediate outburst that also includes a specific insult toward an opponent.
*   **Decision Rule:** If the comment relies heavily on an inside joke or internet slang aimed at disrespecting a specific person, label as `Banter`. If it's just pure emotional frustration ("I hate this ref"), label as `Reaction`.

## 4. Data Collection Plan
*   **Source:** Scraped using Botasaurus from the `r/soccer` Match Thread for Portugal vs Uzbekistan.
*   **Quantity:** 200 comments.
*   **Handling Imbalance:** If any label dominates over 70% of the dataset, we will intentionally sample more of the underrepresented classes by filtering for longer comment lengths (for Analysis) or keyword searches (for Banter).

## 5. Evaluation Metrics
*   **Metrics:** Overall Accuracy, Precision, Recall, and F1-Score per class.
*   **Why these metrics:** Accuracy alone can be misleading if the dataset is imbalanced. F1-Score is crucial to ensure the model isn't just predicting the majority class (e.g., guessing `Reaction` every time) and is actually learning the decision boundaries.

## 6. Definition of Success
**Success Criteria:** The fine-tuned model must achieve an F1-Score of at least 0.70 across all three classes and meaningfully outperform the zero-shot baseline by at least 15% in overall accuracy. This level of performance would make the classifier genuinely useful for auto-moderating or highlighting high-quality comments in a live thread.

## 7. AI Tool Plan
*   **Label Stress-Testing:** We will use an LLM (like Claude or Llama) to generate 5-10 ambiguous soccer comments to test if our label definitions hold up.
*   **Annotation Assistance:** Manual annotation will be used for the 200 comments to ensure grounded labels, but we will use an LLM for final review.
*   **Failure Analysis:** We will provide the model's wrong predictions to an LLM to identify patterns (e.g., "The model consistently misclassifies sarcastic Banter as Reaction").
