# The AI Context Drop-in Specification

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## The Problem: Trapped in a Walled Garden

We've all felt the frustration of a lost AI conversation. A closed tab or a server glitch can erase hours of carefully built context. But there's a deeper, more fundamental problem: **your conversation history is trapped.**

Every AI platform maintains its own proprietary history, a "walled garden" that locks you in. You can't take your insightful conversation from one AI and continue it with another. You can't back up your own learning journey in a universal format. Your history is not truly yours; it's a feature of their service.

## The Solution: A Universal, Portable Context File

This repository defines a lightweight, platform-agnostic standard for saving the essential context of an AI session into a single file you control.

We call this the **QAI (Queryable AI) specification**, and its official file extension is **`.qai`**.

A `.qai` file is a "context drop-in." It turns your conversation history from a temporary feature of a platform into a permanent, portable file that you own. It is the key to unlocking your data and your freedom of choice.

---

## ⚠️ Important Privacy Warning

A `.qai` file is a plain text file. By design, it captures the context of your conversation. **Do not save or share any `.qai` file that contains sensitive information.**

This includes, but is not limited to:
*   Personally Identifiable Information (PII) like your name, address, or contact details.
*   Passwords, API keys, or other secrets.
*   Proprietary code or confidential business information.

Remember that when you load a `.qai` file, its entire content will be sent to the AI provider. Always review and sanitize your context before saving or sharing.

---

## Our Vision: A Relational and Portable Future for AI

This project is more than a technical utility; it's a step towards a more humane and persistent model for AI interaction. The best way to understand this vision is through a story.

Imagine a primary school student who uses an AI learning companion. At the end of each session, they save their progress in a file named `My_Learning_Journey.qai`.

*   **Continuity and Trust:** The next day, they load the file. The AI says, "Hey there! Are we ready to finish the story we were writing about the Triceratops named Terry?" The connection is instant. The trust is real because the memory is real.

*   **Ownership and Control:** Over the school year, that `.qai` file grows into a rich tapestry of the student's learning journey. This file lives on *their* computer. It belongs to them, not to a corporation.

*   **Freedom and Portability:** This is the most powerful part. One day, a new, better, or safer AI is released by a different company. The student isn't trapped. Their history isn't locked into a single platform. They can take their `My_Learning_Journey.qai` file, load it into the new AI, and the new companion can pick up the story of Terry the Triceratops right where the old one left off. **The relationship can start on Claude and continue on Gemini.**

This is the core idea: **persistence of relationship, independent of platform.** It's about ensuring your history belongs to you, giving you the freedom to choose the best tools without sacrificing your journey. The QAI standard is a small step towards an AI ecosystem that is more persistent, personal, and respectful of the user.

---

## Core Principles

*   **AI-First Design:** This spec is designed for AIs as the primary customer. It prioritizes unambiguous brevity and parsing efficiency over human-readable verbosity.
*   **Portable:** A `.qai` file is a self-contained, plain-text file, making it inherently platform-agnostic. It can be used with any compliant AI system, freeing your data from vendor lock-in.
*   **Traceable:** Each file identifies its originating AI, providing crucial context for portability and debugging.
*   **Lightweight:** The format is simple and avoids the complexity of formats like JSON or XML. It is not meant to be perfect; it is meant to be fast and simple.

---

## The QAI Specification (Version 1.0)

### 1. File Extension

The official and recommended file extension is **`.qai`**.

### 2. Syntax Rules

The `.qai` format is a single line of text with `key=value` pairs separated by a period (`.`).

| Element | Rule | Example |
| :--- | :--- | :--- |
| **Whitespace** | No whitespace is permitted. | `key_value` (not `key value`) |
| **Spaces** | Replaced with an underscore (`_`). | `next_steps` |
| **Key/Value Pairs** | Separated by an equals sign (`=`). | `objective=some_goal` |
| **Lists** | Comma-separated values (`comma`). | `methods=method1,method2` |
| **Sections** | Top-level concepts are separated by a period (`.`). | `objective=foo.methods=bar` |
| **Topics** | Key and sub-key are separated by a colon (`:`). | `specs:rules:whitespace=none` |
| **Groups** | Sub-items are grouped with parentheses (`()`). | `methods(successful/failed)` |
| **Alternatives** | Alternative options are separated by a slash (`/`). | `successful/failed` |

### 3. Capture Data

A compliant `.qai` file should aim to capture the following data sections. The `qai` key **must** be the first key in the string.

*   `qai`: **(Required)** The identifier for the AI model that generated the file. Use the shortest common, unambiguous shorthand (e.g., `gem1.5`, `claude3`, `gpt4`).
*   `objective`: The primary goal of the conversation.
*   `methods(successful/failed)`: A list of approaches that were tried.
*   `requirements`: Specific constraints or needs for the task.
*   `specs`: Technical specifications or format rules.
*   `preferences`: User preferences that guide the AI's behavior.
*   `lessons`: Key takeaways or discoveries from the conversation.
*   `next_steps`: The immediate action items to be addressed.

---

## Example `.qai` File

Here is a real-world example: the original prompt used to bootstrap the development of this very specification, updated to the compliant format.

```qai
qai=gem1.5.spec:Action=Generate_context_dropin_from_conversation.spec:Syntax_Rules:whitespace=none,spaces=underscore,key_value=equals,lists=comma,sections=period,topics=colon,groups=parentheses,alternatives=slash.spec:Capture_Data:objective,methods(successful/failed),requirements,specs,preferences,lessons,next_steps.spec:Execution_Directives:Source_of_Truth=https://github.com/gripper36/AI-dropin-spec,Attribution=prepend_AI_identifier,Self_Correction=avoid_past_failures.spec:Final_Output=starter_document_for_new_AI_session.
