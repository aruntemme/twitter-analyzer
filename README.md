# Twitter Persona Analyzer & Generator API - Usage Guide

A **full-stack web application** that analyzes Twitter community data and generates persona-matched tweets using AI. Features a beautiful Tailwind CSS frontend, robust FastAPI backend, and SQLite persistence - all deployable with a single Docker command.

## 🚀 Quick Setup

### 1. Super Quick Start (One Command!)

```bash
# Download and run the startup script
curl -sSL https://raw.githubusercontent.com/aruntemme/twitter-analyzer/main/start.sh | bash

# Or if you have the files locally:
./start.sh
```

### 2. Using Docker (Recommended)

```bash
# Clone the repository
git clone https://github.com/aruntemme/twitter-analyzer.git
cd twitter-analyzer

# Set up environment variables
cp env.example .env
# Edit .env with your OpenAI API key or local AI settings

# Build and run
docker-compose up --build

# Open your browser to http://localhost:8000
```

**🎉 That's it! Your full-stack Twitter Persona Analyzer is now running with:**
- **Beautiful web interface** at `http://localhost:8000`
- **API endpoints** for programmatic access
- **Database persistence** for all your data
- **Dark mode support** and responsive design

### 3. Local Development

```bash
# Clone the repository
git clone https://github.com/aruntemme/twitter-analyzer.git
cd twitter-analyzer

# Install dependencies
pip install -r requirements.txt

# Initialize the database
python init_db.py

# Set environment variables
cp env.example .env
# Edit .env with your settings, or export them:
export OPENAI_API_KEY="your_key_here"
export AI_MODEL="gpt-3.5-turbo"

# Run the application
python main.py

# Open your browser to http://localhost:8000
```

## 🎨 Web Interface Features

The application now includes a beautiful, modern web interface built with **Tailwind CSS** and **vanilla JavaScript**:

### ✨ Key Features:
- **📊 Real-time Dashboard** - View stats, persona analysis, and generated tweets
- **📁 Drag & Drop Upload** - Easy CSV file upload with progress tracking
- **🎯 Tweet Generation** - Intuitive form with context, count, and tone options
- **📈 Persona Analysis** - Visual display of writing patterns and insights
- **📱 Responsive Design** - Works perfectly on desktop, tablet, and mobile
- **🌙 Dark Mode** - Toggle between light and dark themes
- **📋 Copy to Clipboard** - One-click copying of generated tweets
- **📊 Statistics Cards** - Overview of tweets, personas, and generated content
- **📜 History View** - Browse previously generated tweets
- **🔄 Real-time Updates** - Live health status and data refresh
- **💬 Toast Notifications** - User-friendly success and error messages
- **⚡ Loading States** - Smooth loading indicators and progress bars

### 🎯 User Experience:
1. **Upload CSV** → Drag/drop or browse for your Twitter data
2. **View Analysis** → See writing patterns and successful content insights  
3. **Generate Tweets** → Enter context and get AI-powered suggestions
4. **Copy & Use** → One-click copy to clipboard for posting

## API Endpoints

### 1. Upload CSV Data
**POST** `/upload-csv`

Upload your Twitter data CSV file to analyze the community patterns.

```bash
curl -X POST "http://localhost:8000/upload-csv" \
  -H "Content-Type: multipart/form-data" \
  -F "file=@your_twitter_data.csv"
```

**Expected Response:**
```json
{
  "message": "CSV processed successfully",
  "dataset_id": "a1b2c3d4",
  "total_tweets": 190,
  "top_performers_count": 20,
  "writing_patterns": {
    "avg_length": 145,
    "common_starters": ["I just", "Working on", "Today I"],
    "emoji_usage": {"usage_rate": 0.65},
    "hashtag_patterns": {"usage_rate": 0.45, "popular_hashtags": ["#buildinpublic"]}
  },
  "ai_analysis": "Detailed AI analysis of successful patterns...",
  "persona_id": 1
}
```

### 2. Generate Tweets
**POST** `/generate-tweets`

Generate tweets based on your context and the analyzed persona.

```bash
curl -X POST "http://localhost:8000/generate-tweets" \
  -H "Content-Type: application/json" \
  -d '{
    "context": "I just launched a new feature for my SaaS app that helps users track their daily habits",
    "count": 3,
    "tone": "excited"
  }'
```

**Request Body:**
```json
{
  "context": "Your content context - what you want to tweet about",
  "persona_style": "high_engagement",  // optional
  "count": 3,  // number of tweets to generate
  "tone": "auto"  // auto, excited, professional, casual
}
```

**Expected Response:**
```json
{
  "tweets": [
    {
      "content": "Just shipped a habit tracker feature! 🚀 Been working on this for weeks and finally ready to help people build better routines. The dopamine hit from checking off daily goals is *chef's kiss* #buildinpublic",
      "predicted_engagement": "High",
      "style_similarity": "9",
      "reasoning": "Uses community language, includes emoji, mentions building process"
    }
  ],
  "persona_used": "build_in_public",
  "generation_timestamp": "2025-06-09T10:30:00"
}
```

### 3. Get Persona Analysis
**GET** `/persona-analysis`

View the current analyzed persona data.

```bash
curl "http://localhost:8000/persona-analysis"
```

### 4. Database Management
**GET** `/database-stats`

Get database statistics and overview.

```bash
curl "http://localhost:8000/database-stats"
```

**GET** `/personas`

List all available personas.

```bash
curl "http://localhost:8000/personas"
```

**GET** `/generated-tweets/{persona_name}`

Get history of generated tweets for a persona.

```bash
curl "http://localhost:8000/generated-tweets/build_in_public?limit=10"
```

**DELETE** `/dataset/{dataset_id}`

Delete a dataset and all associated data.

```bash
curl -X DELETE "http://localhost:8000/dataset/a1b2c3d4"
```

### 5. Health Check
**GET** `/health`

Check API status and configuration.

```bash
curl "http://localhost:8000/health"
```

## Configuration Options

### AI Backend Options

#### Option 1: OpenAI (Recommended for quality)
```env
OPENAI_API_KEY=your_key_here
AI_MODEL=gpt-3.5-turbo  # or gpt-4
USE_LOCAL_AI=false
```

#### Option 2: Local AI (Cost-free but requires setup)
```env
USE_LOCAL_AI=true
LOCAL_AI_URL=http://localhost:11434
AI_MODEL=llama2  # or mistral, codellama
```

For local AI, you'll need to run Ollama:
```bash
# Install Ollama
curl -fsSL https://ollama.ai/install.sh | sh

# Pull a model
ollama pull llama2

# Or use the Docker compose with Ollama service enabled
```

## Python Client Example

```python
import requests
import json

# Upload CSV
with open('your_twitter_data.csv', 'rb') as f:
    response = requests.post(
        'http://localhost:8000/upload-csv',
        files={'file': f}
    )
print("Upload:", response.json())

# Generate tweets
tweet_request = {
    "context": "I just launched my new productivity app",
    "count": 3,
    "tone": "excited"
}

response = requests.post(
    'http://localhost:8000/generate-tweets',
    json=tweet_request
)

tweets = response.json()
for i, tweet in enumerate(tweets['tweets'], 1):
    print(f"\nTweet {i}:")
    print(f"Content: {tweet['content']}")
    print(f"Predicted Engagement: {tweet['predicted_engagement']}")
    print(f"Style Similarity: {tweet['style_similarity']}/10")
```

## Features

### ✅ What It Does:
- **🌐 Full-Stack Web Application** with beautiful UI and robust API
- **📊 Analyzes CSV data** from Twitter/X community timelines
- **🎯 Extracts writing patterns** (length, tone, hashtags, emojis)
- **📈 Identifies successful content** based on engagement metrics
- **🤖 Generates persona-matched tweets** using AI (OpenAI or local models)
- **📋 One-click copy to clipboard** for easy tweet posting
- **💾 SQLite database persistence** with full history tracking
- **🌙 Dark/light mode support** for better user experience
- **📱 Responsive design** for all device sizes
- **🔄 Real-time updates** and health monitoring
- **🐳 Docker support** for single-command deployment

### 🎯 Perfect For:
- **Build in Public** creators
- **Content marketers** studying successful accounts
- **Social media managers** needing consistent voice
- **Developers** building Twitter automation tools
- **Researchers** analyzing social media patterns

### 🔧 Customization:
- Change AI models (GPT-3.5, GPT-4, Llama2, Mistral)
- Adjust engagement calculation weights
- Modify persona analysis criteria
- Add new content generation strategies
- Integrate with scheduling tools

## Troubleshooting

### Common Issues:

1. **CSV Format Error:**
   - Ensure your CSV has required columns: `content`, `user`, `likes`, `retweets`, `replies`
   - Check for proper UTF-8 encoding

2. **AI API Errors:**
   - Verify your OpenAI API key is correct
   - Check if you have sufficient API credits
   - For local AI, ensure Ollama is running and model is pulled

3. **Docker Issues:**
   - Make sure port 8000 is not in use
   - Check Docker daemon is running
   - Verify environment variables in docker-compose.yml

4. **Low Quality Generations:**
   - Upload more diverse tweet data (190+ tweets recommended)
   - Try different AI models
   - Adjust the context to be more specific

### Performance Tips:
- Use GPT-4 for highest quality (more expensive)
- Use GPT-3.5-turbo for good balance of quality/cost
- Use local models (Llama2) for cost-free operation
- Process larger datasets for better pattern recognition

## Database Management Tools

The application now includes persistent SQLite storage for all data. Here are the management tools:

### Database Initialization
```bash
# Initialize the database (run once)
python init_db.py
```

### Database Viewer & Manager
```bash
# Interactive database viewer
python db_viewer.py

# Quick stats view
python db_viewer.py stats

# View top performing tweets
python db_viewer.py top
```

### Database Structure
The SQLite database includes these tables:
- **tweets**: All imported Twitter data with engagement metrics
- **personas**: Analyzed persona data with writing patterns
- **generated_tweets**: History of AI-generated content

### Data Persistence Benefits:
- **No data loss** on server restart
- **Historical tracking** of generated tweets
- **Multiple datasets** can be stored simultaneously
- **Easy backup** - just copy the .db file
- **Database querying** for analytics and reporting
- **Performance** - indexed for fast retrieval

## License

MIT License - Feel free to use this project for personal or commercial purposes.

## Contributing

Contributions are welcome! Please feel free to submit a Pull Request. For major changes, please open an issue first to discuss what you would like to change.

## Support

If you encounter any issues or have questions:
1. Check the troubleshooting section above
2. Review the API documentation at `/docs`
3. Open an issue on GitHub
4. Check the health endpoint: `http://localhost:8000/health`