# heart_prediction
A Streamlit web application that predicts the likelihood of heart disease/stroke using a trained machine learning model.   This project demonstrates end-to-end ML deployment: training, saving models, building a UI, and deploying with Streamlit.


## 🚀 Features
- Interactive UI built with **Streamlit**
- Predicts risk based on user inputs (age, cholesterol, BP, etc.)
- Uses a trained **scikit-learn model** with preprocessing (scaler + one-hot encoding)
- Displays results with clear risk indicators (✅ Low Risk / ⚠️ High Risk)

---

## 📂 Project Structure
heart_disease/
│
├── app.py                # Streamlit app (main UI + prediction logic)
├── model.pkl             # Trained ML model
├── scaler.pkl            # Scaler used during training
├── columns.pkl           # Expected feature columns
├── requirements.txt      # Dependencies
├── README.md             # Project documentation
└── screenshots/          # Folder for screenshots


---

## 📸 Screenshots

👉 Place screenshots in a `screenshots/` folder and reference them here:

1. **Homepage UI**  
   ![Homepage](screenshots/homepage.png)

2. **Prediction Example (Low Risk)**  
   ![Low Risk](screenshots/low_risk.png)

3. **Prediction Example (High Risk)**  
   ![High Risk](screenshots/high_risk.png)

   ---

## ⚙️ Installation

Clone the repository:
```bash
git clone https://github.com/<your-username>/heart-stroke-prediction.git
cd heart-stroke-prediction
Create a virtual environment:

python -m venv .venv
source .venv/bin/activate   # On Linux/Mac
.venv\Scripts\activate      # On Windows

Install dependencies:
pip install -r requirements.txt

Today
import streamlit as st
import pandas as pd
import joblib

model= joblib.load('model.pkl')
scaler= joblib.load('scaler.pkl')
expected_columns= joblib.load('columns.pkl')


st.title('Heart Stroke Prediction App')
st.markdown("Please enter the following details to predict the likelihood of a heart stroke:")

age= st.slider("Age", 18, 100, 40)
sex= st.selectbox("Sex", ["Male", "Female"])
chest_pain= st.selectbox("Chest Pain Type", ["ATA", "NAP", "ASY", "TA"])
resting_bp= st.number_input("Resting Blood Pressure (mm Hg)", 80, 200, 120)
cholesterol= st.number_input("Serum Cholesterol (mg/dl)", 100, 600, 200)
fasting_bs= st.selectbox("Fasting Blood Sugar > 120 mg/dl", ["Yes", "No"])
resting_ecg= st.selectbox("Resting ECG", ["Normal", "ST", "LVH"])
max_hr= st.slider("Maximum Heart Rate Achieved", 60, 220, 150)
exercise_angina= st.selectbox("Exercise Induced Angina", ["Yes", "No"])
oldpeak= st.number_input("Oldpeak (ST depression induced by exercise)", 0.0, 10.0, 1.0)
st_slope= st.selectbox("Slope of the Peak Exercise ST Segment", ["Up", "Flat", "Down"])

if st.button("Predict"):
  raw_input = {
        'Age': age,
        'RestingBP': resting_bp,
        'Cholesterol': cholesterol,
        'FastingBS': fasting_bs,
        'MaxHR': max_hr,
        'Oldpeak': oldpeak,
        'Sex_' + sex: 1,
        'ChestPainType_' + chest_pain: 1,
        'RestingECG_' + resting_ecg: 1,
        'ExerciseAngina_' + exercise_angina: 1,
        'ST_Slope_' + st_slope: 1
    }

    # Create input dataframe
  input_df = pd.DataFrame([raw_input])

    # Fill in missing columns with 0s
  for col in expected_columns:
        if col not in input_df.columns:
            input_df[col] = 0

    # Reorder columns
  input_df = input_df[expected_columns]

    # Scale the input
  scaled_input = scaler.transform(input_df)

    # Make prediction
  prediction = model.predict(scaled_input)[0]

    # Show result
  if prediction == 1:
        st.error("⚠️ High Risk of Heart Disease")
  else:
        st.success("✅ Low Risk of Heart Disease")   is this correct
Your Streamlit app code is mostly correct, but let me point out a few important refinements and potential issues so it runs smoothly:

✅ What’s good
You’ve correctly loaded the model, scaler, and expected columns with joblib.

The UI inputs cover all the typical heart disease dataset features (age, sex, chest pain, etc.).

You’re handling categorical features by one-hot encoding (Sex_Male, ChestPainType_ASY, etc.).

You’re filling missing columns with zeros and reordering to match training.

⚠️ Issues to fix
Indentation inside if st.button("Predict"):  
Your code has inconsistent indentation. For example:

python
raw_input = { ... }
# Create input dataframe
input_df = pd.DataFrame([raw_input])
should all be aligned under the if st.button("Predict"): block.

Fasting Blood Sugar encoding:  
You currently store "Yes"/"No" directly:

python
'FastingBS': fasting_bs,
But your model likely expects numeric (0/1). Fix:

python
'FastingBS': 1 if fasting_bs == "Yes" else 0,
Categorical one-hot encoding consistency:  
You’re setting only the selected category to 1, but not explicitly setting others to 0.
That’s fine because you later fill missing columns with 0, but make sure your expected_columns.pkl includes all possible one-hot columns (e.g., Sex_Male, Sex_Female, etc.).

Scaling:  
You’re scaling after reordering columns, which is correct. Just ensure your scaler.pkl was fit on the same expected_columns.

Prediction output:  
You’re using:

python
prediction = model.predict(scaled_input)[0]
That’s fine. If you want probability, you could also use:

python
prob = model.predict_proba(scaled_input)[0][1]
🔧 Corrected snippet
Here’s the cleaned-up version:

python
if st.button("Predict"):
    raw_input = {
        'Age': age,
        'RestingBP': resting_bp,
        'Cholesterol': cholesterol,
        'FastingBS': 1 if fasting_bs == "Yes" else 0,
        'MaxHR': max_hr,
        'Oldpeak': oldpeak,
        'Sex_' + sex: 1,
        'ChestPainType_' + chest_pain: 1,
        'RestingECG_' + resting_ecg: 1,
        'ExerciseAngina_' + exercise_angina: 1,
        'ST_Slope_' + st_slope: 1
    }

    # Create input dataframe
    input_df = pd.DataFrame([raw_input])

    # Fill in missing columns with 0s
    for col in expected_columns:
        if col not in input_df.columns:
            input_df[col] = 0

    # Reorder columns
    input_df = input_df[expected_columns]

    # Scale the input
    scaled_input = scaler.transform(input_df)

    # Make prediction
    prediction = model.predict(scaled_input)[0]

    # Show result
    if prediction == 1:
        st.error("⚠️ High Risk of Heart Disease")
    else:
        st.success("✅ Low Risk of Heart Disease")
👉 With these fixes, your app should run correctly and give reliable predictions.
Would you like me to also show you how to add probability output (e.g., “There’s a 72% chance of heart disease”) instead of just binary high/low risk?

ModuleNotFoundError: No module named 'joblib'

File "C:\Users\arusr\OneDrive\Documents\Desktop\heart_disease\.venv\Lib\site-packages\streamlit\runtime\scriptrunner\exec_code.py", line 129, in exec_func_with_error_handling
    result = func()
             ^^^^^^
File "C:\Users\arusr\OneDrive\Documents\Desktop\heart_disease\.venv\Lib\site-packages\streamlit\runtime\scriptrunner\script_runner.py", line 689, in code_to_exec
    exec(code, module.__dict__)  # noqa: S102
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^
File "C:\Users\arusr\OneDrive\Documents\Desktop\heart_disease\app.py", line 3, in <module>
    import joblib
The error means your virtual environment doesn’t have joblib installed. That’s why Python can’t find the module. Here’s how you can fix it:

🛠️ Steps to solve
Activate your virtual environment  
In your project folder:

bash
.venv\Scripts\activate
(On macOS/Linux: source .venv/bin/activate)

Install joblib  
Run:

bash
pip install joblib
This will add joblib to your environment.

Verify installation  
Run:

bash
pip show joblib
You should see details like version and location.

Optional: Add to requirements.txt  
To avoid future issues, add:

Code
joblib
to your requirements.txt file so it’s installed automatically when you set up the project elsewhere.

⚡ Quick check
If you’re using Google Colab (I noticed you have Colab tabs open), you can also install it directly in a notebook:

python
!pip install joblib
👉 Once installed, restart your Streamlit app (streamlit run app.py) and the error should disappear.

Do you want me to also show you how to bundle all dependencies (like streamlit, pandas, scikit-learn, joblib) into a single requirements.txt so deployment on Vercel/Heroku works without missing packages?

no module named sklearn error
That new error means your environment doesn’t have scikit‑learn installed. Since your model and scaler were trained with scikit‑learn, you need that package in your virtual environment.

🛠️ Fix
Activate your virtual environment

bash
.venv\Scripts\activate
(On macOS/Linux: source .venv/bin/activate)

Install scikit‑learn

bash
pip install scikit-learn
Verify installation

bash
pip show scikit-learn
You should see version info and location.

Restart your Streamlit app

bash
streamlit run app.py
📦 Best practice
Since you already needed joblib and now scikit‑learn, it’s smart to create a requirements.txt file for your project. Put this inside:

Code
streamlit
pandas
scikit-learn
joblib
Then you (or anyone else) can install everything at once with:

bash
pip install -r requirements.txt
👉 Once scikit‑learn is installed, the ModuleNotFoundError: No module named 'sklearn' will disappear.

Do you want me to also show you how to export your environment’s installed packages into a requirements.txt automatically, so you don’t miss anything when deploying?

in the app runing in other tab, tell me values to put to verify for high risk of heart stroke
To quickly test your Streamlit app and see the “⚠️ High Risk of Heart Disease” output, you’ll want to enter values that typically indicate higher risk factors. Here’s an example set you can try in the app tab:

Age: 65

Sex: Male

Chest Pain Type: ASY (asymptomatic)

Resting Blood Pressure: 160 mm Hg

Cholesterol: 280 mg/dl

Fasting Blood Sugar > 120 mg/dl: Yes

Resting ECG: LVH (left ventricular hypertrophy)

Maximum Heart Rate Achieved: 100

Exercise Induced Angina: Yes

Oldpeak (ST depression): 3.0

Slope of ST Segment: Down

These values combine several classic high‑risk indicators: older age, asymptomatic chest pain, high blood pressure, high cholesterol, elevated fasting blood sugar, abnormal ECG, low max heart rate, exercise‑induced angina, and significant ST depression.

👉 Entering these should trigger your model to predict high risk and display the red warning message.

Would you like me to also give you a contrasting low‑risk test case (so you can verify both outcomes in your app)?

ModuleNotFoundError: No module named 'plotly'

File "C:\Users\arusr\OneDrive\Documents\Desktop\heart_disease\.venv\Lib\site-packages\streamlit\runtime\scriptrunner\exec_code.py", line 129, in exec_func_with_error_handling
    result = func()
             ^^^^^^
File "C:\Users\arusr\OneDrive\Documents\Desktop\heart_disease\.venv\Lib\site-packages\streamlit\runtime\scriptrunner\script_runner.py", line 689, in code_to_exec
    exec(code, module.__dict__)  # noqa: S102
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^
File "C:\Users\arusr\OneDrive\Documents\Desktop\heart_disease\app.py", line 5, in <module>
    import plotly.graph_objects as go
The new error is simply because Plotly isn’t installed in your virtual environment. Since your app imports plotly.graph_objects as go, you need to add Plotly to your environment.

🛠️ Fix
Activate your virtual environment

bash
.venv\Scripts\activate
(On macOS/Linux: source .venv/bin/activate)

Install Plotly

bash
pip install plotly
Verify installation

bash
pip show plotly
You should see version info and location.

Restart your Streamlit app

bash
streamlit run app.py
📦 Best practice
Update your requirements.txt so you don’t hit missing‑module errors again. Add:

Code
streamlit
pandas
scikit-learn
joblib
plotly
Then anyone (or you on another machine) can install everything at once:

bash
pip install -r requirements.txt
👉 Once Plotly is installed, the error will disappear and you’ll be able to use its charts in your app.

Would you like me to also show you a simple Plotly chart snippet (like a bar chart or gauge) that you can embed into your Streamlit app to visualize the prediction probability?

i want text in white run = st.button("Run AI Risk Analysis", use_container_width=True, type="primary")
In Streamlit, the button text color is controlled by CSS, and by default the primary button has white text only if your theme background is dark. Since you want to force the text to be white, you can inject a small CSS style override.

Here’s how you can make your "Run AI Risk Analysis" button text white:

python
# Custom CSS to make button text white
st.markdown("""
    <style>
    div.stButton > button:first-child {
        color: white !important;
    }
    </style>
    """, unsafe_allow_html=True)

# Your button
run = st.button("Run AI Risk Analysis", use_container_width=True, type="primary")
🔎 Explanation
div.stButton > button:first-child targets the Streamlit button element.

color: white !important; forces the text color to white regardless of theme.

unsafe_allow_html=True lets you inject CSS safely.

👉 With this, your button will display white text consistently, even if your app theme is light.

Would you like me to also show you how to style the button background (e.g., red for high risk, green for low risk) so the UI matches your prediction output?

as u can see, i trained modles for heart stroke prediction and made a ui for it in streamlit, now i want to also publish it on github after deploying, give me complete script for it, keep sapce with proper description on which screenshot to be added there. also tell which files to be uploaded.
Perfect — you’ve already trained your model, built the Streamlit UI, and tested it locally. Now let’s prepare a GitHub repository that documents your project and makes it easy for others to run or deploy.

Here’s a complete README.md script you can use. I’ll also tell you which files to upload and where to add screenshots.

📄 README.md (Complete Script)
markdown
# 🫀 Heart Stroke Prediction App

A Streamlit web application that predicts the likelihood of heart disease/stroke using a trained machine learning model.  
This project demonstrates end-to-end ML deployment: training, saving models, building a UI, and deploying with Streamlit.

---

## 🚀 Features
- Interactive UI built with **Streamlit**
- Predicts risk based on user inputs (age, cholesterol, BP, etc.)
- Uses a trained **scikit-learn model** with preprocessing (scaler + one-hot encoding)
- Displays results with clear risk indicators (✅ Low Risk / ⚠️ High Risk)

---

## 📂 Project Structure
heart_disease/
│
├── app.py                # Streamlit app (main UI + prediction logic)
├── model.pkl             # Trained ML model
├── scaler.pkl            # Scaler used during training
├── columns.pkl           # Expected feature columns
├── requirements.txt      # Dependencies
├── README.md             # Project documentation
└── screenshots/          # Folder for screenshots

Code

---

## 📸 Screenshots

👉 Add screenshots inside a `screenshots/` folder and reference them here:

1. **Homepage UI**  
   ![Homepage](screenshots/homepage.png)

2. **Prediction Example (Low Risk)**  
   ![Low Risk](screenshots/low_risk.png)

3. **Prediction Example (High Risk)**  
   ![High Risk](screenshots/high_risk.png)

---

## ⚙️ Installation

Clone the repository:
```bash
git clone https://github.com/<your-username>/heart-stroke-prediction.git
cd heart-stroke-prediction
Create a virtual environment:

bash
python -m venv .venv
source .venv/bin/activate   # On Linux/Mac
.venv\Scripts\activate      # On Windows
Install dependencies:

bash
pip install -r requirements.txt
▶️ Run the App
bash
streamlit run app.py
Open your browser at http://localhost:8501.

📦 Requirements
Add these to requirements.txt:

Code
streamlit
pandas
scikit-learn
joblib
plotly
🌐 Deployment
You can deploy this app on:

Streamlit Cloud (free, easiest)

Heroku / Vercel (requires Procfile + setup)

For Streamlit Cloud:

Push this repo to GitHub.

Go to streamlit.io.

Connect your GitHub repo.

Deploy — done!

🧑‍💻 Author
Your Name

GitHub: @your-username (github.com in Bing)

Code

---

## 📂 Files to Upload to GitHub
- `app.py` (your Streamlit app)
- `model.pkl` (trained model)
- `scaler.pkl` (scaler object)
- `columns.pkl` (expected columns list)
- `requirements.txt` (dependencies)
- `README.md` (documentation)
- `screenshots/` folder with PNGs (homepage, low risk, high risk)

---

👉 With this setup, anyone can clone your repo, install dependencies, and run the app locally or deploy it.  

Would you like me to also generate a **Procfile + setup.sh** so you can deploy on **Heroku/Vercel** as well, not just Streamlit Cloud?
make readme
Here’s a polished README.md you can use for your GitHub repository. I’ve structured it with clear sections, placeholders for screenshots, and instructions on which files to upload so your project looks professional and easy to understand.

📄 README.md
markdown
# 🫀 Heart Stroke Prediction App

A Streamlit web application that predicts the likelihood of heart disease/stroke using a trained machine learning model.  
This project demonstrates end-to-end ML deployment: training, saving models, building a UI, and deploying with Streamlit.

---

## 🚀 Features
- Interactive UI built with **Streamlit**
- Predicts risk based on user inputs (age, cholesterol, BP, etc.)
- Uses a trained **scikit-learn model** with preprocessing (scaler + one-hot encoding)
- Displays results with clear risk indicators (✅ Low Risk / ⚠️ High Risk)

---

## 📂 Project Structure
heart_disease/
│
├── app.py                # Streamlit app (main UI + prediction logic)
├── model.pkl             # Trained ML model
├── scaler.pkl            # Scaler used during training
├── columns.pkl           # Expected feature columns
├── requirements.txt      # Dependencies
├── README.md             # Project documentation
└── screenshots/          # Folder for screenshots

Code

---

## 📸 Screenshots

👉 Place screenshots in a `screenshots/` folder and reference them here:

1. **Homepage UI**  
   ![Homepage](screenshots/homepage.png)

2. **Prediction Example (Low Risk)**  
   ![Low Risk](screenshots/low_risk.png)

3. **Prediction Example (High Risk)**  
   ![High Risk](screenshots/high_risk.png)

---

## ⚙️ Installation

Clone the repository:
```bash
git clone https://github.com/<your-username>/heart-stroke-prediction.git
cd heart-stroke-prediction
Create a virtual environment:

bash
python -m venv .venv
source .venv/bin/activate   # On Linux/Mac
.venv\Scripts\activate      # On Windows
Install dependencies:

bash
pip install -r requirements.txt
▶️ Run the App
streamlit run app.py
