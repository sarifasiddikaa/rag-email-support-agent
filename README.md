# rag-email-support-agent
AI-powered email customer support agent built in n8n using RAG — retrieves company knowledge, drafts grounded replies, and routes them through human approval before sending.
📬 RAG Email Support Agent
AI that reads your support inbox, thinks before it speaks, and never sends a reply without a human's OK.
Retrieval-Augmented Generation meets email automation meets human-in-the-loop safety — all wired together in n8n.

   


✨ What makes this different
Most "AI email bot" demos answer confidently and hope for the best. This one doesn't.

🔍 It looks things up before it talks. Every reply is grounded in a real vector search over your company's actual policies — not the model's imagination.
🚫 It knows what it doesn't know. No relevant policy found? It says so, flags itself for a human, and drafts a safe holding reply instead of guessing.
🧑‍⚖️ Nothing ships without a human. Every single draft goes to a supervisor for one-click Approve / Reject before the customer ever sees it.
🧵 It threads properly. Replies land in the original conversation, not as a random new email.


🎬 How it works
📥  Customer emails support

      │

      ▼

🧠  RAG Agent searches the knowledge base (vector similarity search)

      │

      ▼

✍️   Drafts a grounded reply + confidence score + category

      │

      ▼

📧  Supervisor gets an email: [ ✅ Approve ]  [ ❌ Reject ]

      │

      ├── ✅ Approved  → reply sent straight into the thread

      └── ❌ Rejected  → saved as a Gmail draft for a human to fix

      │

      ▼

✔️   Email marked handled — never re-processed

Two flows, one workflow:

Flow
Trigger
What happens
🗂️ Knowledge Base Ingestion
Manual, run once (or on policy updates)
Chunks your policy docs → embeds them → stores them in a vector database
✉️ Live Support Handling
New unread email
Retrieves relevant chunks → drafts a reply → waits for approval → sends



🛠️ Stack
Piece
Tool
🧩 Orchestration
n8n
✉️ Inbox
Gmail API
🧠 Brain
OpenAI gpt-5-mini
🔢 Embeddings
OpenAI text-embedding-3-small
🗃️ Vector store
n8n Simple Vector Store (swap in Pinecone / Qdrant / ChromaDB for production)
🔗 RAG glue
LangChain (via n8n's native AI nodes)
✅ Approval
Gmail sendAndWait — no dashboard needed



📂 What's in this repo
rag-email-support-agent/

├── README.md

└── workflow/

    └── rag-email-support-agent.json     ← import this straight into n8n


🚀 Get it running
1. Import the workflow      → n8n: Workflows → Import from File

2. Connect Gmail + OpenAI   → OAuth2 + API key

3. Set your supervisor's email → "Ask Supervisor to Approve" node

4. Paste your real policies → "Company Knowledge Base" node

5. Run ingestion once       → "Run Once to Index Knowledge Base" trigger

6. Scope the inbox trigger  → e.g. to:support@yourcompany.com

7. Activate → send a test email → watch it work


💬 See it in action
Customer:

"Hi, I bought the headphones 3 weeks ago and I'd like to return them. Do I still qualify?"

Agent (before it ever reaches the customer):

"Hi Rahim, yes — you're still within our 30-day return window. Return the item in its original packaging and we'll refund you to your original payment method within 5–7 business days once it arrives..."

{

  "category": "returns",

  "confidence": 0.92,

  "needs_human": false,

  "sources_used": "Returns and Refunds policy"

}

No match in the knowledge base? The agent flips needs_human to true, keeps the reply short and non-committal, and lets a person take it from there — instead of making something up.


⚠️ Good to know
🎲 RAG reduces hallucination, it doesn't erase it — that's what the approval step is for
🔄 The knowledge base is a snapshot — re-run ingestion whenever policies change
🔒 Email content is sent to a third-party LLM API — plan for that in regulated environments
💾 The demo vector store is in-memory — go persistent (Pinecone/Qdrant/pgvector) for production



Built with n8n · Powered by RAG · Guarded by a human

