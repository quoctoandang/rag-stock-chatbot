# 🤖 RAG Stock Chatbot

> Intelligent chatbot for Vietnamese Stock Market Q&A using Retrieval-Augmented Generation (RAG)

## 📋 Overview

**RAG Stock Chatbot** is an AI-powered conversational assistant designed to answer questions about the Vietnamese stock market. It leverages Retrieval-Augmented Generation (RAG) architecture with LangChain, Google Gemini LLM, and multiple vector stores to provide accurate, contextual responses based on real stock market news and data.

## ✨ Key Features

- 🧠 **RAG Architecture** - Retrieves relevant documents before generating answers
- 💬 **Conversational Memory** - Maintains chat history using Redis
- 🔍 **Multi-Embedding Support**
  - BiEncoder embeddings
  - PhoBERT (Vietnamese BERT)
  - Sentence-BERT
- 📊 **Vector Search** - Qdrant vector database for semantic search
- 🗄️ **Document Storage** - MongoDB for document persistence
- 🌐 **RESTful API** - FastAPI endpoints for easy integration
- 🐳 **Dockerized** - Complete Docker Compose setup
- 🇻🇳 **Vietnamese Language** - Optimized for Vietnamese stock market terminology

## 🛠️ Technology Stack

| Component | Technology |
|-----------|------------|
| **LLM** | Google Gemini 1.5 Pro |
| **Framework** | LangChain |
| **API** | FastAPI |
| **Vector DB** | Qdrant |
| **Document Store** | MongoDB |
| **Cache/Memory** | Redis |
| **Embeddings** | PhoBERT, Sentence-Transformers |
| **Containerization** | Docker, Docker Compose |

## 📁 Project Structure

```
rag-stock-chatbot/
├── chatbot_rag/
│   ├── app/
│   │   ├── app.py              # FastAPI application
│   │   ├── chatbot.py          # Main chatbot logic
│   │   ├── rag_chain.py        # RAG chain configuration
│   │   ├── retriever.py        # Document retriever
│   │   ├── store.py            # Vector & document stores
│   │   ├── embedding.py        # Embedding models
│   │   ├── requirements.txt    # Python dependencies
│   │   └── Dockerfile          # App container config
│   └── docker-compose.yaml     # Multi-container setup
├── CreatImage/                 # Image creation utilities
├── Data/                       # Stock market datasets
│   ├── data_thanhnien.csv
│   ├── data_tnck.csv
│   ├── data_VNE.csv
│   └── data_vne1.csv
└── README.md
```

## 🗃️ Architecture

### RAG Pipeline

```
User Question
    ↓
History-Aware Retriever
    ↓
Vector Search (Qdrant)
    ↓
Retrieved Documents
    ↓
LLM (Gemini) + Context
    ↓
Generated Answer
```

### Components

1. **Retriever** - Fetches relevant documents from vector store
2. **RAG Chain** - Combines retrieval and generation
3. **Chat History** - Redis-based conversation memory
4. **Vector Store** - Qdrant with multiple embedding models
5. **Document Store** - MongoDB for raw documents

## 🚀 Getting Started

### Prerequisites
- Docker & Docker Compose
- Python 3.9+
- Google Gemini API Key

### Installation

1. **Clone the repository**
```bash
git clone <repository-url>
cd rag-stock-chatbot/chatbot_rag
```

2. **Set up environment variables**
Create `.env` file:
```env
API_KEY=your_gemini_api_key_here
MONGO_URL=mongodb://mongodb:27017
QDRANT_URL=http://qdrant_db:6333
REDIS_URL=redis://redis:6379
```

3. **Start services with Docker Compose**
```bash
docker-compose up -d
```

This will start:
- **Qdrant** (port 6333) - Vector database
- **MongoDB** (port 27017) - Document store
- **Redis** (port 6379) - Chat history cache
- **FastAPI App** (port 5123) - Chatbot API

### Running without Docker

1. **Install dependencies**
```bash
pip install -r app/requirements.txt
```

2. **Start individual services**
```bash
# Start Qdrant, MongoDB, Redis separately
# Then run the app
python app/app.py
```

## 📡 API Endpoints

### Chat with Bot
```http
POST /chat/{user_id}/{session_id}?question=your_question
```

**Parameters:**
- `user_id`: Unique user identifier
- `session_id`: Conversation session ID
- `question`: User's question

**Response:** Streaming text response

### Get Chat History
```http
GET /chat/history/{user_id}/{session_id}
```

**Response:** Full conversation history

### Clear Chat History
```http
POST /chat/clear/{user_id}/{session_id}
```

**Response:** Status confirmation

### Insert Data
```http
PUT /insert_data?csv_path=/path/to/data.csv
```

**Response:** Data insertion status

## 💡 Usage Examples

### Python Client
```python
import requests

# Ask a question
response = requests.post(
    "http://localhost:5123/chat/user123/session456",
    params={"question": "Giá cổ phiếu VNM hôm nay như thế nào?"}
)

# Stream the response
for chunk in response.iter_content(chunk_size=1024):
    print(chunk.decode('utf-8'), end='')
```

### cURL
```bash
# Ask question
curl -X POST "http://localhost:5123/chat/user123/session456?question=Thị%20trường%20chứng%20khoán%20hôm%20nay"

# Get history
curl -X GET "http://localhost:5123/chat/history/user123/session456"

# Clear history
curl -X POST "http://localhost:5123/chat/clear/user123/session456"
```

## 🔧 Configuration

### RAG Chain Settings
```python
# rag_chain.py
MODEL_NAME = 'gemini-1.5-pro-latest'
TEMPERATURE = 0.7
MAX_RETRIES = 6
```

### Chatbot Settings
```python
# chatbot.py
COLLECTION_NAME = 'stock-news'
HISTORY_WINDOW_K = 10  # Last 10 messages in context
```

### Embedding Models
- **BiEncoder**: For dense retrieval
- **PhoBERT**: Vietnamese BERT model
- **Sentence-BERT**: Multilingual embeddings

## 📊 Data Sources

The chatbot uses Vietnamese stock market news from:
- Thanh Niên (data_thanhnien.csv)
- Tuổi Trẻ Chứng Khoán (data_tnck.csv)
- VNExpress (data_VNE.csv, data_vne1.csv)

### Data Format
```csv
title,content,link,date,source
```

## 🎯 Key Features Explained

### History-Aware Retrieval
- Maintains conversation context
- Reformulates queries based on chat history
- Provides coherent multi-turn conversations

### Multi-Vector Store
- Three separate vector stores with different embeddings
- Allows comparison and selection of best embedding model
- Optimized for Vietnamese language

### Streaming Responses
- Real-time response generation
- Better user experience
- Reduced perceived latency

## 🐳 Docker Services

### Qdrant
```yaml
ports: 6333:6333, 6334:6334
volumes: ./qdrant_data:/qdrant_data
```

### MongoDB
```yaml
ports: 27017:27017
volumes: ./mongo_data:/data
```

### Redis
```yaml
ports: 6379:6379
volumes: ./redis_data:/data
```

## 🔍 Troubleshooting

### Common Issues

1. **Connection errors**
   - Ensure all Docker containers are running
   - Check network connectivity between services

2. **API Key errors**
   - Verify Gemini API key is correct
   - Check API quota limits

3. **Memory issues**
   - Increase Docker memory allocation
   - Reduce embedding model size

4. **Slow responses**
   - Check vector store index size
   - Optimize chunk size in text splitters

## 📈 Performance Tips

- Use appropriate chunk sizes for text splitting
- Implement caching for frequent queries
- Monitor vector store size and optimize regularly
- Use streaming for better UX

## 📝 License

This project is developed for educational and research purposes.

## 🤝 Contributing

Contributions are welcome! Feel free to:
- Report bugs
- Suggest new features
- Submit pull requests
- Improve documentation

## 📚 References

- [LangChain Documentation](https://python.langchain.com/)
- [Qdrant Vector Database](https://qdrant.tech/)
- [Google Gemini API](https://ai.google.dev/)
- [PhoBERT](https://github.com/VinAIResearch/PhoBERT)

---
⭐ If you find this project useful, please give it a star!
