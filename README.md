🤖 Built a RAG-powered Document Q&A Bot via WhatsApp — fully self-hosted, zero cloud dependency
I just finished building a system where you can send any PDF document to a WhatsApp number, and then ask questions about it in natural language — getting accurate, context-aware answers back in seconds. No OpenAI. No paid APIs. Everything runs locally on my laptop.
How it works:
📄 Upload flow (PDF → Knowledge Base):
1️⃣ User sends a PDF via WhatsApp
2️⃣ Evolution API captures the document and forwards it to n8n via webhook
3️⃣ n8n decodes the media, converts it to a binary file, and extracts the raw text
4️⃣ The text is chunked and converted into vector embeddings using Ollama's nomic-embed-text model
5️⃣ Vectors are stored in Qdrant — a self-hosted vector database running in Docker
💬 Q&A flow (Question → Answer):
1️⃣ User sends a text question via WhatsApp
2️⃣ n8n routes it to an AI Agent (LangChain-based)
3️⃣ The agent queries Qdrant for the most semantically relevant chunks
4️⃣ Retrieved context + question are passed to Ollama (llama3.2) for answer generation
5️⃣ The answer is sent back to the user's WhatsApp automatically
Tech stack (100% self-hosted & free):
🔹 n8n — workflow orchestration
🔹 Evolution API — WhatsApp gateway via Baileys protocol
🔹 Ollama — local LLM (llama3.2) + embedding model (nomic-embed-text)
🔹 Qdrant — vector database for semantic search
🔹 Docker — containerized infrastructure (Postgres, Redis, Evolution API, Qdrant)
🔹 LangChain AI Agent — tool-based reasoning via n8n's AI Agent node
The debugging journey (the real learning):
❌ IF node kept throwing type mismatch errors — messageType returned an object instead of a string, had to switch comparison strategy entirely
❌ Qdrant container kept shutting down silently after laptop sleep — lost all vector data on restart, had to rebuild the collection and re-insert documents multiple times before understanding persistence behavior
❌ AI Agent had no idea what document to search — turns out Chroma Vector Store (which I initially used) wasn't compatible with my setup; migrated to Qdrant and rewired all embedding connections
❌ HTTP Request kept returning "Bad Request" when sending WhatsApp replies — the remoteJid field contained @s.whatsapp.net suffix that Evolution API rejected as a phone number; had to parse and strip it correctly
❌ Item pairing errors across n8n nodes — AI Agent breaks the normal item context chain, requiring an intermediate "Edit Fields" node to capture and pass values downstream
❌ JSON body injection errors — AI-generated text containing quotes and newlines broke the raw JSON body; fixed by switching to "Using Fields Below" mode so n8n handles escaping automatically
✅ After systematically isolating each layer — infrastructure → webhook → data pipeline → AI retrieval → response delivery — everything clicked into place.
Key takeaway: RAG isn't just a prompt engineering trick. It's an infrastructure problem. Getting the data ingestion pipeline, vector storage, embedding consistency, and retrieval accuracy all working together requires understanding each layer deeply — not just connecting nodes.
This project pushed me into real AI engineering territory: binary file handling, vector databases, embedding models, LangChain agent tool design, and multi-step async debugging across 6+ interconnected services.
Next up: expanding this into a multi-document knowledge base with source attribution, and eventually deploying the full stack to a VPS for 24/7 availability.

#RAG #n8n #Ollama #Qdrant #WhatsApp #AIAutomation #SelfHosted #Docker #LangChain #VectorDatabase #AIEngineering #PortfolioProject
