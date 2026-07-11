---
"mattpocock-skills": minor
---

Reword how the **`prototype`** skill handles its artifacts around a single idea: **the prototype is a primary source**. Rather than being deleted once it's answered its question, the prototype is captured as runnable evidence on a throwaway branch (`prototype/<name>`) out of main, with a context pointer to it left on the implementation issue — so the main branch keeps only the validated decision while the exploration stays findable. The answer (verdict + question) is still captured durably in an issue/ADR/commit.
