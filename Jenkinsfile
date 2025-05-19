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
                dir('repo-a') {
                    git url: "${REPO_A}", credentialsId: "${CREDENTIALS_ID}"
                }
            }
        }

        stage('Build with Maven') {
            steps {
                dir('repo-a') {
                    bat 'mvn clean package'
                }
            }
        }

        stage('Package ZIP') {
            steps {
                dir('repo-a') {
                    script {
                        def jarPath = bat(script: 'for /f %%i in (\'dir /b target\\*.jar\') do @echo %%i', returnStdout: true).trim()
                        bat "powershell Compress-Archive -Path target\\${jarPath},monitor.xml -DestinationPath ..\\${ZIP_NAME}"
                    }
                }
            }
        }

        stage('Clone Repo B') {
            steps {
                dir('repo-b') {
                    git url: "${REPO_B}", credentialsId: "${CREDENTIALS_ID}"
                }
            }
        }

        stage('Commit ZIP to Repo B') {
            steps {
                script {
                    bat "copy repo-a\\${ZIP_NAME} repo-b\\"
                    dir('repo-b') {
                        bat '''
                            git config user.email "jenkins@example.com"
                            git config user.name "jenkins"
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
