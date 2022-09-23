# Exercise 2 - Build a simple Spring Boot microservice

In this section, we'll build a simple Spring boot microservice and deploy it to Azure Spring Apps. This will give us a starting point for adding Spring Apps technologies in later sections.

## Task 1 : Create a simple Spring Boot microservice

>💡 __Note:__ All subsequent commands in this workshop should be run from the same directory, except where otherwise indicated via `cd` commands.

1. In an __empty__ directory in git bash execute the curl command line mentioned below:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web -d baseDir=simple-microservice -d bootVersion=2.7.0 -d javaVersion=17 | tar -xzvf -
```

> **Info :** We force the Spring Boot version to be 2.3.8.

## Task 2 : Add a new Spring MVC Controller

1. Now minimize the Git Bash window and navigate to the path `C:\Users\demouser\simple-microservice\src\main\java\com\example\demo` in File Explorer.

   ![Path](media/folder-path.png)

2. Open a new notepad, and paste the below code :

   ```java
   package com.example.demo;

   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RestController;

   @RestController
   public class HelloController {

       @GetMapping("/hello")
       public String hello() {
           return "Hello from Azure Spring Apps\n";
       }
   }
   ```

3. Save the file next to `DemoApplication.java` in the `C:\Users\demouser\simple-microservice\src\main\java\com\example\demo` as `HelloController.java` by changing the **save as type** to all files and then **save** as shown below.

   ![HelloController](media/hello-controller-java.png)


## Task 3 : Test the project locally

1. Now navigate back to **Git Bash** and run the project:

    ```bash
    cd simple-microservice
    ./mvnw spring-boot:run &
    cd ..
   ```

   > Note: When the control is stuck during the deployment process, you can press enter to make the process run in backgorund and proceed to next steps to access the endpoint. This is because control will be checking for the available port to host the endpoint.
   
   > Note: In somecases you may face an issue that the project build failure due to **Port already in use** , then you can terminate the process which is running in the port by running the below given commands in Command Prompt. After terminating the process, you need to re-run the Step-1 commands of Task-3 in Git Bash.
   
   ```bash
   netstat  -ano  |  findstr  < Enter Port Number > 
   taskkill  /F  /PID  < Enter Process Id >
   ```
   
2. Run the `curl` command for requesting the `/hello` endpoint where it should return the "Hello from Azure Spring Apps" as the message.

    ```bash
    curl http://127.0.0.1:8080/hello
    ```
    ![HelloController](media/MJA-ex2-04.png)

3. Finally, kill running app by using the below mentioned command.

     ```bash
     kill %1
     ```

## Task 4 : Create and deploy the application on Azure Spring Apps

This section shows how to create an app instance and then deploy your code to it.

1. In order to create the app instance graphically, you can navigate back to Azure portal and look for your Azure Spring Apps instance in your resource group.

   ![Cloud Spring in rg](../media/azurespringapps.png)

2. Click on the **Apps** under **Settings** on the navigation sidebar.

   ![Apps under cloud spring ](../media/selectapps.png)

3. Click on **+ Create App** link at the top of the **Apps** page.

   ![App creation ](../media/createnewapp.png)

4. On **Create App** page, follow the below details:

    - **App Name** : Enter **simple-microservice (1)**
    - **Deployment Type** : Choose **Artifacts(Java/.Net core) (2)** from the drop-down
    - **Runtime Platform** : Choose **Java 17 (3)** from the drop-down
    - Click on **Create (4)**
    

      ![Create application](../media/createapp1.png)


   >💡 __Note:__ Alternatively, you can use the command line to create the app instance, which is easier. If you performed till step 4, skip this Note and continue with Step 5.

   >**Note:** Replace the **DID** with **<inject key="DeploymentID" enableCopy="True"/>** value, you can also find it from Environment details page and run the below given command in **Git Bash**

   ```bash
   az spring app create -n simple-microservice -s azure-spring-apps-lab-DID --runtime-version Java_17
   ```

5. Now you can build your **simple-microservice** project and deploy it to Azure Spring Apps by running the below command.

   >**Note:** Replace the **DID** with **<inject key="DeploymentID" enableCopy="True"/>** value, you can also find it from Environment details page and run the below given command in **Git Bash**

     ```bash
     cd simple-microservice
     ./mvnw clean package
     az spring app deploy -n simple-microservice -s azure-spring-apps-lab-DID --jar-path target/demo-0.0.1-SNAPSHOT.jar
     cd ..
     ```

6. This creates a jar file on your local disk and uploads it to the app instance you created in the preceding step.  The `az` command will output a result in JSON.  You don't need to pay attention to this output right now, but in the future, you will find it useful for diagnostic and testing purposes.

## Task 5 : Test the project in the cloud

1. Navigate back to Azure Portal, From the resource group **spring-apps-workshop-<inject key="DeploymentID" enableCopy="false"/>** select the Azure Spring Apps instance named **azure-spring-apps-lab-<inject key="DeploymentID" enableCopy="false"/>**.

    ![Cloud Spring in rg](../media/azurespringapps.png)

2. Click **Apps** in the **Settings** section of the navigation pane and select **simple-microservice**


3. From the **Overview** tab, mouse over the URL labeled as **Test Endpoint** and click the clipboard icon that appears.  

   ![Endpoint](../media/copytestendpoint.png)
    
<!--- 6. This will give you something like:

   `https://primary:BBQM6nsYnmmdQREXQINityNx63kWUbjsP7SIvqKhOcWDfP6HJTqg27klMLaSfpTB@rwo1106f.test.azuremicroservices.io/simple-microservice/default/`
   >💡 Note the text between `https://` and `@`.  These are the basic authentication credentials, without which you will not be authorized to access the service.

7. If you get **"503 Service Temporarily Unavailable"** or **"WhiteLabel Error"** Page as shown below,

   ![Error](media/endpoint-error.png)

   ![Error2](media/error02.png)
   
   - Click on assign endpoint and wait until the endpoint has been assigned and unassign the endpoint soon after. 

   ![assign endpoint](media/simple-microservice-endpoint-assign.png)

-->

5. You can now use CURL again to test the `/hello` endpoint, this time served by Azure Spring Apps.  For example.

```bash
curl https://primary:...simple-microservice/default/hello/
```

6. Append `hello/` at the end of the URL.  Failure to do this will result in a "404 not found".

   <!--- ![Endpoint](media/hello-from-spring-cloud.png) -->

7. If successful, you should see the message: `Hello from Azure Spring Apps`.

    ![Endpoint](media/curl-hello-from-spring-cloud.png)

## Conclusion

Congratulations, you have deployed your first Spring Boot microservice to Azure Spring Apps!
