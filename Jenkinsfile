pipeline {
    agent any
    tools{
        maven 'maven'
    }    
    stages {
        stage('Checkout From Git') { 
            steps {
                git branch: 'prod', url: 'https://github.com/a4anandhu/enhanced-petclinc-springboot.git'
            }
        }
        stage('Maven Compile') { 
            steps {
                echo 'This Maven Compile Stage'
                sh 'mvn compile'
            }
        }
        stage('Maven Test') { 
            steps {
                echo 'This Maven Test Stage'
                sh 'mvn test'
            }
        }
    }
}
