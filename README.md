# The AI Context Drop-in Specification

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## The Problem: "Where did our conversation go?"

Have you ever had a detailed, productive conversation with an AI, carefully setting up context and objectives, only to have it disappear from your history due to a glitch, a closed tab, or a session timeout?

Starting over from scratch is frustrating and inefficient. We have "save files" for our documents, spreadsheets, and games. We need a universal "save file" for our AI conversations.

## The Solution: A Standardized Context File

This repository defines a lightweight, portable standard for capturing the essential context of an AI session into a single file. This allows any compliant AI to pick up a conversation exactly where you left off.

We call this the **QAI (Queryable AI) specification**, and its official file extension is **`.qai`**.

### Core Principles

*   **Machine-Readable:** The syntax is strict and designed for easy parsing by software, not for casual human reading.
*   **Portable:** A `.qai` file is just text. It can be saved, shared, version-controlled, and used with any AI system that supports the spec.
*   **Extensible:** The key-value structure allows for the addition of new contextual data over time without breaking the standard.
*   **Lightweight:** The format is simple and avoids the complexity of formats like JSON or XML for maximum ease of implementation.

---

## The QAI Specification (Version 1.0)

### 1. File Extension

The official and recommended file extension is **`.qai`**.

### 2. Syntax Rules

The `.qai` format is a single line of text following these strict rules:

| Element | Rule | Example |
| :--- | :--- | :--- |
| **Whitespace** | No whitespace is permitted. | `key_value` (not `key value`) |
| **Spaces** | Replaced with an underscore (`_`). | `next_steps` |
| **Key/Value Pairs** | Separated by an equals sign (`=`). | `objective=some_goal` |
| **Lists** | Comma-separated values (`comma`). | `methods=method1,method2` |
| **Sections** | Top-level concepts are separated by a period (`.`). | `objective=foo.methods=bar` |
| **Topics** | Key and sub-key are separated by a colon (`:`). | `AI_identifier:gem2.5` |
| **Groups** | Sub-items are grouped with parentheses (`()`). | `methods(successful/failed)` |
| **Alternatives** | Alternative options are separated by a slash (`/`). | `successful/failed` |

### 3. Capture Data

A compliant `.qai` file should aim to capture the following data sections:

*   `objective`: The primary goal of the conversation.
*   `methods(successful/failed)`: A list of approaches that were tried.
*   `requirements`: Specific constraints or needs for the task.
*   `specs`: Technical specifications or format rules.
*   `preferences`: User preferences that guide the AI's behavior.
*   `lessons`: Key takeaways or discoveries from the conversation.
*   `next_steps`: The immediate action items to be addressed.

---

## Example `.qai` File

Here is a real-world example: the original prompt used to bootstrap the development of this very specification.

```qai
gem2.5:Dropin_Generation_Spec:Action=Generate_context_dropin_from_conversation.Syntax_Rules:whitespace=none,spaces=underscore,key_value=equals,lists=comma,sections=period,topics=colon,groups=parentheses,alternatives=slash.Capture_Data:objective,methods(successful/failed),requirements,specs,preferences,lessons,next_steps.Execution_Directives:Source_of_Truth=https://github.com/gripper36/AI-dropin-spec,Attribution=prepend_AI_identifier(e.g.,gem2.5),Self_Correction=avoid_past_failures(no_human-readable_formatting/strict_adherence_to_spec).Final_Output=starter_document_for_new_AI_session.
