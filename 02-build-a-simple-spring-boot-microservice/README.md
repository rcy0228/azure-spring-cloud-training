# Exercise 2 - Build a simple Spring Boot microservice

In this section, we'll build a simple Spring boot microservice and deploy it to Azure Spring Cloud. This will give us a starting point for adding Spring Cloud technologies in later sections.

## Task 1 : Create a simple Spring Boot microservice

>💡 __Note:__ All subsequent commands in this workshop should be run from the same directory, except where otherwise indicated via `cd` commands.

1. In an __empty__ directory in git bash execute the curl command line mentioned below:

```bash
curl https://start.spring.io/starter.tgz -d dependencies=web -d baseDir=simple-microservice -d bootVersion=2.3.8 -d javaVersion=1.8 | tar -xzvf -
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
        return "Hello from Azure Spring Cloud\n";
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

2. Run the `curl` command for requesting the `/hello` endpoint where it should return the "Hello from Azure Spring Cloud" as the message.

    ```bash
    curl http://127.0.0.1:8080/hello
    ```
    ![HelloController](media/MJA-ex2-04.png)

3. Finally, kill running app by using the below mentioned command.

     ```bash
     kill %1
     ```

## Task 4 : Create and deploy the application on Azure Spring Cloud

This section shows how to create an app instance and then deploy your code to it.

1. In order to create the app instance graphically, you can navigate back to Azure portal and look for your Azure Spring Cloud instance in your resource group.

   ![Cloud Spring in rg](media/MJA-ex2-01.png)

2. Click on the **Apps** under **Settings** on the navigation sidebar.

   ![Apps under cloud spring ](media/MJA-ex2-02.png)

3. Click on **+ Create App** link at the top of the **Apps** page.

   ![App creation ](media/createapp.png)

4. Create a new application named **simple-microservice (1)** and leave the remaining settings to default then click on **Create (2)**

      ![Create application](media/appname.png)


   >💡 __Note:__ Alternatively, you can use the command line to create the app instance, which is easier. If you performed till step 4, skip this Note and continue with Step 5.

   >**Note:** Replace the **DID** with **<inject key="DeploymentID" enableCopy="True"/>** value, you can also find it from Environment details page and run the below given command in **Git Bash**

   ```bash
   az spring-cloud app create -n simple-microservice -s azure-spring-cloud-lab-DID -g spring-cloud-workshop-DID --assign-endpoint true --cpu 1 --memory 1Gi --instance-count 1
   ```

5. Now you can build your **simple-microservice** project and deploy it to Azure Spring Cloud by running the below command.

     ```bash
     cd simple-microservice
     ./mvnw clean package
     az spring-cloud app deploy -n simple-microservice --jar-path target/demo-0.0.1-SNAPSHOT.jar
     cd ..
     ```

6. This creates a jar file on your local disk and uploads it to the app instance you created in the preceding step.  The `az` command will output a result in JSON.  You don't need to pay attention to this output right now, but in the future, you will find it useful for diagnostic and testing purposes.

## Task 5 : Test the project in the cloud

1. Navigate back to Azure Portal, From the resource group **spring-cloud-workshop-<inject key="DeploymentID" enableCopy="false"/>** select the Azure Spring Cloud instance named **azure-spring-cloud-lab-<inject key="DeploymentID" enableCopy="false"/>**.

    ![Cloud Spring in rg](media/MJA-ex2-01.png)

2. Click **Apps** in the **Settings** section of the navigation pane and select **simple-microservice**

3. Click on 'See more' to see **Test Endpoint**

   ![See More](media/seemore.png)

4. Mouse over the URL labeled as **Test Endpoint** and click the clipboard icon that appears.  

   ![Endpoint](media/microservice-endpoint.png)
    
<!--- 6. This will give you something like:

   `https://primary:BBQM6nsYnmmdQREXQINityNx63kWUbjsP7SIvqKhOcWDfP6HJTqg27klMLaSfpTB@rwo1106f.test.azuremicroservices.io/simple-microservice/default/`
   >💡 Note the text between `https://` and `@`.  These are the basic authentication credentials, without which you will not be authorized to access the service.

7. If you get **"503 Service Temporarily Unavailable"** or **"WhiteLabel Error"** Page as shown below,

   ![Error](media/endpoint-error.png)

   ![Error2](media/error02.png)
   
   - Click on assign endpoint and wait until the endpoint has been assigned and unassign the endpoint soon after. 

   ![assign endpoint](media/simple-microservice-endpoint-assign.png)

-->

5. You can now use CURL again to test the `/hello` endpoint, this time served by Azure Spring Cloud.  For example.

```bash
curl https://primary:...simple-microservice/default/hello/
```

6. Append `hello/` at the end of the URL.  Failure to do this will result in a "404 not found".

   <!--- ![Endpoint](media/hello-from-spring-cloud.png) -->

7. If successful, you should see the message: `Hello from Azure Spring Cloud`.

    ![Endpoint](media/curl-hello-from-spring-cloud.png)

## Conclusion

Congratulations, you have deployed your first Spring Boot microservice to Azure Spring Cloud!
