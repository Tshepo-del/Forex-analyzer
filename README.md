name: Deploy Forex Analyzer App

on:
  push:
    branches:
      - main  # Trigger on pushes to the main branch
  pull_request:
    branches:
      - main  # Trigger on pull requests to the main branch

jobs:
  build:
    runs-on: ubuntu-latest
    
    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: '3.9'  # Specify your Python version

    - name: Install dependencies
      run: |
        python -m venv venv
        source venv/bin/activate  # On Windows use `venv\\Scripts\\activate`
        pip install -r requirements.txt

    - name: Run FastAPI app (background)
      run: |
        source venv/bin/activate
        nohup uvicorn main:app --reload &
      
    - name: Run Streamlit app
      run: |
        source venv/bin/activate
        nohup streamlit run frontend/app.py &
