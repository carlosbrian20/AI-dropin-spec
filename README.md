# The AI Context Drop-in Specification

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](https://opensource.org/licenses/MIT)

## The Problem: "Where did our conversation go?"

Have you ever had a detailed, productive conversation with an AI, carefully setting up context and objectives, only to have it disappear from your history due to a glitch, a closed tab, or a session timeout?

Starting over from scratch is frustrating and inefficient. We have "save files" for our documents, spreadsheets, and games. We need a universal "save file" for our AI conversations.

## The Solution: A Standardized Context File

This repository defines a lightweight, portable standard for capturing the essential context of an AI session into a single file. This allows any compliant AI to pick up a conversation exactly where you left off.

We call this the **QAI (Queryable AI) specification**, and its official file extension is **`.qai`**.

---

## ⚠️ Important Privacy Warning

A `.qai` file is a plain text file. By design, it captures the context of your conversation. **Do not save or share any `.qai` file that contains sensitive information.**

This includes, but is not limited to:
*   Personally Identifiable Information (PII) like your name, address, or contact details.
*   Passwords, API keys, or other secrets.
*   Proprietary code or confidential business information.

Remember that when you load a `.qai` file, its entire content will be sent to the AI provider. Always review and sanitize your context before saving or sharing.

---

## Core Principles

*   **Machine-Readable:** The syntax is strict and designed for easy parsing by software, not for casual human reading.
*   **Portable:** A `.qai` file is just text. It can be saved, shared, version-controlled, and used with any AI system that supports the spec.
*   **Extensible:** The key-value structure allows for the addition of new contextual data over time without breaking the standard.
*   **Lightweight:** The format is simple and avoids the complexity of formats like JSON or XML for maximum ease of implementation.

---

## The QAI Specification (Version 1.0)

*(The rest of the document remains the same...)*

### 1. File Extension
(...etc.)
