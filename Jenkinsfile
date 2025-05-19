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
                    copy target\\%%i .
                    copy pom.xml .
                    tar -a -c -f ..\\build_package.zip %%i pom.xml
                    del %%i
                    del pom.xml
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
                    bat "copy JarFileRepo\\${ZIP_NAME} DemoZipRepo\\"
                    dir('DemoZipRepo') {
                        bat '''
                            git config user.email "kelkaradityan17@gmaail.com"
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