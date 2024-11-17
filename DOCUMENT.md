To set up your Google Cloud project to integrate directly with your GitHub repository, you can use **Cloud Build**. Here’s how you can generate the necessary configuration and install the package for your project:

### Steps to Integrate Google Cloud with GitHub:

1. **Enable Required APIs**: Ensure that the necessary APIs are enabled in your Google Cloud project:
   - Cloud Build API
   - Artifact Registry API (if you need a package repository)

2. **Install Google Cloud SDK**:
   If you don’t have the Google Cloud SDK installed:
   ```bash
   curl -O https://dl.google.com/dl/cloudsdk/channels/rapid/downloads/google-cloud-cli-<VERSION>-linux-x86_64.tar.gz
   tar -xf google-cloud-cli-<VERSION>-linux-x86_64.tar.gz
   ./google-cloud-sdk/install.sh
   gcloud init
   ```

3. **Authenticate Google Cloud**:
   ```bash
   gcloud auth login
   gcloud config set project aenzbi-idle
   ```

4. **Link Google Cloud with GitHub**:
   Run the following command to connect your GitHub repository:
   ```bash
   gcloud builds triggers create github \
       --name="github-trigger" \
       --repo-name="aenzbi" \
       --repo-owner="AllyElvis" \
       --branch-pattern=".*" \
       --build-config="cloudbuild.yaml"
   ```

5. **Generate the `cloudbuild.yaml` Configuration**:
   Create a `cloudbuild.yaml` file in your GitHub repository root to define the build steps:
   ```yaml
   steps:
     - name: 'gcr.io/cloud-builders/git'
       entrypoint: 'bash'
       args:
         - '-c'
         - |
           git clone https://github.com/AllyElvis/aenzbi.git /workspace/aenzbi
           cd /workspace/aenzbi
           npm install
           npm run build

   images:
     - gcr.io/$PROJECT_ID/aenzbi-app:latest
   ```

6. **Deploy the App** (Optional):
   If you want to deploy your app to App Engine or Cloud Run:
   - **App Engine**:
     ```yaml
     steps:
       - name: 'gcr.io/cloud-builders/gcloud'
         args:
           - 'app'
           - 'deploy'
     ```
   - **Cloud Run**:
     ```yaml
     steps:
       - name: 'gcr.io/cloud-builders/docker'
         args:
           - 'build'
           - '-t'
           - 'gcr.io/$PROJECT_ID/aenzbi-app:latest'
           - '.'
       - name: 'gcr.io/cloud-builders/gcloud'
         args:
           - 'run'
           - 'deploy'
           - 'aenzbi-app'
           - '--image=gcr.io/$PROJECT_ID/aenzbi-app:latest'
           - '--platform=managed'
           - '--region=us-central1'
     ```

7. **Trigger the Build**:
   Once the trigger is set up, any push to the GitHub repository will automatically trigger a build on Google Cloud.

### Commands Summary:
```bash
gcloud services enable cloudbuild.googleapis.com
gcloud builds triggers create github \
    --name="github-trigger" \
    --repo-name="aenzbi" \
    --repo-owner="AllyElvis" \
    --branch-pattern=".*" \
    --build-config="cloudbuild.yaml"
```

Let me know if you need help with specific parts of the integration or additional features.
