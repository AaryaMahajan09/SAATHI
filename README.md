# 🧠 SAATHI: Stress Assessment and Trauma Aware Helpline Intelligence

A research-oriented multimodal AI platform that fuses facial expressions, vocal sentiment, acoustic features, and behavioral signals to assess emotional well-being and surface early indicators of psychological stress.

---

## 📌 Overview

Traditional mental health screening tools rely predominantly on self-reported questionnaires — a single modality susceptible to subjective bias and recall inaccuracy. **SAATHI** (**S**tress **A**ssessment and **T**rauma **A**ware **H**elpline **I**ntelligence) provides an advanced, empathetic, multimodal approach.

**SAATHI** integrates three independent input streams:
1. **Clinical Psychological Questionnaire (PHQ-9)**: Standardized clinical inventory classified using machine learning.
2. **Acoustic & NLP Vocal Sentiment Analysis**: Dual-engine voice processing analyzing acoustic features (Pitch, Energy, MFCCs) and semantic sentiment (Transformer-based DistilRoBERTa model + OpenAI Whisper STT).
3. **Facial Emotion Recognition**: Real-time webcam tracking powered by MTCNN face detection and Deep CNN emotion classification.

Each modality provides complementary insights, fused into a unified composite risk assessment with personalized guidance.

> [!NOTE]  
> **Disclaimer**: SAATHI is designed for educational, research, and self-screening awareness purposes only. It is **not a certified medical diagnostic device** and cannot replace professional clinical evaluation.

---

## 🎯 Key Objectives

- **Early Detection**: Surface subtle vocal, facial, and behavioral indicators of depressive mood patterns.
- **Multimodal Fusion**: Combine diverse signals into a weighted composite score for higher reliability.
- **Stratified Risk Classification**: Categorize risk levels into **Normal / Low**, **Moderate / Medium**, and **Severe / High**.
- **Actionable Guidance**: Offer immediate, personalized wellness recommendations, coping strategies, and crisis resources.
- **Longitudinal Tracking**: Store session history and visualize trend patterns over time via an interactive dashboard.

---

## ⚙️ Modalities & Features

### 1. 📝 PHQ-9 Psychological Assessment
- Standard 9-item Patient Health Questionnaire capturing core depressive symptoms over the past two weeks.
- Answers are classified using a trained **Scikit-learn Naive Bayes** model.
- Outputs confidence scores and risk levels feeding directly into the fusion pipeline.

### 2. 🎭 Facial Emotion Recognition
- Real-time webcam capture and face alignment using **MTCNN**.
- Pretrained Deep CNN (**TensorFlow / Keras**) classifying 7 facial states: *Happy, Neutral, Surprise, Sad, Disgust, Angry, Fear*.
- Sliding-window temporal smoothing across consecutive frames to filter transient noise and false positives.

### 3. 🎙️ Voice Emotion & NLP Sentiment Analysis
- **Acoustic Feature Extraction (Librosa & PyAudio)**:
  - **RMS Energy**: Speech volume dynamics and vocal vitality.
  - **Fundamental Frequency (Pitch / F0)**: Pitch variation and contour (flattened/monotone pitch signals depressive affect).
  - **MFCCs (Mel-Frequency Cepstral Coefficients)**: 13 spectral coefficients capturing vocal tract timbre.
- **Semantic & Clinical NLP (DistilRoBERTa + Whisper)**:
  - Speech transcription via **OpenAI Whisper**.
  - Transformer text emotion classification (`j-hartmann/emotion-english-distilroberta-base`).
  - Clinically weighted indicators (Anhedonia, Hopelessness & Self-worth, Sleep disturbance, Fatigue, General mood).
  - High-risk keyword & suicidal ideation detection rules.

### 4. 🔗 Multimodal Fusion Engine
- Combines individual normalized modality scores using clinically informed weights:
  $$\text{Composite Score} = (\text{Quiz} \times 0.40) + (\text{Voice NLP} \times 0.40) + (\text{Facial Emotion} \times 0.20)$$
- Maps composite scores to risk tiers:
  - **Normal / Low (0.00 – 0.33)**: Mild stress / healthy baseline.
  - **Moderate / Medium (0.34 – 0.66)**: Noticeable depressive symptoms.
  - **Severe / High (0.67 – 1.00)**: Clinically significant distress indicator.

### 5. 📊 User Dashboard & History Tracking
- Secure user registration and login with password hashing (**Werkzeug**).
- Session tracking and SQLite persistent storage (`users.db`).
- Historical results table with timestamps and trend analysis.

---

## 🛠️ Tech Stack

| Layer | Technology / Library | Description |
| :--- | :--- | :--- |
| **Frontend** | HTML5, CSS3, Vanilla JavaScript | Responsive glassmorphism interface & interactive pages |
| **Backend** | Flask, Flask-CORS, Werkzeug | Web server routing, session management, and API endpoints |
| **Database** | SQLite3 | User authentication and assessment history storage |
| **Machine Learning** | Scikit-learn, Joblib | Naive Bayes classification for questionnaire data |
| **Deep Learning** | TensorFlow, Keras, PyTorch | CNN models for facial emotion detection |
| **Computer Vision** | OpenCV, MTCNN | Real-time video processing, face detection & bounding boxes |
| **Audio & Speech** | Librosa, PyAudio, SoundDevice, SoundFile | Acoustic feature extraction (MFCCs, Pitch, Energy) |
| **NLP & Transformers** | Hugging Face Transformers, OpenAI Whisper | Emotion classification (DistilRoBERTa) & Speech-to-Text |

---

## 📂 Project Structure

```text
SAATHI/
│
├── app.py                     # Main Flask web application entrypoint
├── db.py                      # SQLite database operations & helper functions
├── requirements.txt           # Python package dependencies
├── README.md                  # Project documentation
│
├── BACKEND/                   # Core ML research & standalone backend modules
│   ├── Data/                  # Training & test datasets
│   ├── MODELS/                # Saved trained model weights (.h5, .hdf5, .pkl)
│   │   ├── emotion_model_best.h5
│   │   ├── emotion_model.hdf5
│   │   └── quiz_model.pkl
│   ├── MODULES/               # Core processing & inference modules
│   │   ├── face.py            # MTCNN face detection & emotion classification
│   │   ├── quiz.py            # PHQ-9 Naive Bayes classification
│   │   ├── voice.py           # Acoustic feature extraction (MFCCs, Pitch, RMS)
│   │   ├── voice_nlp.py       # Transformer-based emotion & clinical sentiment NLP
│   │   ├── voice_stt.py       # Speech-to-text transcription & analysis
│   │   └── test_mic.py        # Microphone diagnostic test script
│   └── Training/              # Model training scripts
│       ├── train_face.py
│       └── train_quiz.py
│
├── modules/                   # Flask application module wrappers
│   ├── __init__.py
│   ├── quiz.py
│   └── voice.py
│
├── database/                  # SQLite database storage (users.db)
│
├── templates/                 # Jinja2 HTML templates
│   ├── base.html              # Base layout template
│   ├── index.html             # Landing page & sign-in modal
│   ├── register.html          # User registration page
│   ├── main.html              # Central user dashboard
│   ├── assessment.html        # Assessment modality selector
│   ├── text-quiz.html         # Standalone PHQ-9 quiz
│   ├── voice-analysis.html    # Standalone voice analysis
│   ├── video-analysis.html    # Standalone video emotion analysis
│   ├── full-assessment-stage1.html # Full assessment Stage 1 (Quiz)
│   ├── full-assessment-stage2.html # Full assessment Stage 2 (Audio & Video)
│   └── results.html           # Assessment result breakdown & history
│
└── static/                    # CSS stylesheets & client-side scripts
    ├── global.css
    ├── index.css
    ├── register.css
    ├── main.css
    ├── main.js
    ├── assessment.css
    ├── text-quiz.css
    ├── voice-analysis.css
    ├── video-analysis.css
    ├── full-assessment.css
    └── results.css
```

---

## 🚀 Getting Started

### Prerequisites

- **Python**: Version 3.10 or 3.11 recommended.
- **Hardware**: Working webcam and microphone for video and audio assessments.
- **Git**: Installed on your system.

---

### Step 1: Clone the Repository

```bash
git clone https://github.com/AaryaMahajan09/SAATHI.git
cd SAATHI
```

---

### Step 2: Create & Activate a Virtual Environment

You can use either **Conda** or Python's built-in **venv**. Choose the method you prefer:

#### Option A: Using Conda (Recommended for ML dependencies)

```bash
# Create a new conda environment with Python 3.10
conda create -n saathi_env python=3.10 -y

# Activate the conda environment
conda activate saathi_env
```

#### Option B: Using Python `venv`

- **On Windows (PowerShell / Command Prompt)**:
  ```powershell
  # Create the virtual environment
  python -m venv venv

  # Activate on PowerShell:
  .\venv\Scripts\Activate.ps1

  # OR Activate on Command Prompt (CMD):
  .\venv\Scripts\activate.bat
  ```

- **On macOS / Linux**:
  ```bash
  # Create the virtual environment
  python3 -m venv venv

  # Activate the virtual environment
  source venv/bin/activate
  ```

---

### Step 3: Install Dependencies

Ensure your virtual environment is active, then install all required packages:

```bash
pip install -r requirements.txt
```

> [!TIP]
> If you encounter issues with `pyaudio` on Windows, you can install the pre-compiled wheel via:
> ```bash
> pip install pipwin
> pipwin install pyaudio
> ```

---

### Step 4: Run the Application

Start the Flask development server:

```bash
python app.py
```

---

### Step 5: Open in Your Browser

Open your web browser and navigate to:

```
http://127.0.0.1:5000/
```

- Create an account on the **Register** page or sign in.
- Navigate to **Assessment** to take individual tests (PHQ-9 Quiz, Voice Analysis, Video Analysis) or the comprehensive **Full Assessment**.
- View your composite score, risk stratification, and previous logs on the **Results** page.

---

## 🧪 System Architecture & Pipeline

```text
                               ┌────────────────────────────────────────────────────────┐
                               │                    User Input Streams                  │
                               └──────┬────────────────────┬────────────────────┬───────┘
                                      │                    │                    │
                             [ PHQ-9 Questionnaire ]  [ Microphone Audio ] [ Webcam Video Feed ]
                                      │                    │                    │
                                      ▼                    ▼                    ▼
                               ┌──────────────┐     ┌──────────────┐     ┌──────────────┐
                               │ Naive Bayes  │     │  Acoustic +  │     │ MTCNN Face + │
                               │  Classifier  │     │ DistilRoBERTa│     │ Emotion CNN  │
                               └──────┬───────┘     └──────┬───────┘     └──────┬───────┘
                                      │ (Weight: 40%)      │ (Weight: 40%)      │ (Weight: 20%)
                                      └────────────┬───────┴────────────────────┘
                                                   │
                                                   ▼
                                      ┌─────────────────────────┐
                                      │ Multimodal Fusion Model │
                                      │ Composite Score (0–100%)│
                                      └────────────┬────────────┘
                                                   │
                                                   ▼
                                      ┌─────────────────────────┐
                                      │   Risk Stratification   │
                                      │ Normal | Moderate | High│
                                      └────────────┬────────────┘
                                                   │
                                                   ▼
                                      ┌─────────────────────────┐
                                      │ Personalized Guidance & │
                                      │ Historical Trend Log    │
                                      └─────────────────────────┘
```

---

## 💡 Running Individual Submodules (Standalone CLI)

If you wish to test individual modules without running the full web app:

1. **Test Face Emotion Recognition (Webcam)**:
   ```bash
   python BACKEND/MODULES/face.py
   ```
   *(Press `ESC` to close the webcam window)*

2. **Test Voice Acoustic Analyzer**:
   ```bash
   python BACKEND/MODULES/voice.py
   ```

3. **Test Speech-to-Text & Transcriber**:
   ```bash
   python BACKEND/MODULES/voice_stt.py
   ```

4. **Test Quiz Predictor via Terminal**:
   ```bash
   python BACKEND/MODULES/quiz.py
   ```

---

## ⚠️ Limitations & Ethical Considerations

- **Acoustic Noise**: Ambient room noise and low-grade microphones can impact acoustic pitch/energy accuracy.
- **Lighting & Occlusion**: Poor illumination, glasses, or partial face coverage may reduce MTCNN detection reliability.
- **Cultural & Linguistic Factors**: Emotional prosody and facial expressions naturally differ across cultures, accents, and demographics.
- **Single-Point vs. Longitudinal**: A single test reflects momentary state; longitudinal trends offer more meaningful insights.
- **Non-Clinical System**: SAATHI is an assistive screening research tool, not a clinical diagnostic instrument.

---

## 📜 License & Disclaimer

This project is licensed under the academic / open-source MIT License.

> [!WARNING]  
> **Crisis Support Disclaimer**: If you or someone you know is struggling with mental health issues or experiencing a crisis, please reach out to professional mental health resources or a national crisis helpline (e.g., in the US call or text **988**, in India contact **KIRAN at 1800-599-0019**).

---

## 🙏 Acknowledgements

- **Hugging Face Transformers**: Pretrained `emotion-english-distilroberta-base` emotion classification model.
- **OpenAI Whisper**: Robust speech-to-text audio transcription.
- **Open-source Python Community**: Flask, Scikit-learn, TensorFlow, Keras, Librosa, OpenCV, PyAudio, SQLite.
