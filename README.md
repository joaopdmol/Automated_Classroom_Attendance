# Automated Classroom Attendance

Real-time classroom engagement tracker: a webcam feed is analyzed with OpenCV
face detection and DeepFace emotion recognition to measure **how many students
are present** and **what emotions dominate the class over time**. Results are
served through a small Flask web app and plotted with Matplotlib.

Built during a hackathon to explore a low-cost way for teachers to get
objective feedback on classroom attention and mood, without any specialized
hardware — just a webcam.

## How it works

1. **Face detection** — each video frame is scanned with a Haar Cascade
   classifier (`haarcascade_frontalface_default.xml`) to find every face in
   frame, giving a live headcount.
2. **Emotion recognition** — each detected face is cropped and passed to
   [DeepFace](https://github.com/serengil/deepface), which classifies the
   dominant emotion (happy, sad, angry, fear, surprise, neutral...).
3. **Time tracking** — the script accumulates how long the class spends in
   each emotional state and how long each headcount (0 students, 1 student, 2
   students...) was observed, then writes both series to CSV.
4. **Visualization** — a second script reads the CSVs and renders bar charts
   summarizing emotional presence and class focus over the session.
5. **Web UI** — a minimal Flask app exposes two buttons that kick off the
   detection and visualization scripts as background threads, so the whole
   pipeline can be driven from the browser instead of the terminal.

```
Webcam ──► Haar Cascade (face detection) ──► DeepFace (emotion analysis)
                                                     │
                                                     ▼
                                        emotion_times.csv / people_times.csv
                                                     │
                                                     ▼
                                       Matplotlib charts (visualizacao.py)
```

## Project structure

```
.
├── app.py                              # Flask app: serves the UI and triggers the scripts
├── emotion.py                          # Webcam capture, face detection, emotion tracking
├── visualizacao.py                     # Reads the CSV output and plots the charts
├── haarcascade_frontalface_default.xml # OpenCV pretrained face detector
├── templates/
│   └── index.html                      # Web UI (start detection / show charts)
├── emotion_times.csv                   # Sample output: time spent per emotion
├── people_times.csv                    # Sample output: time spent per headcount
└── requirements.txt
```

## Getting started

### Prerequisites

- Python 3.9+
- A webcam
- (Recommended) a virtual environment

### Installation

```bash
git clone https://github.com/joaopdmol/Automated_Classroom_Attendance.git
cd Automated_Classroom_Attendance
python -m venv venv
venv\Scripts\activate      # Windows
# source venv/bin/activate # macOS/Linux
pip install -r requirements.txt
```

### Running

Start the web app:

```bash
python app.py
```

Open `http://127.0.0.1:5000` in your browser, then:

- **Start detection** — opens the webcam feed, draws a bounding box and the
  detected emotion over each face, and tracks headcount/emotion over time.
  Press `q` in the video window to stop; results are written to
  `emotion_times.csv` and `people_times.csv`.
- **Show charts** — reads those CSVs and renders bar charts of emotional
  presence and student focus for the session.

Each script can also be run standalone (`python emotion.py`,
`python visualizacao.py`) without the web UI.

## Tech stack

| Layer               | Tools                                   |
|----------------------|------------------------------------------|
| Computer vision      | OpenCV (Haar Cascade face detection)      |
| Emotion recognition  | DeepFace, TensorFlow/Keras                |
| Backend              | Flask                                     |
| Data / visualization | Pandas, Matplotlib                        |

## Roadmap / ideas for next steps

- [ ] Persist sessions to a database instead of overwriting the CSVs each run
- [ ] Stream the annotated video to the browser instead of a native OpenCV window
- [ ] Per-student identification/attendance list (not just headcount)
- [ ] Automated tests for the detection/aggregation logic
- [ ] Dockerfile for one-command setup

## License

Distributed under the [MIT License](LICENSE).
