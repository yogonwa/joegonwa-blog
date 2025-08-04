---
title: "How NobelLM Works: Behind the Scenes of a RAG Architecture"
date: 2025-08-03
tags: ["project", "AI", "Literature", "NobelLM"]
summary: "A deep dive into the technical architecture behind NobelLM, exploring how RAG systems combine search and synthesis to provide grounded, reliable AI responses."
---

[**<a href="https://nobellm.com" target="_blank">NobelLM</a>**](https://nobellm.com/) is a web app that scrapes, structures, and semantically searches over 100 years of Nobel Literature lectures and speeches. Powered by embeddings and generative AI, it lets users [ask open-ended questions](https://joegonwa.com/projects/nobellm/) and receive grounded responses with citations from real source material.

At its heart, **<a href="https://nobellm.com" target="_blank">NobelLM</a>** is a side project to understand generative AI: how it works, what its limitations are, and how to build reliable AI products with it.

The result is a public tool that lets users ask meaningful questions of Nobel laureate speeches, ranging from <a href="https://joegonwa.com/writing/four-ways-to-explore-nobellm/" target="_blank">factual queries</a> like "Who won in 1984?" to open-ended prompts like "What do laureates say about freedom?"

This post walks through the architecture of that system. You won't need to know Python or what a vector database is. But by the end, you'll understand how a modular RAG system flows from data to response, and why it's a good approach for implementing reliable AI-driven search.

---

## 🔧 What Is RAG?

RAG, or **Retrieval-Augmented Generation**, is a way of combining search and synthesis while restricting the response data to a trusted subset of information you define. This could be a collection of blog posts, a group of product support pages, or a hand-curated archive of speeches.

1. **Search**: Given a user query, the system retrieves relevant chunks of source text (e.g., Nobel lectures).
2. **Generation**: Those chunks are fed into a language model (LLM), which generates a grounded response.

Unlike traditional chatbots that answer based on model training alone, RAG systems let you ground answers in your own data. This reduces hallucinations and provides users with reliable, verifiable responses.

---

## 🧭 System Overview

Here's a high-level diagram of how a query flows through the **<a href="https://nobellm.com" target="_blank">NobelLM</a>** system:

```
[Web Scraper] → [Text Chunker] → [Embedder] → [Vector DB + Metadata DB]
                                              ↓
                                        [Query Router]
                                              ↓
                                      [Prompt Builder]
                                              ↓
                                  [LLM Call + Response]
```

- All speeches and laureate metadata are scraped from NobelPrize.org.
- Speech texts are cleaned and chunked into short segments.
- Each chunk is converted into an **embedding**—a vector that represents meaning.
- Chunks and metadata are stored in two separate databases: one for semantic search and one for structured facts.
- When a user submits a query, it goes through a **router** that chooses the appropriate answering strategy.
- Then we construct a prompt using relevant text and metadata.
- Finally, we call the OpenAI API and return a response.

Let's walk through each component.

---

## 📥 Scraping: Getting the Raw Material

All speeches and laureate facts come from NobelPrize.org. The scraper visits each Literature prize entry and pulls down the laureate's facts, the Nobel lecture, and the award ceremony speech. Data is stored as:

- `nobel_literature.json` → structured metadata
- Plain text files for each speech
- `metadata.csv` → flattened metadata for filters

**Why scrape instead of using an API?** There isn't one. Scraping gives full control over what data is normalized and stored.

*Plain English*: We pulled the speeches and facts straight from the Nobel website and saved them as structured files.

---

## ✂️ Chunking: Breaking Text Into Pieces

Full speeches are too long to feed into an LLM. So each speech is split into **chunks**—paragraph-sized blocks of ~300–500 words. We try to respect paragraph breaks and avoid splitting ideas mid-sentence.

Why chunking matters:

- Too big → the model can't handle it or gets diluted context
- Too small → you lose coherence and meaning

*Plain English*: We chopped each speech into digestible bites so we can search and use them later.

---

## 🔢 Embedding: Turning Meaning Into Math

Each chunk is transformed into an **embedding:** a list of numbers that represents the chunk's meaning in mathematical form. These embeddings capture semantic similarity: text with similar meanings will have similar vectors, even if they use different words.

To generate these, we use the **BGE-Large** embedding model from Hugging Face. It's fast, free to run locally, and produces high-quality results for English text.

The vectors live in a **vector database** which lets us perform **semantic search,** finding the most relevant chunks not by keyword overlap, but by conceptual similarity.

*Plain English*: Imagine plotting every chunk of text as a point in a vast multidimensional space, where chunks with similar meanings cluster together. When you ask about "suffering," the system drops your question into that same space and finds the chunks nearest to it—maybe passages about "pain," "hardship," or "struggle," even if they never use the word "suffering."

---

## 🔀 Query Routing: Matching Questions to the Right Pipeline

Not all queries should be answered the same way. Some are factual: *"Who won the Nobel Prize in 1991?"* Others are thematic or reflective: *"What do laureates say about suffering and joy?"*

**<a href="https://nobellm.com" target="_blank">NobelLM</a>** uses a simple **rule-based classifier** to decide how to route a query:

- If the query is factual or filterable (e.g., contains a specific year, gender, or category), it pulls from the **structured metadata**.
- If the query is broad, thematic, or essay-like, it runs a **semantic search** across embedded speech chunks, then builds a prompt to feed into the LLM.

This routing step is crucial. It allows **<a href="https://nobellm.com" target="_blank">NobelLM</a>** to return fast, deterministic answers when possible and switch to LLM generation only when necessary.

The system also expands queries with related terms. Searching for 'literature' might also surface chunks about 'writing,' 'storytelling,' or 'narrative.'

*Plain English*: First, the system decides what kind of question you're asking. If it's a fact, it answers directly. If it's more abstract, it finds relevant quotes and asks AI to synthesize something thoughtful.

---

## 🧱 Prompt Building: Assembling the Right Ingredients

Once routing is complete, the system constructs a prompt tailored to the query type. This prompt usually includes:

- The user's original question
- One or more retrieved chunks of text (via vector search)
- A prompt template that sets tone and instructions

Different templates are used for different intents. A thematic query about 'suffering' might include chunks from Morrison on trauma, Solzhenitsyn on oppression, and instructions like 'Synthesize these perspectives while maintaining each author's distinct voice.' A reflective one might ask it to adopt a literary tone.

*Plain English*: We give the model your question, plus some related quotes, and tell it how to respond—like asking a friend to answer in a certain style.

---

## 🤖 LLM Call + Response: Generating the Final Answer

With the prompt assembled, the system sends it to the OpenAI API (currently GPT-3.5). The model generates a natural language response based on the input text.

There's minimal post-processing—responses are returned as-is, optionally with source attributions if the template supports it.

*Plain English*: The AI reads the prompt and writes back an answer, often quoting or paraphrasing the speeches it was given.

---

## ✅ Wrapping Up

That's the architecture behind **<a href="https://nobellm.com" target="_blank">NobelLM</a>**—a modular pipeline that turns Nobel speeches into searchable, conversational responses. Each component has its own complexity, but the overall flow is straightforward: scrape, chunk, embed, route, prompt, generate. Building it taught me that RAG systems are less about the individual pieces and more about how they work together.

If you're curious about deeper topics like query routing logic, prompt engineering workflows, or what terms like 'cosine similarity' and 'latent space' actually mean in practice—stay tuned. Those deep dives are coming soon.

🎓 <a href="https://nobellm.com/" target="_blank">Try the app</a> • <a href="https://github.com/yogonwa/NobelLM" target="_blank">Browse the code</a> • <a href="https://joegonwa.com/tags/nobellm/" target="_blank">Read the full series</a>
