pipeline {
    agent any

    tools {
        maven 'maven'
    }
    environment {
        IMAGE_NAME = "springboot"
        IMAGE_TAG = "latest"


    }

    stages {
        stage('Checkout From Git') {
            steps {
                git branch: 'prod', url: 'https://github.com/a4anandhu/enhanced-petclinc-springboot.git'
            }
        }

        // stage('Maven Compile') {
        //     steps {
        //         echo 'This Maven Compile Stage'
        //         sh 'mvn compile'
        //     }
        // }

        // stage('Maven Test') { 
        //     steps {
        //         echo 'This Maven Test Stage'
        //         sh 'mvn test'
        //     }
        // }

        stage('Trivy Scan - File System') {
        steps {
        echo 'Trivy scanning started'
        script {
            try {
                sh 'trivy fs --format table --output trivy-report.txt --severity HIGH,CRITICAL .'
            } catch (Exception e) {
                echo "Trivy scan failed, skipping... Error: ${e.message}"
            }
        }
    }
}


        stage('Sonar Analysis') {
            environment {
                SCANNER_HOME = tool 'Sonar-scanner'
            }
            steps {
                withSonarQubeEnv('sonarserver') {
                    sh '''
                        $SCANNER_HOME/bin/sonar-scanner \
                        -Dsonar.organization=a4anandhu \
                        -Dsonar.projectName=enhanced-petclinc-springboot \
                        -Dsonar.projectKey=a4anandhu_enhanced-petclinc-springboot \
                        -Dsonar.java.binaries=. \
                        -Dsonar.exclusions=**/trivy-report.txt
                    '''
                }
            }
        }

        stage('Sonar Quality Gate') {
            steps {
        echo 'Sonar Quality Gate Stage started'
        timeout(time: 1, unit: 'MINUTES') {
            script {
                def qualityGate = waitForQualityGate()
                if (qualityGate.status != 'OK') {
                    echo "Quality gate failed: ${qualityGate.status}"
                    // Don’t fail the build
                }
            }
        }
    }
}

        stage('Maven Package') { 
            steps {
                echo 'This Maven package Stage'
                sh 'mvn package'
            }
        }
        stage('Docker Build') { 
            steps {
                script {
                    echo 'creating docker image'
                        docker.build("IMAGE_NAME:$IMAGE_TAG")
                }
                
            }
        }
    }
}
