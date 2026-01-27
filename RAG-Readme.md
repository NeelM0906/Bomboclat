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

**Supported formats:**
- PDF (`.pdf`)
- Microsoft Word (`.docx`)
- Plain text (`.txt`)
- Markdown (`.md`)

### Searching Documents

Use explicit trigger phrases to search your documents:
```
"Search my documents for patient intake workflow"
"What do my documents say about Dr. Kumar?"
"Based on my uploads, what is the hierarchy of progressive yeses?"
"Use my files to explain the ecosystem"
"Check my documents for information about Unblinded Health"
```

### Normal Conversation

For general questions that don't require document search, just ask normally:
```
"How are you?"
"What's the weather like?"
"Explain quantum mechanics"
```

The bot will only search documents when you explicitly request it.

### Managing Documents
```
"List my documents"
"What documents do I have?"
"Show my uploaded files"
```

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

### Chunking Strategy

- **Method:** Fixed-size sliding window
- **Chunk size:** 500 words
- **Overlap:** 100 words
- **Purpose:** Preserve context at boundaries while maintaining manageable chunk sizes

### Hybrid Search Weights

- **Keyword (TF-IDF):** 40%
  - Fast exact matching
  - Good for specific terminology
  - Catches rare/unique terms

- **Semantic (Embeddings):** 60%
  - Understands meaning and context
  - Handles synonyms and related concepts
  - Better recall for conceptual queries

### Embedding Model

- **Model:** `Xenova/all-MiniLM-L6-v2`
- **Dimensions:** 384
- **Type:** Sentence Transformer
- **Similarity:** Cosine similarity (threshold > 0.3)

## Technical Details

### Key Technologies

- **Text Extraction:**
  - `pdf-parse` - PDF processing
  - `mammoth` - DOCX processing
  - Node.js `fs` - TXT/MD reading

- **Search & Retrieval:**
  - `natural` - TF-IDF implementation
  - `@xenova/transformers` - Embedding generation
  - Custom cosine similarity implementation

- **Storage:**
  - Filesystem-based storage
  - JSON metadata indexing
  - In-memory caching for performance

### Performance Characteristics

- **First upload:** Slower (downloads embedding model ~90MB)
- **Subsequent uploads:** Fast (model cached)
- **Search latency:** <1s for typical queries
- **Storage:** ~1.5x original document size (text + embeddings)
- **Scalability:** Suitable for <1000 documents per user

