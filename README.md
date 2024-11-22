# ArbGPT

ArbGPT is a powerful tool designed to enhance the development experience for Arbitrum devs. This application integrates AI technologies to offer intelligent code debugging, code generation. 

## Demo:

## Key Features

1. **Smart Contract Debugger**: Analyze and debug Arbitrum smart contract code written in C#. Get detailed insights into potential issues and suggestions for code improvements.

2. **Smart Contract Generator**: Generate and enhance Arbitrum smart contract code based on user input. Receive AI-driven suggestions for code enhancements.

3. **Chat with Arbitrum Documentation**: Interact with the Arbitrum documentation through an AI-powered chat interface. Ask questions and get responses based on the Aelf documentation.

## Setup and Deployment (Local)

### 1. Fork and Clone the Repository

1. **Fork** this repository on GitHub to your own account.
2. **Clone** your forked repository:

   ```bash
   git clone https://github.com/your-username/arbGPT.git
   cd arbGPT
   ```

### 2. Create and Configure the `.env` File

Create a `.env` file in the root directory of your project using the provided example:

1. **Copy** the example `.env` file:

   ```bash
   cp .env.example .env
   ```

2. **Fill in** the environment variables:

   - **MONGODB_CONN_URI**: Your MongoDB connection URI.
   - **GITHUB_TOKEN**: Your Github Token. Obtain it from [here](https://github.com/settings/tokens), You need to make sure you have access to Github Models.

### 3. Set Up Dependencies

Install the required dependencies:

```bash
pip install -r requirements.txt
```

### 4. Create Embeddings and Configure MongoDB Atlas

1. **Run** create-embeddings.ipynb, provided to create and store embeddings into MongoDB.

2. **Configure MongoDB Atlas**:

   - **Create a Vector Search**:
     - Go to MongoDB Atlas and navigate to the search section.
     - Create a new vector search with the provided JSON configuration:

       ```json
       {
          "fields": [
            {
              "numDimensions": 3072,
              "path": "embedding",
              "similarity": "dotProduct",
              "type": "vector"
            }
          ]
        }
       ```

   - **Create Atlas Search**:
     - Select the `docs` collection and create an Atlas Search.
     - This process may take 1-2 minutes. Once done, your vector search should have status 'Active' and its ready for use.

### 5. Run the Application Locally

Run the Streamlit application:

```bash
streamlit run Home.py
```
---

## Deployment Guide for Streamlit Application (optional, uses GCP)

This guide provides step-by-step instructions to deploy your Streamlit application on Google Cloud Platform (GCP) using Cloud Run. 

### Prerequisites

1. **Google Cloud Account**: If you don't have a Google Cloud account, create one [here](https://cloud.google.com/).

### Getting Started

1. **Create a Google Cloud Project**:
   - Sign in to the Google Cloud Console.
   - Create a new project or select an existing one.
   - Enable the billing account (a free trial is recommended to avoid unintended costs).

2. **Clone the Repository**:
   ```bash
   git clone https://github.com/codingis4noobs2/arbGPT.git
   cd arbGPT
   ```

3. **Authenticate with Google Cloud**:
   ```bash
   gcloud auth login
   ```

4. **Initialize Your Project**:
   ```bash
   gcloud init
   ```

### Cloud Run Deployment

#### Create Required Files

1. **Dockerfile**:
   Create a `Dockerfile` with the following content:
   ```dockerfile
   # Use an official Python runtime as a parent image
   FROM python:3.10-slim

   # Set the working directory in the container
   WORKDIR /app

   # Copy the requirements file into the container
   COPY requirements.txt /app/

   # Install the required dependencies
   RUN pip install --no-cache-dir -r requirements.txt

   # Copy the rest of the application code into the container
   COPY . /app

   # Expose the port that the app runs on
   EXPOSE 80

   # Command to run the app
   CMD ["streamlit", "run", "Home.py", "--server.enableCORS", "false", "--browser.serverAddress", "0.0.0.0", "--browser.gatherUsageStats", "false", "--server.port", "80"]
   ```

2. **app.yaml**:
   Create an `app.yaml` file with the following content:
   ```yaml
   runtime: python310

   entrypoint: streamlit run Home.py --server.enableCORS false --browser.serverAddress 0.0.0.0 --browser.gatherUsageStats false --server.port $PORT
   ```

#### Build and Deploy

1. **Build the Container**:
   ```bash
   gcloud builds submit --tag gcr.io/PROJECT-ID/streamlit-app
   ```
   Replace `PROJECT-ID` with your actual project ID, which can be found in the top left dropdown in the Google Cloud Console.
   ![image](https://github.com/user-attachments/assets/85683ece-aa60-4357-8603-6897c0fbbc18)

2. **Deploy the Container**:
   ```bash
   gcloud run deploy --image gcr.io/PROJECT-ID/streamlit-app --platform managed --allow-unauthenticated
   ```

#### Note
If you encounter the error `Cloud Run – Failed to start and then listen on the port defined by the PORT`:

1. Execute the following command:
   ```bash
   docker buildx build --platform linux/amd64 -t {project-name} .
   ```
2. Then, redeploy the container.

### Alternative Deployment Method: Cloud Run UI

You can also deploy the container via the Cloud Run UI in the Google Cloud Console:
1. Navigate to Cloud Run
2. Click on create "+ Create Service" Button.
3. In the Cloud Run UI, select the latest container image and enter a service name for the application.
   ![image](https://github.com/user-attachments/assets/58a6bc00-c02d-4244-aa32-d534e684c2ec)
4. Click on the radio button "Allow unauthenticated invocations" so that others can visit your app.
5. Select the appropriate machine configuration based on your estimated traffic handling and click "Create."

You will receive a link to your application in a few minutes.
