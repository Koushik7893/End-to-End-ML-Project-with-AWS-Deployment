# **End-to-End Machine Learning Project with AWS Deployment**  

### 🚀 **Overview**  
This project implements an **end-to-end machine learning pipeline** that includes **data ingestion, transformation, model training, and prediction**. The trained models are deployed using **Flask**, and the entire workflow is containerized using **Docker**. The CI/CD pipeline is automated via **GitHub Actions**, which builds the Docker image and pushes it to **AWS Elastic Container Registry (ECR)** before deploying it on an **AWS EC2 instance**.  

---

## 📂 **Project Structure**  

```
📦 ML_Project
│-- 📜 README.md
│-- 📜 Dockerfile  # Docker container setup
│-- 📜 setup.py  # Package setup
│-- 📜 requirements.txt  # Dependencies
│-- 📂 workflows/  # GitHub Actions for CI/CD
│   └── main.yaml  # Workflow for Docker build and AWS deployment
│-- 📂 artifacts/  # Stored training artifacts
│   │-- train.csv
│   │-- test.csv
│   │-- data.csv
│   │-- model.pkl
│   └── preprocessor.pkl
│-- 📂 notebook/  # Jupyter Notebook with model training results
│   │-- MODEL_TRAINING.ipynb
│   │-- catboost_info/
│   └── data/  # Raw dataset
│-- 📂 src/  # Main source code
│   │-- logger.py  # Logging utility
│   │-- utils.py  # Helper functions
│   └── components/
│       │-- data_ingestion.py
│       │-- data_transformation.py
│       └── model_trainer.py
│-- 📂 pipeline/  # Model inference pipeline
│   └── predict_pipeline.py
│-- 📂 templates/  # Flask Web UI
│   │-- home.html
│   └── index.html
```

---

## ⚙️ **Machine Learning Models Implemented**  
The project trains multiple **regression models** and evaluates them using **R² scores**:  

```python
models = {
    "Linear Regression": LinearRegression(),
    "Lasso": Lasso(),
    "Ridge": Ridge(),
    "K-Neighbors Regressor": KNeighborsRegressor(),
    "Decision Tree": DecisionTreeRegressor(),
    "Random Forest Regressor": RandomForestRegressor(),
    "XGBRegressor": XGBRegressor(), 
    "CatBoosting Regressor": CatBoostRegressor(verbose=False),
    "AdaBoost Regressor": AdaBoostRegressor()
}
```

- The models are trained and stored as **model.pkl** along with **preprocessor.pkl** for data transformation.  
- Performance evaluation metrics like **R² score** are stored for comparison.  

---

## 🌐 **Flask Web App Usage**  
1. **Run Flask Application**  
   ```bash
   python app.py
   ```
2. Open **http://localhost:5000/** in the browser.  
3. **Navigate to Prediction Page**  
   - After loading the home page, manually enter `/predictdata` in the URL.  
   - Enter feature values to get predictions.  

---

## 🐳 **Docker Setup & Deployment on AWS**  

### **1️⃣ Docker Build & Push to AWS ECR**  
```bash
# Build Docker Image
docker build -t simple-app .

# Authenticate Docker with AWS ECR
aws ecr get-login-password --region us-east-1 | docker login --username AWS --password-stdin 566373416292.dkr.ecr.ap-south-1.amazonaws.com

# Tag Docker Image
docker tag simple-app:latest 566373416292.dkr.ecr.ap-south-1.amazonaws.com/simple-app:latest

# Push Image to ECR
docker push 566373416292.dkr.ecr.ap-south-1.amazonaws.com/simple-app:latest
```

### **2️⃣ Run Docker Container on AWS EC2**  
```bash
# SSH into EC2
ssh -i "your-key.pem" ubuntu@your-ec2-ip

# Update and Install Docker (Required)
sudo apt-get update -y
sudo apt-get upgrade -y
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo usermod -aG docker ubuntu
newgrp docker

# Run Docker Container
docker run -d -p 5000:5000 566373416292.dkr.ecr.ap-south-1.amazonaws.com/simple-app:latest
```

---

## 🤖 **GitHub Actions - CI/CD Workflow**  

The **GitHub Actions workflow** automates:  
✅ **Building the Docker image**  
✅ **Pushing it to AWS ECR**  
✅ **Deploying it on EC2**  

📌 **Setup GitHub Secrets:**  
- **AWS_ACCESS_KEY_ID**  
- **AWS_SECRET_ACCESS_KEY**  
- **AWS_REGION** = `us-east-1`  
- **AWS_ECR_LOGIN_URI** = `566373416292.dkr.ecr.ap-south-1.amazonaws.com`  
- **ECR_REPOSITORY_NAME** = `simple-app`  

📌 **Workflow File: `workflows/main.yaml`**  
```yaml
name: Build and Deploy ML Model

on:
  push:
    branches:
      - main

jobs:
  build:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout Code
        uses: actions/checkout@v2

      - name: Login to AWS ECR
        run: |
          aws ecr get-login-password --region ${{ secrets.AWS_REGION }} | docker login --username AWS --password-stdin ${{ secrets.AWS_ECR_LOGIN_URI }}

      - name: Build Docker Image
        run: docker build -t ${{ secrets.ECR_REPOSITORY_NAME }} .

      - name: Tag Docker Image
        run: docker tag ${{ secrets.ECR_REPOSITORY_NAME }}:latest ${{ secrets.AWS_ECR_LOGIN_URI }}/${{ secrets.ECR_REPOSITORY_NAME }}:latest

      - name: Push Docker Image to AWS ECR
        run: docker push ${{ secrets.AWS_ECR_LOGIN_URI }}/${{ secrets.ECR_REPOSITORY_NAME }}:latest
```

---

## 🏗 **Project Setup (Local Execution)**  

### **1️⃣ Clone the Repository**  
```bash
git clone https://github.com/Koushik7893/End-to-End-Machine-Learning-Project-with-AWS-Deployment
cd End-to-End-Machine-Learning-Project-with-AWS-Deployment
```

### **2️⃣ Install Dependencies**  
```bash
pip install -r requirements.txt
```

### **3️⃣ Train & Evaluate Models**  
```bash
python src/components/model_trainer.py
```

### **4️⃣ Run Flask App**  
```bash
python app.py
```

---

## 📌 **Technologies Used**  
- **Python**  
- **Flask** (Web Framework)  
- **Docker** (Containerization)  
- **AWS ECR & EC2** (Cloud Deployment)  
- **GitHub Actions** (CI/CD Pipeline)  
- **Machine Learning Libraries**:  
  - **Scikit-Learn** (Linear Regression, Decision Trees, Random Forest, etc.)  
  - **XGBoost & CatBoost** (Boosting Models)  
  - **Pandas, NumPy** (Data Processing)  

---

## 🔮 **Future Enhancements**  
- Deploying as a **serverless API using AWS Lambda**.  
- Adding **monitoring & logging** with AWS CloudWatch.  
- Implementing **feature engineering & hyperparameter tuning**.  

---

## 🤝 **Contributing**  
Contributions are welcome! Feel free to **fork**, **create a pull request**, or open an **issue**.  

---

## 📜 **License**  
This project is **open-source** under the **Apache License**.  

---

### 🎯 **Final Thoughts**  
This project showcases **ML model training, deployment, and automation with AWS and Docker**. It provides a robust **CI/CD pipeline** via **GitHub Actions** to streamline deployment. 🚀  
