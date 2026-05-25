from flask import Flask, request, jsonify
from flask_cors import CORS

import os
import numpy as np

from tensorflow.keras.models import load_model
from tensorflow.keras.preprocessing import image

# Flask App
app = Flask(__name__)

# Enable CORS
CORS(app)

# Upload Folder
UPLOAD_FOLDER = 'uploads'

os.makedirs(UPLOAD_FOLDER, exist_ok=True)

# Load Trained AI Model
model = load_model('saved_model/cancer_model.h5')


# Home Route
@app.route('/')
def home():

    return "AI Cancer Detection Backend Running"


# Prediction Route
@app.route('/predict', methods=['POST'])
def predict():

    # Check image uploaded
    if 'image' not in request.files:

        return jsonify({
            'error': 'No image uploaded'
        })

    file = request.files['image']

    # Save uploaded image
    filepath = os.path.join(
        UPLOAD_FOLDER,
        file.filename
    )

    file.save(filepath)

    # Load image
    img = image.load_img(
        filepath,
        target_size=(224, 224)
    )

    # Convert image to array
    img_array = image.img_to_array(img)

    # Expand dimensions
    img_array = np.expand_dims(
        img_array,
        axis=0
    )

    # Normalize image
    img_array = img_array / 255.0

    # AI Prediction
    prediction = model.predict(img_array)[0][0]

    # Generate Result
    if prediction > 0.5:

        result = {
            'result': 'Cancer Detected',
            'confidence': float(prediction)
        }

    else:

        result = {
            'result': 'No Cancer Detected',
            'confidence': float(1 - prediction)
        }

    return jsonify(result)


# Run Flask Server
if __name__ == '__main__':

    app.run(
        debug=True,
        host='0.0.0.0',
        port=5000
    )
