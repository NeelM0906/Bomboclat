# Document Retrieval System with Hybrid RAG

A persistent document storage and retrieval system for Clawdbot that enables intelligent querying of uploaded documents using hybrid search (keyword + semantic).

## Features

### 🔄 Persistent Document Storage
- Documents remain accessible across multiple conversations
- Automatic text extraction from multiple formats (PDF, DOCX, TXT, MD)
- User-specific document isolation for privacy
- Organized filesystem-based storage with JSON indexing

### 🔍 Hybrid Search Architecture
- **Sparse Retrieval (TF-IDF)**: Fast keyword-based matching for exact terms
- **Dense Retrieval (Embeddings)**: Semantic understanding using Sentence Transformers
- **Weighted Scoring**: 40% keyword + 60% semantic for optimal results
- Cosine similarity for vector comparison

### 📄 Smart Text Processing
- Fixed-window chunking (500 words with 100-word overlap)
- Context preservation at chunk boundaries
- Efficient processing of large documents
- Automatic metadata tracking (filename, upload time, size, chunks)

### 🧠 Semantic Understanding
- Goes beyond keyword matching to understand meaning
- Finds "physician" when you search for "doctor"
- Handles synonyms and related concepts automatically
- Uses `Xenova/all-MiniLM-L6-v2` embedding model


## Usage

### Uploading Documents

Simply send any supported file to the bot via WhatsApp. The system will:
1. Extract text content
2. Chunk the document into manageable pieces
3. Generate embeddings for semantic search
4. Build keyword index for fast matching
5. Store everything permanently


## Architecture

### Data Flow
```
Upload → Extract → Chunk → Index → Store
                            ↓
                    [Dual Index]
                   /           \
            TF-IDF          Embeddings
           (Sparse)          (Dense)
                   \           /
                    ↓         ↓
Query → Hybrid Search → Combine → Rank → Return
```

### Storage Structure
```
document_storage/
├── index.json                              # Metadata index
├── [userId]_[timestamp]_[filename].txt     # Document text
└── embeddings/
    └── [userId]_[timestamp]_[filename].json # Chunk embeddings
```


# UNBLINDED MASTERY DATA INTEGRATION

WhatsApp bot with Clawdbot integration and RAG document retrieval.


## Quick Start

### 1. Clone Repository
```bash
git clone https://github.com/NeelM0906/Bomboclat.git
cd Bomboclat
```

### 2. Install Dependencies
```bash
pnpm install
pip install pinecone openai
```

### 3. Setup Skills

**Windows:**
```bash
setup-skills.bat
```

**Linux/Mac:**
```bash
chmod +x setup-skills.sh
./setup-skills.sh
```

This copies the Unblinded Knowledge skill to your Clawdbot workspace.

### 4. Configure API Keys

**Pinecone API Key:**
- Get from: https://app.pinecone.io/ → API Keys

**OpenAI API Key:**
- Get from: https://platform.openai.com/api-keys

**Set environment variables:**

**Windows:**
```bash
set PINECONE_API_KEY=your-pinecone-key
set OPENAI_API_KEY=your-openai-key
```

**Linux/Mac:**
```bash
export PINECONE_API_KEY=your-pinecone-key
export OPENAI_API_KEY=your-openai-key
```

**Make permanent (optional):**

**Windows:**
```bash
setx PINECONE_API_KEY "your-key"
setx OPENAI_API_KEY "your-key"
```

**Linux/Mac:**
```bash
echo 'export PINECONE_API_KEY="your-key"' >> ~/.bashrc
echo 'export OPENAI_API_KEY="your-key"' >> ~/.bashrc
source ~/.bashrc
```

### 5. Run Clawdbot
```bash
pnpm clawdbot gateway --port 18789
```

### 6. Connect WhatsApp

Follow the QR code prompt to link your WhatsApp.

### 7. Use Unblinded Knowledge

On WhatsApp:
```
"activate unblinded knowledge"
"What is Sean's teaching methodology?"
```



## Project Structure
```
Bomboclat/
├── setup-skills.bat          # Windows skill installer
├── setup-skills.sh           # Linux/Mac skill installer
├── skills/
│   ├── document-store/       # Document RAG system
│   └── unblinded-knowledge/  # Pinecone knowledge base
│       ├── SKILL.md          # Clawdbot skill definition
│       ├── query.py          # Pinecone query script
│       └── README.md         # Skill documentation
├── clawdbot.config.js        # Clawdbot configuration
└── README.md                 # This file
```

## 📖 Usage

### Activate Mode
```
You: "activate unblinded knowledge"
Bot: ✅ Unblinded Knowledge Mode activated.
     I will ONLY use Pinecone ublib2, ignoring uploaded documents.
```

### Query the Knowledge Base
```
You: "What is predictive diagnostics?"

Bot: 🔍 Pinecone Query: "predictive diagnostics"

     📊 Results (Avg Relevance: 0.42):
     
     Result 1 (0.49): Predictive Diagnostic asks whether 
     teaching creates lasting installation vs temporary 
     understanding...
     
     Result 2 (0.38): The question is not "How efficiently 
     did I transfer information?" but "Did I create emotional 
     grounding..."
     
     💡 Based on Pinecone ublib2:
     Predictive Diagnostics is a framework that evaluates 
     whether teaching creates emotional grounding that anchors 
     information to feeling.
```

### Exit Mode
```
You: "exit unblinded mode"
Bot: ✅ Normal mode restored. I can now use all knowledge sources.
```

---

## 🎓 Example Queries

### High Relevance (Work Well)
```
"What is predictive diagnostics?"
"What is Sean's teaching philosophy?"
"What is ACTi company culture?"
"What is the hierarchy of progressive yeses?"
"How does Sean approach teaching?"
```