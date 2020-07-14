## Selenium Grid Integration With Docker

##### Framework Architecture: (Slide 3)

* In this architecture, we have a Maven project based on Java/Selenium automation framework.
* Here our final goal, is to execute our selenium tests parallel on multiple browsers using a Jenkins job.
* To achieve this, we are going to have Selenium Grid Integrated with Docker.
* We will see how docker helps in setting up the infrastructure required for our selenium grid tests.

##### **Selenium Grid – Overview: (Slide 4)**

* Selenium Grid is used to run our tests on different OS/browsers in parallel. It's basically uses master-slaves (or hub-nodes) concept – where there is one master/hub and there are few slaves/nodes registered to the master/hub. When we send our tests to the master/hub for execution, based on the browser/OS requirements of the test, master will route the request to the appropriate nodes and get them executed. Thus it minimizes the overall execution time of the tests.
* Here let me show you on what are my tests are related to and how are they designed as per Page Object Model pattern.
* Show all the three classes under test.
* automationpractice.xml and webappsecurity.xml

##### Selenium Grid- Setup Manually: (Slide 5)

* Download selenium standalone jar : selenium-server-standalone-3.141.59.jar
* Open Command Prompt(bash1) and navigate to the selenium server file path and run below command.
  * java -jar selenium-server-standalone-3.141.59.jar -role hub
* Open another Command Prompt (bash2) and navigate to the selenium server file path and run below command.
  * java -Dwebdriver.gecko.driver="/Users/Arun/Nisum_Pluralsight_Trainings/Selenium_Docker_Framework_Presentation/SeleniumDockerFramework/src/test/resources/geckodriver" -jar selenium-server-standalone-3.141.59.jar -role node -hub http://10.0.0.69:4444/grid/register
* Open another Command Prompt (bash3) and navigate to the selenium server file path and run below command.
  * java -Dwebdriver.gecko.driver="/Users/Arun/Nisum_Pluralsight_Trainings/Selenium_Docker_Framework_Presentation/SeleniumDockerFramework/src/test/resources/geckodriver" -jar selenium-server-standalone-3.141.59.jar -role node -hub http://10.0.0.69:4444/grid/register
* mvn clean package -DskipTests
* Navigate to target folder 
  * java -cp selenium-docker.jar:selenium-docker-tests.jar:libs/* -DBROWSER=firefox -DHUB_HOST=10.0.0.69 org.testng.TestNG ../automationpractice.xml

##### Docker: (Slide 6)

* Docker is a manager of Infrastructure. It will be able to package a software and all its dependencies to run as a container. You can deploy the software, packaged as a docker image, in any machine where docker is installed. It, kind of, separates the software from the hardware – so the developer / test engineer can rest assured that the application will run on any machine regardless of any customized settings that machine might have that could differ from the machine used for writing and testing the code.

##### **Selenium Grid Setup using Docker**

* Setup Hub

  * ```bash
    docker run -d -p 4444:4444 --name selenium-hub selenium/hub
    ```

* Setup nodes with Chrome & Firefox and register to the hub

  * ```bash
    docker run -d --link selenium-hub:hub selenium/node-chrome
    docker run -d --link selenium-hub:hub selenium/node-firefox
    ```

* You can access the selenium-grid console using http://localhost:4444/grid/console

* If you need one more chrome node instance, run this command.

  * ```bash
    docker run -d --link selenium-hub:hub selenium/node-chrome
    ```

* To shutdown the docker-grid infrastructure, run the below commands.

  * ```bash
    docker stop $(sudo docker ps -a -q)
    docker rm $(sudo docker ps -a -q)
    ```

* Challenges:
  
  * Need to start/stop the containers one by one to setup the hub and nodes.

##### **Selenium Grid Setup using Docker-Compose:**

* As part of our Selenium Grid we need to have 1 hub and few nodes like chrome and firefox. We can define all these services in a file called docker-compose.yml file and bring the entire infrastructure up and running by using a single command.

  * ```bash
    docker-compose up -d
    docker-compose ps -a
    ```

* If you want more instance of chrome, you can run below command

  * ```bash
    docker-compose scale chrome=5
    ```

* Entire grid can be brought down by issuing a single command

  * ```bash
    docker-compose down
    ```

##### **Challenges** **in** **Test** **Execution:**

We already simplified our selenium grid set up using docker, but there are few challenges where our tests might fail due to below

* Test depends on specific version of java (like java 8)
* Host should have maven or Ant or Gradle or other build tools installed
* Remote slaves need to be set up with the build tools, java and any dependent jar files to execute the tests.

##### Docker File:

* To overcome these problems, we can create a Docker file which will have instructions on how a docker image should be build.
* ▪Compare to Similar Java term: .java

##### Docker Image

* After you build a Docker file , an Image is created. A Docker Image is the snapshot of your virtual machine, it will have all the instructions mentioned in the Dockerfile and it is ready to be used and not in running state.
  * `docker build -t='ayellapu/selenium-docker' .`
* Compare to Similar Java term: .class/.jar

##### Docker Container

* Docker container is actual running virtual machine which was created from Docker image.

* We can create multiple containers from single Docker image.

* Packing an application including the Runtime environment.

* Compare to Similar Java term: instances/ Objects

* ```bash
  docker run -d -e HUB_HOST=10.0.0.69 -e BROWSER=firefox -e MODULE=webappsecurity.xml ayellapu/selenium-docker:latest
  ```

##### Update Docker-compose yaml with required services

* ```bash
  docker-compose up -d
  docker-compose ps -a
  docker-compose down
  ```

##### Docker Hub

* Docker Hub is a cloud-based repository in which Docker users and partners create, test, store and distribute container images.
* Through Docker Hub, a user can access public, open source image repositories, as well as use a space to create their own private repositories, automated build functions, webhooks and work groups.

##### Integrating GitHub and Jenkins

* Show in Github code related to test framework(selenium-docker)
* Show in Github code related to (selenium-docker-runner)





