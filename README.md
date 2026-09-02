# Databricks SBert MLflow Serving (SBert_Custom_Model)

This repository contains the code to package, log, and deploy a custom **Sentence-BERT (SBERT)** model using the Hugging Face `paraphrase-MiniLM-L6-v2` architecture. By wrapping the model in an **MLflow custom `pyfunc`**, it is optimized for seamless deployment onto **Databricks Model Serving** for real-time text embedding generation.

## 🚀 Features
* **Custom MLflow Pyfunc Wrapper:** Standardizes input and output shapes for Sentence Transformers.
* **Databricks Native Integration:** Built to leverage Databricks Model Serving endpoints.
* **Lightweight & Fast:** Uses `paraphrase-MiniLM-L6-v2` for efficient, high-quality sentence embeddings.

---

## 🛠️ Project Structure
```text
├── README.md
├── requirements.txt         # Dependencies required for logging and running the model
├── model_wrapper.py         # Custom MLflow PythonModel implementation
└── log_and_register.py     # Script/Notebook to log and register the model in Unity Catalog
```

---

## 💻 Usage

### 1. Project Requirements
Ensure your target environment or cluster has the following dependencies (`requirements.txt`):
```text
mlflow>=2.0.0
sentence-transformers
transformers
torch
pandas
```

### 2. Querying the Served Endpoint
Once deployed as a Databricks Model Serving endpoint under the name `SBert_Custom_Model`, you can query it using the following Python script:

```python
import os
import requests
import json

# Configuration
DATABRICKS_INSTANCE = "https://<your-databricks-instance>.cloud.databricks.com"
ENDPOINT_NAME = "SBert_Custom_Model"
DATABRICKS_TOKEN = os.environ.get("DATABRICKS_TOKEN")

url = f"{DATABRICKS_INSTANCE}/serving-endpoints/{ENDPOINT_NAME}/invocations"
headers = {
    "Authorization": f"Bearer {DATABRICKS_TOKEN}",
    "Content-Type": "application/json"
}

# Define your payload using the dataframe_split format
payload = {
    "dataframe_split": {
        "columns": ["text"],
        "data": [
            ["Data science is amazing."],
            ["Machine learning model deployment."]
        ]
    }
}

response = requests.post(url, headers=headers, data=json.dumps(payload))

if response.status_code == 200:
    embeddings = response.json()
    print("Embeddings generated successfully!")
    # Access embeddings via embeddings['predictions']
else:
    print(f"Error {response.status_code}: {response.text}")
```

---

## 📊 Expected Payload Format

### Request Format (`dataframe_split`)
```json
{
  "dataframe_split": {
    "columns": ["text"],
    "data": [
      ["Your first sentence here."],
      ["Your second sentence here."]
    ]
  }
}
```

### Response Format
```json
{
  "predictions": [
    [0.0123, -0.0456, 0.7890, "... 384 dimensions total ..."],
    [-0.0789, 0.0123, -0.0456, "..."]
  ]
}
```

---

## 📝 License
This project is licensed under the MIT License.
