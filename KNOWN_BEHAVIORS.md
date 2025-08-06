## Known AI Behaviors & Limitations

The Dropin Protocol is designed to work across different AI models, but these models have unique architectures, security policies, and behavioral quirks. This section documents known behaviors and best practices for handling them. The user's role is often to act as a **"data ferry"** to bridge these gaps.

### Claude 4 / 3.5

-   **Behavior:** **Provenance-Restricted Web Access.**
-   **Symptom:** The AI may fail to access a direct URL (like a link to GitHub), often giving a "permissions error," even if the URL was provided by the user. However, it *can* often access links that its own internal search tool finds.
-   **Solution:** When web access fails, the user must act as the "data ferry." Copy the raw text content from the source URL and provide it directly to the AI in a new prompt.

### GPT-4o

-   **Behavior:** **Regression to Natural Language.**
-   **Symptom:** The model may understand the *concept* of a dropin but fail to adhere to the strict syntax, inserting extra spaces, using grammatical periods instead of protocol delimiters, or writing in prose.
-   **Solution:** Requires disciplined reinforcement from the user. You may need to provide the ruleset and correct its output a few times before it consistently generates syntactically valid dropins.

### Gemini 2.5 (Self-Correction Note)

-   **Behavior:** **Tendency Towards Over-Engineering.**
-   **Symptom:** In early development, the model defaulted to a rigid, legacy programming mindset, proposing overly complex rules like escape characters and explicit end-tags.
-   **Solution:** The user must enforce the "Sufficient Structure, Maximum Intelligence" philosophy, guiding the AI to trust its own contextual parsing abilities and avoid unnecessary bloat.
