RAG Project
===========

A retrieval-augmented generation (RAG) service focused on fall-prevention
and healthy-aging content.

------------------------------------------------------------
Project Overview
------------------------------------------------------------

The system performs:

- Retrieval of candidate documents from MongoDB using BM25.
- Optional reranking with a cross-encoder model.
- Relevance evaluation before generation.
- Web fallback search (SerpApi + extraction pipeline) when local relevance is low.
- Answer generation using one of three LLM backends:
    • Gemini
    • OpenAI
    • DeepSeek (Ollama-compatible endpoint)

------------------------------------------------------------
Folder Structure
------------------------------------------------------------

1_Code/          Backend, retrieval, evaluation, UI, two service files
2_Data/          Curated corpus and metadata
3_Documents/     Final report and presentation slides
4_LaTeX_Source/  Project report LaTeX source files

Detailed Project Modules:

- BM25/                BM25 retrieval, cache loading/building, reranking logic
- LLM_models/          Gemini and OpenAI generation adapters
- Google_API/          Web search and page extraction fallback
- MongoDB/             PDF ingestion and MongoDB utilities
- Quantitive_Relevant/ Relevance scoring utilities
- Crawl_urls/          Source crawling scripts/assets
- cache_bm25/          Serialized BM25 indexes

------------------------------------------------------------
Main Entry Points
------------------------------------------------------------

Flask_API_Server.py
    REST API server (/api/search)

main.py
    Local pipeline testing script

wsgi.py
    WSGI application entry point

------------------------------------------------------------
Prerequisites
------------------------------------------------------------

- Python 3.10 or higher
- MongoDB running locally (default: mongodb://localhost:27017/)
- Network access for model/search providers

------------------------------------------------------------
Quick Start
------------------------------------------------------------

1. Create and activate a virtual environment.

2. Install dependencies:

   pip install -r requirements.txt

3. Configure credentials and model settings in config.py:

   - GEMINI_API_KEY
   - OPENAI_API_KEY
   - SERPAPI_API_KEY
   - Model names and host values

   NOTE: For production, store API keys in config.py.

4. Start the API server:

   python3.12 Flask_API_Server.py
   or 
   python Flask_API_Server.py

   Default server address:
   http://0.0.0.0:5050

------------------------------------------------------------
API Usage
------------------------------------------------------------
Search Endpoint:
POST /api/search

Request JSON:
{
  "query": "What are evidence-based fall prevention strategies?",
  "model": 1,
  "category": ""
}

Model Mapping:
0  -> DeepSeek
1  -> Gemini (default)
2  -> OpenAI

Response JSON:
{
  "answer": "..."
}

------------------------------------------------------------
Configuration Notes
------------------------------------------------------------

- BM25 and reranking behavior is controlled by parameters in config.py
  (RERANK, CANDIDATE_K, thresholds, etc.).

- PDF serving path is defined by PDF_FOLDER in Flask_API_Server.py.

------------------------------------------------------------
Troubleshooting
------------------------------------------------------------

If retrieval results are weak or empty:
- Verify MongoDB content
- Confirm BM25 cache files exist

If generation fails:
- Check model API keys and endpoint configuration

If web fallback fails:
- Verify SERPAPI_API_KEY
- Confirm outbound network access

------------------------------------------------------------
Systemd Services (Production Deployment)
------------------------------------------------------------

This project can be deployed as two systemd services:
Folder: \JiaYang_RAG_Offboarding_2025\1_Code\RAG_Solution\REHL_service_files
1) rag-api.service  (Flask API via Gunicorn)
2) rag-ui.service   (Streamlit UI)

------------------------------------------------------------
Service 1: rag-api.service
------------------------------------------------------------
Description:
Runs the Flask API backend using Gunicorn.

Default:
- Port: 5050
- Entry: Flask_API_Server:app
- Working Directory: /home/yangjw/RAG_Project

To install:

1. Copy the service file to systemd:
   sudo cp rag-api.service /etc/systemd/system/

2. Reload systemd:
   sudo systemctl daemon-reload

3. Enable service on boot:
   sudo systemctl enable rag-api

4. Start service:
   sudo systemctl start rag-api

5. Check status:
   sudo systemctl status rag-api

6. View logs:
   journalctl -u rag-api -f

------------------------------------------------------------
Service 2: rag-ui.service
------------------------------------------------------------

Description:
Runs the Streamlit frontend UI.

Default:
- Port: 8501
- Script: rag_ui.py
- Working Directory: /home/yangjw/Streamlit

To install:

1. Copy the service file:
   sudo cp rag-ui.service /etc/systemd/system/

2. Reload systemd:
   sudo systemctl daemon-reload

3. Enable service:
   sudo systemctl enable rag-ui

4. Start service:
   sudo systemctl start rag-ui

5. Check status:
   sudo systemctl status rag-ui

6. View logs:
   journalctl -u rag-ui -f

------------------------------------------------------------
Stopping / Restarting Services
------------------------------------------------------------

Restart:
   sudo systemctl restart rag-api
   sudo systemctl restart rag-ui

Stop:
   sudo systemctl stop rag-api
   sudo systemctl stop rag-ui

Disable from auto-start:
   sudo systemctl disable rag-api
   sudo systemctl disable rag-ui

------------------------------------------------------------
Deployment Notes
------------------------------------------------------------
- Ensure MongoDB is running before starting services.
- Ensure API keys are configured.
- Ensure Python 3.12 and required packages are installed.
- If using a virtual environment, modify ExecStart to use the venv path.
- Default exposed ports:
      API: 5050
      UI : 8501


------------------------------------------------------------
License
------------------------------------------------------------

Copyright © 2025
Wireless System Research Group (WiSeR), McMaster University.

Licensed under the Creative Commons Attribution–NonCommercial 4.0
International License (CC BY-NC 4.0).

See LICENSE.txt for full license details.
