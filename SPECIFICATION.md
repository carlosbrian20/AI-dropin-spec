# The Dropin Protocol Specification

**Version: 1.1 (Lean)**

This protocol is designed to be lean, fast, and powerful. It follows one guiding principle:

> **"Sufficient Structure, Maximum Intelligence"**

This means we provide the *minimum* structure necessary for an AI to understand intent, and then trust the AI's native pattern-recognition to handle complexity. The burden is on the AI to be smart, not on the user to write perfectly-formed code.

---

### Guiding Principles

-   **Short, Sharp, Fast:** Prioritize speed and information density over handling every possible edge case.
-   **Sufficient Structure:** Provide the minimum syntax necessary for an AI to infer the user's intent.
-   **Trust the AI:** The burden of parsing complex values (like URLs or code blocks) is on the AI's contextual understanding, not on complex user-side rules.

### Core Syntax

| Rule | Syntax | Example |
| :--- | :--- | :--- |
| **Key-Value Pairs** | `Key=Value` | `Objective=Summarize_the_text` |
| **Section Topics** | `Section:Topic`| `Persona:Act_as_a_pirate` |
| **Underscore for Spaces**|`Key_Name=Value`|`Project_ID=Alpha-36`|
| **Comma Lists**|`key=item1,item2`|`Topics=AI,history,art`|
| **Forward Slash Lists**|`key=item1/item2`|`Options=yes/no/maybe`|
| **Parentheses Comments**|`key=value(comment)`|`Status=Complete(as_of_today)`|

### Structural & Parsing Rules

1.  **Provenance is Mandatory:** A dropin **MUST** begin with an `AI_Source` tag. This is critical for provenance tracking and debugging in a multi-AI environment.
    -   *Example:* `AI_Source=gem2.5/User=...`

2.  **Implicit Termination:** The dropin ends when the syntactic pattern is broken. There is **no** `End_of_Dropin` tag. The AI is expected to stop parsing when it encounters normal, non-protocol text.

3.  **No Escape Sequences:** The protocol intentionally has no escape characters (`\`). An AI is expected to be intelligent enough to parse values with reserved characters contextually.
    -   *Example:* `URL=https://github.com/gripper36/AI-dropin-spec` should be parsed correctly, with the AI understanding that `//` is part of the URL, not a protocol feature.

4.  **Whitespace Pre-processing:** The receiving AI **MUST** strip all transmission artifacts (like extra line breaks or tabs between sections) to reconstitute the dropin before parsing.
