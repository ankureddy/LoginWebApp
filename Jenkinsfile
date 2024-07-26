pipeline {
    agent any
    tools {
        maven "Maven"
        jdk "java8"
    }

    environment {
        // This can be nexus3 or nexus2
        NEXUS_VERSION = "nexus3"
        // This can be http or https
        NEXUS_PROTOCOL = "http"
        // Where your Nexus is running
        NEXUS_URL = "65.2.63.152:8081"
        // Repository where we will upload the artifact
        NEXUS_REPOSITORY = "LoginWebApp"
        // Jenkins credential id to authenticate to Nexus OSS
        NEXUS_CREDENTIAL_ID = "nexus_credential"
        ARTIFACT_VERSION = "${BUILD_NUMBER}"
    }

    stages {
        stage("Check out") {
            steps {
                script {
                    git credentialsId: 'Git-03', url: 'https://github.com/ankureddy/LoginWebApp.git'
                }
            }
        }

        stage("mvn build") {
            steps {
                script {
                    sh "mvn clean package"
                }
            }
        }

        stage("publish to nexus") {
            steps {
                script {
                    // Read POM xml file using 'readMavenPom' step , this step 'readMavenPom' is included in: https://plugins.jenkins.io/pipeline-utility-steps
                    pom = readMavenPom file: "pom.xml";
                    // Find built artifact under target folder
                    filesByGlob = findFiles(glob: "target/*.${pom.packaging}");
                    // Print some info from the artifact found
                    echo "${filesByGlob[0].name} ${filesByGlob[0].path} ${filesByGlob[0].directory} ${filesByGlob[0].length} ${filesByGlob[0].lastModified}"
                    // Extract the path from the File found
                    artifactPath = filesByGlob[0].path;
                    // Assign to a boolean response verifying If the artifact name exists
                    artifactExists = fileExists artifactPath;

                    if(artifactExists) {
                        echo "*** File: ${artifactPath}, group: ${pom.groupId}, packaging: ${pom.packaging}, version ${pom.version}";

                        nexusArtifactUploader(
                            nexusVersion: 'nexus3',
                            protocol: 'http',
                            nexusUrl: '65.2.63.152:8081',
                            groupId: 'pom.com.ranjitswain',
                            version: 'pom.1.0-SNAPSHOT',
                            repository: 'LoginWebApp',
                            credentialsId: 'nexus_credential',
                            artifacts: [
                                // Artifact generated such as .jar, .ear and .war files.
                                [artifactId: 'pom.LoginWebApp',
                                classifier: '',
                                file: artifactPath,
                                type: pom.packaging]
                            ]
                        );

                    } else {
                        error "*** File: ${artifactPath}, could not be found";
                    }
                }
            }
        }
        
