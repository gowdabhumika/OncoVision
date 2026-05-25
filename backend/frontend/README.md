import streamlit as st

import requests

from PIL import Image

# Page Title
st.title("AI-Based Early Cancer Detection")

st.write(
    "Upload MRI / CT Scan / X-ray Image"
)

# Upload Image
uploaded_file = st.file_uploader(
    "Choose Medical Image",
    type=["jpg", "png", "jpeg"]
)

# Show Uploaded Image
if uploaded_file is not None:

    image = Image.open(uploaded_file)

    st.image(
        image,
        caption="Uploaded Image",
        use_column_width=True
    )

    # Detect Button
    if st.button("Detect Cancer"):

        # Send Image to Flask Backend
        files = {
            'image': uploaded_file.getvalue()
        }

        response = requests.post(
            'http://127.0.0.1:5000/predict',
            files={
                'image': uploaded_file
            }
        )

        result = response.json()

        st.subheader("Prediction Result")

        st.success(result['result'])

        st.write(
            f"Confidence: {result['confidence'] * 100:.2f}%"
        )
