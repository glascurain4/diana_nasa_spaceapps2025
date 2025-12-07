# DIANA - NASA Exoplanet Analysis Dashboard

A full-stack application for analyzing exoplanet observations using machine learning. The dashboard allows users to input astronomical data, load pre-existing datasets, and analyze them using trained ML models.

## Features

### Frontend (React + Redux)
- **Astronomical Data Input**: Form to enter observation parameters with expandable fields
- **Dataset Management**: Load pre-existing datasets (Kepler, K2, TESS) or upload CSV files
- **Interactive Dataset Table**: View observations with pagination, click rows for single analysis
- **Dual Analysis Modes**:
  - **Single Observation**: Click any row to get detailed classification with feature importance and explanations
  - **Batch Analysis**: Analyze entire dataset with aggregate statistics and model performance metrics
- **Results Visualization**: Dynamic display of classification results, confidence scores, and model insights

### Backend (FastAPI + ML)
- **RESTful API**: FastAPI endpoints for dataset loading and analysis
- **ML Integration**: Trained models for exoplanet classification
- **Feature Importance**: SHAP-like explanations for individual predictions
- **Model Metrics**: Confidence distributions, accuracy stats, and performance tracking

## Project Structure

```
NASA/
├── back/                           # Backend (FastAPI)
│   ├── main.py                     # API endpoints
│   ├── models/                     # Trained ML models
│   │   ├── modelo_kepler_exoplanetas.pkl
│   │   └── modelo_tess_exoplanetas.pkl
│   ├── services/
│   │   ├── analisis.py            # ML analysis functions
│   │   └── datasets.py            # Dataset management
│   ├── resources/
│   │   └── clean/                 # Pre-cleaned datasets
│   │       ├── kepler_clean.csv
│   │       ├── k2_clean.csv
│   │       └── tess_clean.csv
│   └── requirements.txt
│
└── front-2/                        # Frontend (React)
    ├── src/
    │   ├── pages/
    │   │   └── dashboard/
    │   │       ├── index.jsx       # Main dashboard
    │   │       ├── store.js        # Redux store
    │   │       ├── componentes/
    │   │       │   ├── AstronomicalDataInput.jsx
    │   │       │   ├── DatasetTable.jsx
    │   │       │   ├── DatasetActionButtons.jsx
    │   │       │   └── Results.jsx
    │   │       └── styles/
    │   │           └── formStyles.css
    │   └── App.jsx
    └── package.json
```

## Installation & Setup

### Backend Setup

1. Navigate to the backend directory:
```bash
cd back
```

2. Create a virtual environment (recommended):
```bash
python -m venv venv
source venv/bin/activate  # On Windows: venv\Scripts\activate
```

3. Install dependencies:
```bash
pip install -r requirements.txt
```

4. Run the FastAPI server:
```bash
uvicorn main:app --reload --port 8000
```

The API will be available at `http://localhost:8000`

### Frontend Setup

1. Navigate to the frontend directory:
```bash
cd front-2
```

2. Install dependencies:
```bash
npm install
```

3. Run the development server:
```bash
npm run dev
```

The application will be available at `http://localhost:5173`

## Usage

### Manual Entry Workflow

1. **Enter Observation Data**: Fill in the astronomical parameters in the input form
   - First 10 fields are visible by default
   - Click "Show More Fields" to see all 25 parameters
2. **Add to Dataset**: Click "Add to Dataset" to add the observation to your working dataset
3. **Repeat**: Add multiple observations as needed
4. **Analyze**:
   - Click "Analyze Dataset" button to analyze all observations at once
   - OR click on any row to analyze just that observation

### Load Dataset Workflow

1. **Choose Dataset**: Click "Choose Dataset" and select Kepler, K2, or TESS
2. **View Data**: The first 10 rows will be loaded into the table
3. **Analyze**:
   - Click "Analyze Dataset" for batch analysis
   - OR click on any row for single observation analysis

### CSV Upload Workflow

1. **Import CSV**: Click "Import CSV" and select your CSV file
2. **Automatic Parsing**: The first 10 rows will be parsed and loaded
3. **Analyze**: Use either batch or single observation analysis

## API Endpoints

### GET `/datasets`
Get available datasets metadata

### GET `/select-dataset/{datasetId}`
Load a pre-existing dataset (kepler, k2, or tess)

**Response:**
```json
{
  "status": 200,
  "data": [...]
}
```

### POST `/analyze-dataset`
Analyze multiple observations

**Request:**
```json
[
  { "pl_radeerr1": 0.0, "st_rad": 1.0, ... },
  { "pl_radeerr1": 0.0, "st_rad": 1.0, ... }
]
```

**Response:**
```json
{
  "status": "success",
  "analyzed_data": [...],
  "summary": {
    "total": 10,
    "planets": 2,
    "candidates": 3,
    "ambiguous": 2,
    "non_planets": 3
  },
  "model_metrics": {
    "average_confidence": 0.85,
    "low_confidence_count": 1,
    "high_confidence_count": 6,
    "model_version": "1.0"
  }
}
```

### POST `/analyze-observation`
Analyze a single observation with detailed explanation

**Request:**
```json
{
  "pl_radeerr1": 0.0,
  "st_rad": 1.0,
  ...
}
```

**Response:**
```json
{
  "status": "success",
  "classification": 2,
  "confidence": 0.85,
  "probabilities": [0.05, 0.10, 0.85, 0.00],
  "feature_importance": [
    { "name": "Transit Depth", "importance": 0.25 },
    ...
  ],
  "explanation": "This observation has been classified as a planet candidate with 85% confidence...",
  "details": {}
}
```

## Classification Labels

- **0**: Non-Planet
- **1**: Ambiguous
- **2**: Candidate
- **3**: Confirmed Planet

## Technologies Used

### Frontend
- React 18
- Redux Toolkit (state management)
- React Router (navigation)
- Vite (build tool)

### Backend
- FastAPI (web framework)
- NumPy & Pandas (data processing)
- Scikit-learn (ML models)
- Uvicorn (ASGI server)

## Development Notes

### Redux Store Structure

The store uses a clean separation between field metadata and values:
- `formFields`: Array of field definitions (labels, steps, validation)
- `formValues`: Object with actual form values
- `dataset`: Array of observation objects
- `analysisResult`: Results from backend (single or batch)
- `analysisType`: "single" or "batch" to determine display mode

### Model Integration

The backend uses pickle-serialized ML models. The analysis functions include:
- Preprocessing: Convert observation dict to numpy array
- Prediction: Run model inference
- Feature Importance: Calculate which features influenced the decision
- Explanation Generation: Natural language description of the classification

### Error Handling

All components include comprehensive error handling:
- Form validation before adding to dataset
- API error messages displayed to user
- Fallback mock data if model fails to load
- Clear error states in Redux

## Future Enhancements

- [ ] Real-time analysis as observations are added
- [ ] Advanced data visualization (charts, graphs)
- [ ] Model comparison (select different models)
- [ ] Batch upload (analyze >10 rows at once)
- [ ] Export results as PDF reports
- [ ] Historical analysis sessions
- [ ] User authentication and saved datasets
- [ ] Advanced filtering and sorting
- [ ] SHAP integration for better explanations

## Contributing

This project was built following the comprehensive specification in `front-2/src/pages/dashboard/prompt_2.md`.

## License

MIT License

## Contact

For questions or issues, please open an issue on the repository.
