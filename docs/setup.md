  Recommended setup

  LLM:         free hosted API (Gemini free tier or Groq)   ← the "smart" part
  Embeddings:  local MiniLM / bge-small on the CPU
  Vector DB:   Chroma or LanceDB, local
  Orchestration: LangChain + LangGraph
  Observability: Langfuse Cloud Hobby