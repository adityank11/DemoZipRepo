pipeline {
    agent any

    environment {
        REPO_A = 'https://github.com/adityank11/JarFileRepo'
        REPO_B = 'https://github.com/adityank11/DemoZipRepo'
        BRANCH_A = 'main'
        BRANCH_B = 'main'
        ZIP_NAME = 'IKPResMonitor.zip'
        GIT_CREDS = 'github-creds'  // ID of Jenkins GitHub credentials
    }

    stages {
        stage('Clone Repo A') {
            steps {
                dir('repo-a') {
                    git branch: "${BRANCH_A}", credentialsId: "${GIT_CREDS}", url: "${REPO_A}"
                }
            }
        }

        stage('Build with Maven') {
            steps {
                dir('repo-a') {
                    sh 'mvn clean package'
                }
            }
        }

        stage('Package ZIP') {
            steps {
                dir('repo-a') {
                    script {
                        def targetDir = sh(script: "ls -d target/", returnStdout: true).trim()
                        sh "zip -j ../${ZIP_NAME} ${targetDir}/*.jar pom.xml"
                    }
                }
            }
        }

        stage('Clone Repo B') {
            steps {
                dir('repo-b') {
                    git branch: "${BRANCH_B}", credentialsId: "${GIT_CREDS}", url: "${REPO_B}"
                }
            }
        }

        stage('Commit ZIP to Repo B') {
            steps {
                dir('repo-b') {
                    sh '''
                        cp ../${ZIP_NAME} .
                        git config user.name "adityank11"
                        git config user.email "kelkaradityan17@gmail.com"
                        git add ${ZIP_NAME}
                        git commit -m "Add ${ZIP_NAME} from repo A build" || echo "No changes to commit"
                        git push origin ${BRANCH_B}
                    '''
                }
            }
        }
    }
}
