# selenium-pipeline
Run your Selenium Test using this declarative pipeline. 

# Pre-requisites.

Before we start you need to:

1. Have a Jenkins Instance with an admin user. 
2. Have the browser you are going to use installed on your server (this pipeline do not use Docker for now!)
3. Have a maven project with your test code. 

Also, you need to review the pipeline and check the path of the following variables:

- __JOB_FILES_DIRECTORY__: This parameter set the Directory of the files in the workspace. The current value is: "${workspace}"+"/src/test/resources/files".
- __SUITE_NAME__: This parameter set the suite name and path. The current value is: "src/test/resources/suites/"+"${JOB_SELENIUM_SUITE}"+".xml". 
- __Selenium Test Stage__: On this stage, if the test failed, you need to check the path where the screenshot are saved. The current value is: "**/screenshot/*.png".
- __SonarQube Stage__: In order to be able to run Sonar on your test code, you need to define the Sonarqube server on your POM.xml. 

# Configure your Jenkins Job. 

## Create a Pipeline Job. 

First, you need to install the pipeline plugins on Jenkins to be able to create a Pipeline job. 
Once you have installed all the plugins you need, you need to create a new job:

Left Menu -> New Item -> Add job title -> Select Pipeline. 

## Add parameters to the job execution.

On the Jenkins job, you need to add several parameters by clicking the option **This project is parameterized**:

- __JOB_GIT_URL__: Code Repository URL (*String Parameter*)
- __JOB_GIT_BRANCH__: Branch to download the project code to. You can add one or several branches (*Choice Parameter*)
- __JOB_GIT_CREDENTIAL__: You need to define the user to download the project. You can fill this parameter using the users defined on your Jenkins System Credential. (*Credentials Parameter*)
- __JOB_OS__: Operative System in which you are going to launch the Tests. You can add several OS such as Windows, Linux or MacOSX (*Choice Parameter*). 
- __JOB_BROWSER__: Browser in which you are going to launch the Tests. You can add several Browsers, such as Firefox, Internet Explorer or Chrome (*Choice Parameter*)
- __JOB_SELENIUM_SUITE__: Name of the Test Suite to execute. You can add several test suites, for example acceptance or regression. (*Choice Parameter*)
- __JOB_ENABLE_SONAR__: Variable to define if the code could be analyzed by Sonarqube. The Sonar Server should be defined on the POM. (*Boolean Parameter*)

## Configure the pipeline section.

On the pipeline section of the job you need to set this repository:

- __URL__: https://github.com/estefafdez/selenium-pipeline.git
- __Credentials__: you do not need it. This is a **public** repository.
- __Branch__: master (There is only one for now!)
- __Script Path__: selenium-pipeline.jenkinsfile

## Save the Job and click on RUN!

When your pipeline job is ready and all the parameters are set and filled, you just need to click on play and enjoy! 


