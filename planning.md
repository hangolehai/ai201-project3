# TakeMeter: Discourse Quality in r/soccer

## Community
**Chosen Community**: r/soccer (Match Threads)
**Reasoning**: Match threads on r/soccer are fast-paced and highly reactive. Discourse ranges from insightful tactical analysis to emotionally charged reactions and pure banter/trolling. This provides a diverse and rich text environment that is perfect for a classification task, as the quality and intent of the comments vary drastically.

## Labels
1. **Analysis**: The post makes a structured argument, tactical observation, or critique backed by specifics (e.g., stats, player positioning, historical comparisons).
   * *Example 1*: "The major difference today was Ruben Dias coming back, and not starting Bernardo Silva."
   * *Example 2*: "Portugal’s midfield is stacked, Trincao is a tidy player and he doesn’t even start."
2. **Banter**: Humorous, mocking, or sarcastic comments aimed at a player, team, or fan base. Often relies on memes or inside jokes.
   * *Example 1*: "Oh my camel."
   * *Example 2*: "Conceicao has this uncanny ability to find himself in space and then dribble himself into 4 players."
3. **Reaction**: An immediate, often emotional response to an event in the match without deep analysis or intended humor. Expresses feelings like shock, joy, awe, or frustration.
   * *Example 1*: "Ahhh damn that would’ve been an amazing goal."
   * *Example 2*: "What a game!"

## Hard Edge Cases
**Ambiguous Case**: A comment that uses sarcasm or humor to make a tactical point or complain about a player's performance.
*Example*: "Great pass to send his team through. Ronaldo really pulling out all the stops including winning balls back." (Could be Banter or Analysis/Reaction depending on context).
**Decision Rule**: If the primary purpose of the post is to mock or joke, it will be labeled `Banter`. If it genuinely points out a tactical flaw or observation despite a sarcastic tone, it leans towards `Analysis`. Pure emotional outbursts with sarcasm lean towards `Reaction`.

## Data Collection Plan
- **Source**: Scraped using Botasaurus from the r/soccer Match Thread for Portugal vs Uzbekistan.
- **Quantity**: 200 comments.
- **Handling Imbalance**: If any label (like `Reaction`) dominates over 70%, we will intentionally sample more `Analysis` or `Banter` comments by filtering for longer comment lengths (which correlate with Analysis) or keyword searches.

## Evaluation Metrics
- **Metrics**: Overall Accuracy, Precision, Recall, and F1-Score per class.
- **Why these metrics**: Accuracy alone can be misleading if the dataset is imbalanced. F1-Score is crucial to ensure the model isn't just predicting the majority class (e.g., guessing `Reaction` every time).

## Definition of Success
- **Success Criteria**: The fine-tuned model must achieve an F1-Score of at least 0.70 across all three classes and meaningfully outperform the zero-shot baseline by at least 15% in overall accuracy. This level of performance would make the classifier genuinely useful for auto-moderating or highlighting high-quality comments in a live thread.

## AI Tool Plan
- **Label Stress-Testing**: We will use an LLM to generate 5-10 ambiguous soccer comments to test if our label definitions hold up.
- **Annotation Assistance**: Manual annotation was used for the 200 comments to ensure grounded labels.
- **Failure Analysis**: We will provide the model's wrong predictions to an LLM to identify patterns (e.g., "The model consistently misclassifies sarcastic Banter as Reaction").
