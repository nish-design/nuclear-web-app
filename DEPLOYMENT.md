# Deployment Guide

This guide covers multiple free deployment options for the Nuclear Web App.

## Option 1: Hugging Face Spaces (Recommended for ML Apps)

### Why Hugging Face Spaces?
- **Perfect for ML applications** with large model files
- **No file size restrictions** for model files
- **Free hosting** with good performance
- **Built-in Python support**
- **Easy Git integration**

### Steps:
1. Create account at [huggingface.co](https://huggingface.co)
2. Create a new Space:
   - Choose "Gradio" or "Streamlit" as SDK (we'll use custom)
   - Set to Public
3. Clone your space repository
4. Copy all files to the space repository
5. Push to deploy

### Commands:
```bash
git clone https://huggingface.co/spaces/YOUR_USERNAME/nuclear-web-app
cd nuclear-web-app
# Copy all your files here
git add .
git commit -m "Initial deployment"
git push
```

## Option 2: Render

### Steps:
1. Create account at [render.com](https://render.com)
2. Connect your GitHub repository
3. Create a new Web Service
4. Configure:
   - Build Command: `pip install -r requirements.txt`
   - Start Command: `gunicorn app:app --host 0.0.0.0 --port $PORT`
   - Python Version: 3.11

## Option 3: Railway

### Steps:
1. Create account at [railway.app](https://railway.app)
2. Connect GitHub repository
3. Deploy automatically
4. Railway will detect Python and use the Procfile

## Option 4: Fly.io

### Steps:
1. Install Fly CLI: `curl -L https://fly.io/install.sh | sh`
2. Login: `fly auth login`
3. Launch: `fly launch`
4. Deploy: `fly deploy`

## File Structure for Deployment

```
nuclear-web-app/
├── app.py                    # Main Flask application
├── app_hf.py                # Hugging Face Spaces version
├── prediction_bundle.joblib  # ML model (66MB)
├── requirements.txt          # Python dependencies
├── Procfile                 # Process configuration
├── runtime.txt              # Python version
├── render.yaml              # Render configuration
├── templates/
│   └── index.html           # Frontend template
└── README.md                # Documentation
```

## Environment Variables

Most platforms will automatically detect the Flask app. If needed, set:
- `PORT`: Will be automatically set by the platform
- `FLASK_APP`: `app.py` (usually not needed)

## Troubleshooting

### Large Model File Issues
- **Hugging Face Spaces**: No issues with large files
- **Other platforms**: May need Git LFS for files >100MB

### Memory Issues
- The 66MB model file loads into memory
- Most free tiers provide 512MB-1GB RAM
- Should be sufficient for this application

### Build Timeouts
- If builds timeout, try reducing dependencies
- Consider using lighter ML libraries if needed

## Testing Deployment

After deployment, test these endpoints:
- `GET /` - Should show the web interface
- `POST /predict` - Should return predictions for valid Z,N values

Example test:
```bash
curl -X POST https://your-app-url.com/predict \
  -H "Content-Type: application/json" \
  -d '{"z": 8, "n": 8}'
```

## Performance Optimization

1. **Model Loading**: Model loads once at startup (good)
2. **Caching**: Consider adding response caching for common queries
3. **CDN**: Hugging Face Spaces includes CDN automatically
4. **Compression**: Enable gzip compression in production
