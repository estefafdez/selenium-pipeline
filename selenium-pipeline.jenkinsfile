#!groovy
//Jenkinsfile (Declarative Pipeline)

// --------------------------------------------
// -- CREATED BY: Estefania Fernandez Muñoz --
// -- https://github.com/estefafdez/ --
// --------------------------------------------


// -- Suite Name
def SUITE_NAME

// -- Keep only 15 builds
echo 'Discard old build'
properties([buildDiscarder(logRotator(artifactDaysToKeepStr: '', artifactNumToKeepStr: '', 
    daysToKeepStr: '', numToKeepStr: "15"))])

pipeline {
    agent any
    // -- Display a timestamp on the log.
    options{timestamps()}
    stages {

    // Parameters needed: JOB_FILES_DIRECTORY, JOB_SELENIUM_SUITE, SUITE_NAME
    // ------------------------------------
    // -- STAGE: Initial Configuration
    // ------------------------------------
    stage("Initial Configuration") {
        steps {

            script {
                // -- Set the Directory of the files in the workspace
                JOB_FILES_DIRECTORY = "${workspace}"+"/src/test/resources/files"
                // -- Set the suite name and path
                SUITE_NAME = "src/test/resources/suites/"+"${JOB_SELENIUM_SUITE}"+".xml" 
            }

            // -- Clean Workspace
            echo "Clean Workspace"
            cleanWs()
        }
    }
    
    // Parameters needed: JOB_GIT_BRANCH, JOB_GIT_URL, GIT_CREDENTIAL
    // --------------------------------
    // -- STAGE: Download GIT Code
    // --------------------------------
    stage("Download GIT Code") {
        steps {
            script {
                try {
                   // -- Download GIT Code 
                    echo "Downloading GIT Code from: ${JOB_GIT_URL}. Branch: ${JOB_GIT_BRANCH}"

                    //-- Git checkout
                    git branch: "${JOB_GIT_BRANCH}", changelog: false, credentialsId: "${env.JOB_GIT_CREDENTIAL}", 
                    poll: false, url: "${JOB_GIT_URL}"

                } catch (err) { 
                    echo "The Download GIT Code Stage failed"                                          
                }
            }   
        }
    }

    // Parameters needed: JOB_OS, JOB_BROWSER, SUITE_NAME 
    // --------------------------------
    // -- STAGE: Selenium Test
    // --------------------------------
    stage("Selenium Test") {
        steps {
            script {
                // -- Script to launch Appium Test
                script {
                    try {
                        sh """
                            mvn clean -Dos="${JOB_OS}" -Dbrowser="${JOB_BROWSER}" -DtestSuite="${SUITE_NAME}" test
                        """
                        echo "Publishing Junit Results"
                        junit "**/target/surefire-reports/junitreports/*.xml"

                    } catch (err) { 
                        echo "Archiving Screenshot of the Failed Tests"
                        archiveArtifacts "**/screenshot/*.png"
                        echo "Publishing Junit Results"
                        junit "**/target/surefire-reports/junitreports/*.xml"
                    }
                }   
            }
        }
    }

    //------------------------------------
    // -- STAGE: SonarQube
    // ------------------------------------
    stage("SonarQube") {
        when {
                expression { return JOB_ENABLE_SONAR == "true" }
        }
        steps {
            script {
                try {
                    echo "Analizing the project with SonarQube."
                    withSonarQubeEnv("Sonar Server") {
                        sh "mvn sonar:sonar"
                    }
                } catch (err) { 
                    echo "The SonarQube analysis failed"                                          
                }
            }  
        }
    }

    } // -- End stage
    // ----------------------------------------------
    // -- STAGE: Post Build actions
    // ----------------------------------------------
    post ("Post-Build Actions"){
        always {
            //-- Delete Directory
            echo "Delete Directory"
            deleteDir() 
        }
        success ("JOB SUCCESS"){
            echo "Success Job"
        }

        failure ("JOB FAILURE"){
            echo "Failure Job"
        }

        unstable ("JOB UNSTABLE") {
            echo "Unstable Job"
        }

        aborted ("JOB ABORTED") {
            echo "Aborted Job"
        }
    }
}
