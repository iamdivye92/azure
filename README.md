## Deploying a Flask API to Azure Web App Using GitHub Actions
This project deploys a simple Flask API to an Azure Web App using GitHub Actions. It automates the build and deployment process, ensuring that updates are automatically pushed to Azure whenever changes are made to the code. The workflow simplifies continuous integration and deployment for the Flask application.




- **Secrets and Variables**: Basic knowledge of handling secrets and environment variables.
- **Cloud Service Access**: Access to a cloud service and a text editor or IDE.
- **Web Browser**: For accessing project repositories and references.


## Workflow Overview

### **Workflow Overview**

This workflow automates the deployment of a Flask API to Azure Web App using GitHub Actions. When changes are pushed to the main branch of the GitHub repository, the workflow is triggered. It first sets up the Python environment and installs the necessary dependencies. The Flask API is then packaged and prepared for deployment. The application is deployed to the Azure Web App using the publish profile, and finally, the deployment status is checked to ensure the app is running correctly. This process facilitates continuous integration and deployment, ensuring automated updates and maintenance for the Flask application.


## Workflow Steps

### **Step 1: Create a Basic Flask API**

Start by setting up a simple Flask API. Create a new directory for your project and inside it, set up a Python virtual environment. Install Flask and create a file named `app.py` with the following code:

```
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, Azure!'

if __name__ == '__main__':
    app.run(debug=True)
```



 

### **Step 2 : Install Azure CLI**

Download and install the Azure CLI from the [official Microsoft website](https://learn.microsoft.com/en-us/cli/azure/). Follow the installation instructions for your operating system and login via terminal . :

### **Step 3 : Create a Resource Group**

Use the Azure CLI to create a resource group for your Azure Web App. Replace <YourResourceGroupName> with your chosen name and <YourRegion> with the desired location:

```bash
az group create --name <YourResourceGroupName> --location <YourRegion>
```    
 



### **Step 4 : Create an App Service Plan**

Use the Azure CLI to create an App Service Plan, which defines the region and pricing tier for your Azure Web App. Replace `<YourAppServicePlanName>` with your desired plan name and `<YourResourceGroupName>` with your resource group name. 

```bash
az appservice plan create --name <YourAppServicePlanName> --resource-group <YourResourceGroupName> --sku F1 --is-linux --location <YourRegion>
```

**Explanation of the Command:**
--name MyAppServicePlan: Sets the App Service Plan name.
--resource-group demo: Specifies the resource group.
--sku F1: Chooses the Free pricing tier.
--is-linux: Uses a Linux environment.
--location westeurope: Defines the Azure region.

## STEP 6 Create a Web App

Use the Azure CLI to create a Web App within the App Service Plan. Replace `<YourWebAppName>`, `<YourResourceGroupName>`, `<YourAppServicePlanName>`, and `<YourRegion>` with your chosen values:

```bash
az webapp create --name <YourWebAppName> --resource-group <YourResourceGroupName> --plan <YourAppServicePlanName> --runtime "PYTHON|3.9" --location <YourRegion>
```

**Explanation of the Command:**

-   `--name flask-testing-env`: The name of your Web App.
-   `--resource-group demo`: The resource group in which the Web App will be created.
-   `--plan MyAppServicePlan`: The App Service Plan that the Web App will use.
-   `--runtime "PYTHON|3.9"`: Specifies that the Web App will use Python version 3.9.
-   `--location westeurope`: The Azure region where the Web App will be deployed.

### **Step 7: Retrieve Deployment Credentials**

Retrieve the deployment credentials for your Web App, which will be used to configure GitHub Actions for automatic deployment. Use the Azure CLI to list the publishing profiles of your Web App:
```bash
az webapp deployment list-publishing-profiles --name <YourWebAppName> --resource-group <YourResourceGroupName> --xml
```

**Explanation of the Command:**

-   `--name flask-testing-env`: The name of your Web App.
-   `--resource-group demo`: The resource group where your Web App is located.
-   `--xml`: Outputs the deployment credentials in XML format.




### **Step 8: Configure GitHub Repository with Deployment Credentials**

To automate the deployment of your Flask application using GitHub Actions, you need to add the deployment credentials from Azure to your GitHub repository as secrets.

1.  **Navigate to Your GitHub Repository:** Go to your GitHub repository where your Flask application code is hosted.
    
2.  **Open Repository Settings:** Click on the "Settings" tab of your repository.
    
3.  **Add Secrets:**
    
    -   Under the "Security" section, click on "Secrets and variables" and then select "Actions".
    -   Click the "New repository secret" button and  add a new secret.
4.  

Adding these secrets will allow GitHub Actions to access your Azure Web App credentials securely, enabling continuous deployment of your application.

### **Step 9: Create a GitHub Actions Workflow File**

Now, create a GitHub Actions workflow file to automate the deployment of your Flask application to Azure. Follow these steps to set up the workflow:

1.  **Create Workflow Directory:** Inside your repository, create a directory named `.github/workflows`.
    
2.  **Create Workflow File:** In the `.github/workflows` directory, create a new file named `deploy.yml`.
    
3.  **Define the Workflow:** :

```bash

name: Deploy Flask App to Azure

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
    - name: Checkout code
      uses: actions/checkout@v2

    - name: Set up Python
      uses: actions/setup-python@v2
      with:
        python-version: '3.9'

    - name: Install dependencies
      run: |
        python -m pip install --upgrade pip
        pip install -r requirements.txt

    - name: Deploy to Azure Web App
      uses: azure/webapps-deploy@v2
      with:
        app-name: ${{ secrets.AZURE_WEBAPP_NAME }}
        publish-profile: ${{ secrets.AZURE_WEBAPP_PUBLISH_PROFILE }}
```      





### Step Nine
To access the URL of your deployed Azure Web App from the Azure CLI without specifying the app name and resource group, you can use the following command:
```bash
az webapp show --name <YourWebAppName> --resource-group <YourResourceGroup> --query defaultHostName --output tsv
```

Explanation of Command :
-   `az webapp show`: This command retrieves information about the specified Azure Web App.
-   `--name flask-testing-env`: Specifies the name of your web app (`flask-testing-env`).
-   `--resource-group demo`: Specifies the resource group (`demo`) that contains your web app.
-   `--query defaultHostName`: Extracts the default host name (URL) of the web app from the retrieved information.
-   `--output tsv`: Outputs the result in plain text format (tab-separated values), making it easier to copy or use.

After running this command, you will get the URL of your deployed web app, such as `flask-testing-env.azurewebsites.net`. You can open this URL in a web browser to access your Flask
