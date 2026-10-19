pipeline{
    agent any 
    stages{
        stage('lint'){
            agent{
                docker{
                    image 'python:3.12'
                }

            }
            environment{
                HOME = '/tmp'
            }
            steps{
                sh 'pip install --user ruff==0.17.1' 
                sh 'python -m ruff check .'
            }
        }
    }
}