pipeline {
    agent any

    environment {
        ZIP_NAME = "build_package.zip"
        REPO_A = "https://github.com/adityank11/JarFileRepo"
        REPO_B = "https://github.com/adityank11/DemoZipRepo"
        CREDENTIALS_ID = 'github-creds'
    }

    stages {
        stage('Clone Repo A') {
            steps {
                dir('JarFileRepo') {
                    git branch: 'main', url: "${REPO_A}", credentialsId: "${CREDENTIALS_ID}"
                    bat 'dir'
                }
            }
        }

        stage('Build with Maven') {
            steps {
                dir('JarFileRepo') {
                    bat 'mvn clean package'
                }
            }
        }

        stage('Package ZIP') {
            steps {
                dir('JarFileRepo') {
                    script {
                      bat '''
                      for /f %%i in ('dir /b target\\*.jar') do (
                      copy /Y target\\%%i temp_%%i  
                      copy /Y pom.xml temp_pom.xml  
                      tar -a -c -f ..\\build_package.zip temp_%%i temp_pom.xml  
                      del temp_%%i  
                      del temp_pom.xml
                      )
                      '''
                  }
                }
            }
        }

        stage('Clone Repo B') {
            steps {
                dir('DemoZipRepo') {
                    git branch: 'main', url: "${REPO_B}", credentialsId: "${CREDENTIALS_ID}"
                }
            }
        }

        stage('Commit ZIP to Repo B') {
            steps {
                script {
                    bat "copy build_package.zip DemoZipRepo"
                    dir('DemoZipRepo') {
                        bat '''
                            git config user.email "kelkaradityan17@gmail.com"
                            git config user.name "adityank11"
                            git add .
                            git commit -m "Add zipped build artifact"
                            git push origin main
                        '''
                    }
                }
            }
        }
    }
}