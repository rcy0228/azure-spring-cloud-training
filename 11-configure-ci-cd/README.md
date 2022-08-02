# Exercise 11 - Configure CI/CD

In this section, we will use GitHub Actions to implement continuous deployment to Azure Spring Apps. For simplicity, we will not implement blue-green deployments in this section, but don't hesitate to come back and add blue-green deployments after completing the remainder of the tutorial.

---

Our microservices and gateway are easy to deploy manually, but it is of course better to automate all those tasks! We are going to use [GitHub actions](https://github.com/features/actions) as a Continuous Integration / Continuous Deployment platform (or CI/CD for short). This configuration is rather simple, so it should be trivial to port it to another CI/CD platform.

We are going to automate the deployment of the `weather-service` microservice that was developed in exercise 7. It is exactly the same configuration that would need to be done for the `city-service` microservice and the gateway, so if you want to automate them too, you can just copy/paste what is being done in the current guide.

## Task 1 : Configure GitHub

1. [Create a new GitHub repository](https://github.com/new) with the name `weather-service` and commit the code from the `weather-service` microservice into that repository:

   ![New Repo](media/new-repo.png)

2. Open a fresh instance of Git Bash and login to your azure account using ```az login``` command.

3. Enter the following commands and substitute the username/email of your Github account.

   ```
    git config --global user.email "user@mail.com"
    git config --global user.name "username"

   ```

> 🛑 Make sure you substitute the Git URL from your own github repository (make sure you use the HTTPS URL, not the SSH URL). This should be a different repository than the one you used to store configuration in exercise 4. If a login dialog appears, log in with your regular GitHub credentials.

```bash
cd weather-service
git init
git add .
git add -f .mvn
git commit -m 'Initial commit'
git remote add origin <GIT HTTPS URL HERE>
git push origin master
cd ..
```

4. You now need to allow access from your GitHub workflow to your Azure Spring Apps instance. Open up a terminal and type the following command.

> Please ensure to replace the **DID** with the **<inject key="DeploymentID" enableCopy="True"/>** value, it can also be obtained from the **Environmnet Details** tab.

```
AZ_RESOURCE_GROUP=spring-apps-workshop-DID
```

```bash
# Prevents a Git bash issue. Not necessary outside of Windows:
export MSYS_NO_PATHCONV=1

# Get the ARM resource ID of the resource group
RESOURCE_ID=$(az group show --name "$AZ_RESOURCE_GROUP" --query id -o tsv)

# Create a service principal with a Contributor role to the resource group.
SPNAME="sp-$(az spring list --query '[].name' -o tsv)"
az ad sp create-for-rbac --name "${SPNAME}" --role contributor --scopes "$RESOURCE_ID" --sdk-auth
```

5. This should output a JSON text, that you need to copy.

6. Then, in your GitHub project, select `Settings >` expand `Secrets >` click on ` Actions` and add a new secret called `AZURE_CREDENTIALS`. Paste the JSON text you just copied into that secret.

## Task 2 : Create a GitHub Action

1. Inside the `weather-service` directory, create a new directory called `.github/workflows` and add a file called `azure-spring-cloud.yml` in it. This file is a GitHub workflow and will use the secret we just configured above to deploy the application to your Azure Spring Apps instance.

2. In that file, copy/paste the following content, performing the indicated substitutions:

>🛑 You must substitute the name of your Azure Spring App instance for `<AZ_SPRING_CLOUD_NAME>` as **azure-spring-apps-lab-<inject key="DeploymentID" enableCopy="false" />** and the name of the resource group for `<AZ_RESOURCE_GROUP>` as **spring-apps-workshop-<inject key="DeploymentID" enableCopy="false" />** in the YAML below.

```yaml
name: Build and deploy to Azure Spring Apps

on: [push]

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v2
    - name: Set up Java
      uses: actions/setup-java@v2
      with:
        distribution: 'temurin'
        java-version: 17
        check-latest: false
        cache: 'maven'
    - name: Build with Maven
      run: mvn package -DskipTests
    - name: Login to Azure Spring Apps
      uses: azure/login@v1
      with:
        creds: ${{ secrets.AZURE_CREDENTIALS }}
    - name: Get Subscription ID
      run: |
        echo "SUBSCRIPTION_ID=$(az account show --query id --output tsv --only-show-errors)" >> $GITHUB_ENV
      shell: bash
    - name: Deploy to Azure Spring Apps
      uses: azure/spring-cloud-deploy@v1
      with:
        action: deploy
        azure-subscription: ${{ env.SUBSCRIPTION_ID }}
        service-name: <AZ_SPRING_APPS_NAME>
        app-name: weather-service
        use-staging-deployment: false
        package: target/demo-0.0.1-SNAPSHOT.jar

```

3. This workflow does the following:

- It sets up the JDK

- It compiles and packages the application using Maven

- It authenticates to Azure Spring Apps using the credentials we just configured

- It adds the Azure Spring Apps extensions to the Azure CLI (this step should disappear when the service is in final release)

- It deploys the application to your Azure Spring Apps instance

This workflow is configured to be triggered whenever code is pushed to the repository.
There are many other [events that trigger GitHub actions](https://help.github.com/en/articles/events-that-trigger-workflows). You could, for example, deploy each time a new tag is created on the project.

## Task 3 : Test the GitHub Action

1. You can now commit and push the `azure-spring-cloud.yml` file we just created.

2. Going to the `Actions` tab of your  GitHub project, you should see that your project is automatically built and deployed to your Azure Spring Apps instance:

   ![GitHub workflow](media/01-github-workflow.png)


> **NOTE!** If the Build fails, delete the exisiting `azure-spring-cloud.yml` from `.github/workflows` and create it again, make sure to substitute the name of your Azure Spring Apps instance and resource group. 

Congratulations! Each time you `git push` your code, your microservice is now automatically deployed to production.

---
