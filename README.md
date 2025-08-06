# The Dropin Protocol

## What is a Dropin?

A Dropin is a simple, powerful tool for giving an AI context and memory. It's a short, structured block of text that you place at the beginning of a conversation to instantly "bootstrap" an AI, telling it who to be, what to know, and what its goal is.

Think of it as a **mission briefing for your AI.**

Using a simple but strict syntax (`Key=Value`, `Section:Topic`), a dropin transforms a generic, stateless AI into a specialized assistant that is already up to speed on your project, persona, or specific problem, saving you the time and frustration of repeating yourself.

## ⚠️ A Critical Security Warning

Dropins are powerful because they package context, but this means they can easily contain **sensitive or private information**.

-   Project details
-   Personal data (names, emails)
-   Confidential ideas
-   Client information

**Always sanitize your dropins before sharing them publicly.** Replace any sensitive information with generic placeholders like `[Project_Name]` or `[Client_Email]`. Treat a dropin with the same security caution as you would an email or a private document.




## The Problem it Solves: AI Amnesia

Modern Large Language Models (LLMs) are incredibly powerful, but they have a fundamental limitation: they are **stateless**. This means they have no memory of past conversations. Every time you start a new chat, the AI is a blank slate.

This leads to the **Context Continuity Problem**:

-   **Constant Repetition:** You have to re-explain your goals, persona, and background information in every single session.
-   **Lost Nuance:** Complex, multi-session projects lose fidelity as key details are forgotten.
-   **User Frustration:** The burden of maintaining context falls entirely on you, the user.

A Dropin solves this by giving you a simple, user-controlled way to inject a complete "state packet" at the start of any conversation, ensuring the AI is instantly aware and aligned with your project.

## Project Documentation

This project is organized into the following documents:

-   **[The Protocol Specification](./SPECIFICATION.md):** The complete v1.1 ruleset, syntax, and guiding principles.
-   **[Examples in Action](./EXAMPLES.md):** A growing list of practical, copy-paste examples.
-   **[Known AI Behaviors & Limitations](./KNOWN_BEHAVIORS.md):** A crucial guide to handling cross-platform inconsistencies.

    -   *Example:* `URL=https://github.com/gripper36/AI-dropin-spec` should be parsed correctly, with the AI understanding that `//` is part of the URL, not a protocol feature.

4.  **Whitespace Pre-processing:** The receiving AI **MUST** strip all transmission artifacts (like extra line breaks or tabs between sections) to reconstitute the dropin before parsing.
