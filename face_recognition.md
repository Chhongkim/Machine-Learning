# Face Recognition

<img width="1201" height="750" alt="image" src="https://github.com/user-attachments/assets/e87b6f32-8c4a-4797-8997-5778b18400ca" />

### 1. Clone ពី Git

```
cd "D:\Year4\Machine Learning"
git clone https://github.com/ageitgey/face_recognition.git
cd face_recognition
```
### 2. បង្កើត venv ដោយ Python 3.12
```
py -3.12 -m venv venv
venv\Scripts\activate
python --version
```

### 3. ដំឡើង packages ទាំងអស់
```
python -m pip install --upgrade pip
pip install "setuptools<81"
pip install cmake dlib
pip install face_recognition Pillow opencv-python
pip install git+https://github.com/ageitgey/face_recognition_models
```

### 4. ផ្ទៀងផ្ទាត់
```
python -c "import os, face_recognition_models as m; print(os.path.exists(m.pose_predictor_model_location()))"
```

### 5. សាករកមុខក្នុងរូបភាព
```
cd examples
python find_faces_in_picture.py
cd ..
```

### 6. Setup Webcam

#### បង្កើត file តាម Terminal
```
New-Item <File-Name>.py
code <File-Name>.py
```
#### face_rec_webcam
```
import face_recognition
import cv2
import numpy as np

# ---- Load known faces ----
people = [
    ("examples/kim.png",    "Kim"),
    ("examples/vantha.jpg", "Vantha"),
    ("examples/molina.png", "Molina"),
    ("examples/lin.png",    "Lin"),
    ("examples/lyhoo.png",  "Lyhoo"),
]

known_encodings = []
known_names = []

for path, name in people:
    image = face_recognition.load_image_file(path)
    encs = face_recognition.face_encodings(image)
    if len(encs) == 0:
        print(f"Warning: no face found in {path} - skipping")
        continue
    known_encodings.append(encs[0])
    known_names.append(name)
    print(f"Loaded {name}")

print(f"\nTotal known people loaded: {len(known_names)}\n")

# ---- Open webcam ----
video_capture = cv2.VideoCapture(0)
if not video_capture.isOpened():
    print("Cannot open webcam!")
    exit()

print("Webcam started - press 'q' to quit")

while True:
    ret, frame = video_capture.read()
    if not ret:
        break

    # Resize frame to 1/4 for faster processing
    small_frame = cv2.resize(frame, (0, 0), fx=0.25, fy=0.25)
    rgb_small_frame = np.ascontiguousarray(small_frame[:, :, ::-1])

    face_locations = face_recognition.face_locations(rgb_small_frame)
    face_encodings = face_recognition.face_encodings(rgb_small_frame, face_locations)

    for (top, right, bottom, left), face_encoding in zip(face_locations, face_encodings):
        name = "Unknown"

        if len(known_encodings) > 0:
            face_distances = face_recognition.face_distance(known_encodings, face_encoding)
            best_match_index = np.argmin(face_distances)
            # tolerance 0.6 (lower = stricter)
            if face_distances[best_match_index] < 0.6:
                name = known_names[best_match_index]

        # Scale back up since frame was resized to 1/4
        top *= 4; right *= 4; bottom *= 4; left *= 4

        cv2.rectangle(frame, (left, top), (right, bottom), (0, 255, 0), 2)
        cv2.rectangle(frame, (left, bottom - 35), (right, bottom), (0, 255, 0), cv2.FILLED)
        cv2.putText(frame, name, (left + 6, bottom - 6),
                    cv2.FONT_HERSHEY_DUPLEX, 1.0, (0, 0, 0), 1)

    cv2.imshow('Face Recognition - Press Q to quit', frame)

    if cv2.waitKey(1) & 0xFF == ord('q'):
        break

video_capture.release()
cv2.destroyAllWindows()
```
