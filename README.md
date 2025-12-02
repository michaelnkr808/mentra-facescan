# Visage

A face recognition memory assistant for MentraOS. Helps you remember people you've met by recognizing faces and storing context about your interactions.

> *Visage* (n.) — a person's face, with reference to the form or features.

## What It Does

```
Meet someone → "What's your name?" → 📸 Photo captured
                                           ↓
                                    🔍 Face detected
                                           ↓
                                    🧠 Face → 128D vector
                                           ↓
                                    💾 Stored in database
                                           ↓
              See them again → 🎯 Face matched → "That's Sarah! You met at the coffee shop."
```

## Features

- **Voice-activated photo capture** — Say "What's your name?" to snap a photo
- **Face detection & recognition** — Uses DeepFace with Facenet model
- **Vector similarity search** — pgvector finds matching faces in milliseconds
- **Relationship memory** — Tracks names, conversation context, when you met, how many times
- **Audio feedback** — Text-to-speech responses through MentraOS

## Tech Stack

| Component | Technology |
|-----------|------------|
| Runtime | [Bun](https://bun.sh) |
| Backend | Python / FastAPI |
| Database | PostgreSQL + pgvector |
| Face Detection | DeepFace (Facenet) |
| ORM | SQLAlchemy + Alembic |
| Device SDK | @mentra/sdk |

## Prerequisites

- [Bun](https://bun.sh) v1.3.3+
- Python 3.11+
- PostgreSQL 17+ with pgvector extension
- MentraOS device with camera/audio

## Setup

### 1. Clone & Install Dependencies

```bash
git clone <your-repo-url>
cd mentra-facescan

# Frontend/SDK
bun install

# Backend
cd backend
python -m venv venv
source venv/bin/activate
pip install -r requirements.txt
```

### 2. Database Setup

```bash
# Create database
createdb watcher_photos_db

# Enable pgvector extension
psql watcher_photos_db -c "CREATE EXTENSION IF NOT EXISTS vector;"

# Run migrations
cd backend/app
alembic upgrade head
```

### 3. Environment Variables

Create `.env` in project root:
```env
PACKAGE_NAME=visage
MENTRAOS_API_KEY=your-api-key
PORT=3000
```

Create `backend/.env`:
```env
DATABASE_URL=postgresql+psycopg2://your-username@localhost:5432/watcher_photos_db
```

## Running the App

```bash
# Terminal 1: Start backend
cd backend
source venv/bin/activate
uvicorn app.main:app --reload

# Terminal 2: Start frontend
bun run index.ts
```

## Database Schema

```
┌─────────────┐     ┌──────────────────┐     ┌────────────────┐
│   Photo     │────▶│  DetectedFace    │────▶│  FaceEncoding  │
│             │     │                  │     │                │
│ - image     │     │ - bounding box   │     │ - 128D vector  │
│ - filename  │     │ - cropped face   │     │ - model name   │
│ - timestamp │     │ - confidence     │     └────────────────┘
└─────────────┘     └──────────────────┘
       │                    │
       ▼                    ▼
┌─────────────┐     ┌──────────────────┐
│ Transcript  │     │   PersonInfo     │
│             │     │                  │
│ - raw_text  │     │ - name           │
│ - extracted │     │ - context        │
│   name      │     │ - first_met_at   │
└─────────────┘     │ - last_seen_at   │
                    │ - times_met      │
                    └──────────────────┘
```

## Voice Commands

| Command | Action |
|---------|--------|
| "What's your name?" | Captures photo and starts face recognition flow |

## How Face Matching Works

1. **Detection**: DeepFace finds faces in the photo and extracts bounding boxes
2. **Encoding**: Each face is converted to a 128-dimensional vector using Facenet
3. **Storage**: Vectors are stored in PostgreSQL using pgvector
4. **Matching**: New faces are compared using L2 distance (Euclidean)
5. **Threshold**: Faces with distance < 0.6 are considered a match

```python
# The magic query
FaceEncoding.encoding.l2_distance(query_encoding) < 0.6
```

## Troubleshooting

### pgvector not available
```bash
# macOS with Homebrew PostgreSQL
brew install pgvector
brew services restart postgresql@17
```

### Database connection fails
- Check `backend/.env` has correct `DATABASE_URL`
- Ensure PostgreSQL is running: `brew services list`
- Verify database exists: `psql -l | grep watcher`

### Face detection not working
- Ensure DeepFace models are downloaded (happens on first run)
- Check image quality — needs clear, front-facing faces

## Project Structure

```
mentra-facescan/
├── src/                    # TypeScript frontend (MentraOS SDK)
│   ├── index.ts
│   ├── api-client.ts
│   └── config.ts
├── backend/
│   └── app/
│       ├── main.py         # FastAPI entry point
│       ├── models/         # SQLAlchemy models
│       ├── routes/         # API endpoints
│       ├── services/       # Business logic
│       │   ├── database.py
│       │   └── face_detection.py
│       └── utils/
└── package.json
```

## License

MIT
