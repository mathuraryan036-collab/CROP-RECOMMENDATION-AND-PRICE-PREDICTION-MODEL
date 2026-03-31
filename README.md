# 🌾 Sehore Crop Recommendation & Price Prediction System

This project is an **AI/ML-based decision support system for farmers in Sehore district**. It recommends suitable crops using **soil** and **weather** conditions, then predicts the **expected selling price** of the recommended crops to help identify the best option.

The system combines **crop recommendation** and **price prediction** into one workflow and provides a simple **Streamlit frontend** connected to a **FastAPI backend**.

---

## 📌 Project Overview

The project works in two stages:

1. **Crop Recommendation**  
   Soil and weather inputs are processed and clustered using trained **KMeans** and **GMM** models.

2. **Price Prediction**  
   For the crops recommended from the clustering stage, the system predicts selling prices using a trained regression model.

Finally, it returns:
- Soil cluster
- Weather cluster
- Recommended crops
- Predicted prices for each recommended crop
- Best crop based on highest predicted selling price

> **Note:** The current best crop is selected on the basis of **highest predicted selling price**, not actual profit. To estimate true profit, cultivation cost and production cost data should also be included.

---

## ✨ Features

- Soil-based crop recommendation
- Weather-based crop recommendation
- Combined soil + weather crop mapping
- Selling price prediction for recommended crops
- Best crop suggestion from predicted prices
- Interactive Streamlit user interface
- FastAPI backend for model serving

---

## 🧠 Input Parameters

### Soil Inputs
- N High
- N Medium
- N Low
- P High
- P Medium
- P Low
- K High
- K Medium
- K Low
- pH Alkaline
- pH Acidic
- pH Neutral

### Weather Inputs
- Humidity
- Precipitation
- Pressure
- Temperature
- Wind
- Prediction Year
- Season

---

## ⚙️ How It Works

### Step 1: Soil Preprocessing
The soil dataset is preprocessed by converting value ranges such as `280 – 450`, `> 450`, and `< 280` into numeric values so that scaling and clustering can be applied.

### Step 2: Weather Preprocessing
Weather labels such as humidity and precipitation are encoded into numeric form, and features are scaled before clustering.

### Step 3: Clustering
The backend uses:
- **KMeans for soil clustering**
- **GMM for soil clustering**
- **KMeans for weather clustering**
- **GMM for weather clustering**

The final soil and weather cluster are selected using majority voting between KMeans and GMM predictions.

### Step 4: Crop Recommendation
A predefined crop map links the `(soil_cluster, weather_cluster)` pair to a set of crops.

### Step 5: Price Prediction
For each recommended crop, the system fetches the latest available crop-related pricing features such as:
- MSP
- Previous price
- Price change

These features are passed to the trained price prediction model.

### Step 6: Best Crop Selection
The crop with the **highest predicted price** is selected as the best crop.

---

## 🏗️ Tech Stack

- **Python**
- **Streamlit** – frontend UI
- **FastAPI** – backend API
- **Pandas / NumPy** – data handling
- **Scikit-learn** – preprocessing and clustering support
- **Joblib** – model loading
- **SciPy** – voting using mode

---

## 📂 Project Structure

```bash
.
├── app.py                         # Streamlit frontend
├── backend.py                     # FastAPI backend
├── filled_msp_dataset.csv         # Crop price/MSP dataset
├── FINAL SOIL DATASET _new.csv    # Soil dataset
├── FINAL WEATHER DATASET_new.csv  # Weather dataset
├── kmeans_soil.pkl                # Trained KMeans soil model
├── kmeans_weather.pkl             # Trained KMeans weather model
├── gmm_soil.pkl                   # Trained GMM soil model
├── gmm_weather.pkl                # Trained GMM weather model
├── price_model_new.joblib         # Trained price prediction model
└── README.md
```

---

## 📊 Datasets Used

### 1. Soil Dataset
Contains yearly soil category/range values for:
- Nitrogen (N)
- Phosphorus (P)
- Potassium (K)
- pH levels

### 2. Weather Dataset
Contains weather-related data such as:
- Humidity
- Precipitation
- Pressure
- Temperature
- Wind

### 3. MSP / Price Dataset
Contains crop-wise information such as:
- Commodity
- Crop
- Year
- Season
- MSP
- Price
- Previous price
- Price change

---

## 🚀 Running the Project Locally

### 1. Clone the Repository
```bash
git clone https://github.com/your-username/your-repo-name.git
cd your-repo-name
```

### 2. Create a Virtual Environment
```bash
python -m venv venv
```

#### On Windows
```bash
venv\Scripts\activate
```

#### On Mac/Linux
```bash
source venv/bin/activate
```

### 3. Install Dependencies
```bash
pip install streamlit fastapi uvicorn pandas numpy scipy scikit-learn joblib requests
```

### 4. Start the FastAPI Backend
```bash
uvicorn backend:app --reload
```

The backend will run at:
```bash
http://127.0.0.1:8000
```

### 5. Start the Streamlit Frontend
Open a new terminal and run:

```bash
streamlit run app.py
```

---

## 🔌 API Endpoint

### `POST /recommend`
This endpoint accepts soil and weather inputs and returns recommended crops with predicted prices.

### Example Request Body
```json
{
  "n_high": 472.5,
  "n_medium": 365.0,
  "n_low": 266.0,
  "p_high": 25.0,
  "p_medium": 17.5,
  "p_low": 9.5,
  "k_high": 294.0,
  "k_medium": 214.5,
  "k_low": 128.25,
  "pH_alkaline": 7.875,
  "pH_acidic": 6.175,
  "pH_neutral": 7.0,
  "humidity": "muggy",
  "precipitation": "rain",
  "pressure": 29.8,
  "temperature": 78.0,
  "wind": 10.0,
  "year": 2026,
  "season": "Kharif"
}
```

### Example Response
```json
{
  "soil_cluster": 1,
  "weather_cluster": 2,
  "recommended_crops": ["Soyabean", "Mango"],
  "price_predictions": [
    {
      "crop": "Soyabean",
      "predicted_price": 5100.45,
      "season": "Kharif",
      "year": 2026,
      "msp_used": 4300.0,
      "prev_price_used": 4700.0,
      "price_change_used": 120.5
    }
  ],
  "best_crop_by_price": {
    "crop": "Soyabean",
    "predicted_price": 5100.45
  },
  "note": "This best crop is based on highest predicted selling price, not true profit. For profit, add cultivation cost data."
}
```

---

## 🖥️ Frontend Interface
The Streamlit app allows the user to:
- Enter soil data
- Enter weather data
- Select year and season
- Get crop recommendations
- View predicted prices in tabular format
- See the best crop suggestion

---

## 🌱 Crop Mapping Logic
The backend uses a crop map based on `(soil_cluster, weather_cluster)` combinations. Some example mappings include:

- `(0, 0)` → Barley, Lentil, Guava, Lemon
- `(0, 1)` → Jowar, Sesamum, Arhar, Black Gram, Green Gram
- `(1, 1)` → Wheat, Mustard, Bengal Gram, Onion, Potato
- `(1, 2)` → Soyabean, Mango
- `(2, 2)` → Maize, Paddy, Tomato, Green Chilli, Marigold, Rose, Chrysanthemum, Jasmine, Papaya, Banana

---

## 🔮 Future Improvements
- Add profit prediction instead of only selling price prediction
- Include cultivation cost, irrigation cost, fertilizer cost, and labor cost
- Add district-wise and mandi-wise price forecasting
- Deploy backend and frontend on cloud platforms
- Improve crop recommendation using supervised learning as well
- Add authentication and database support

---

## 👨‍💻 Author
**Aryan Mathur**  
B.Tech CSE AIML Student  
Project Focus: AI/ML for Agriculture

---

## 📜 License
You can add a license depending on how you want to share the project, for example:
- MIT License
- Apache 2.0
- GPL

If you do not want others to freely reuse it yet, you can keep it private until final submission.

---

## 🙌 Acknowledgment
This project is aimed at helping farmers and researchers make better agricultural decisions using machine learning, soil analysis, weather analysis, and price forecasting.
